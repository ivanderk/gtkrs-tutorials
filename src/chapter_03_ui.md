# Creating the UI Structure

<img src="images/ch03_complete.png" />

Using the first chapter as a template and expanding upon that, we shall design our UI according
to the vision of our UI, pictured above. The key widgets to take note of are the **post** button
in the header bar; and within our content window is a **title** entry, **tags** entry,
**content** text view, and a **right_pane** text view for displaying the output in HTML. As such,
our application's UI structure should look as so:

```rust
pub struct App {
    pub window: Window,
    pub header: Header,
    pub content: Content,
}

pub struct Header {
    pub container: HeaderBar,
    pub post: Button
}

pub struct Content {
    pub container: Paned,
    pub title: Entry,
    pub tags: Entry,
    pub content: TextBuffer,
    pub right_pane: TextBuffer,
}
```

Note that the container for our content will not be a **GtkBox**, but a **GtkPaned**. This will
enable the user to drag the divider between the panels to resize them as needed. In addition,
the **content** and **right_pane** fields are stored as **GtkTextBuffers**, instead of
**GtkTextViews**. This is because we are not going to be programming the views -- just accessing
the underlying text buffers that are associated with the views.

## App Implementation

Something new that we will do here is to define the default size of the window, as we should
have a reasonable size for the user to interact with by default, without needing to resize
the window to get a good look at the contents within. We are also setting the title of this
application as 'HTML Articler'. Beyond that, this should be very similar for every application
you develop.

```rust
impl App {
    fn new() -> App {
        // Create a new top level window.
        let window = Window::new(WindowType::Toplevel);
        // Create a the headerbar and it's associated content.
        let header = Header::new();
        // Create the main content.
        let content = Content::new();

        // Set the headerbar as the title bar widget.
        window.set_titlebar(&header.container);
        // Set the title of the window.
        window.set_title("HTML Articler");
        // Set the window manager class.
        window.set_wmclass("html-articler", "HTML Articler");
        // The icon the app will display.
        Window::set_default_icon_name("iconname");
        // Set the default size of the window.
        window.set_default_size(800, 600);
        // Add the content to the window.
        window.add(&content.container);

        // Programs what to do when the exit button is used.
        window.connect_delete_event(move |_, _| {
            main_quit();
            Inhibit(false)
        });

        // Return our main application state
        App { window, header, content }
    }
}
```

## Header Implementation

Our header bar will simply have a button with a "Post" label, which is given a 'suggested-action'
as a style class, and packed at the end of the bar. The title of the bar should also be set to
the name of our application.

```rust
impl Header {
    fn new() -> Header {
        // Creates the main header bar container widget.
        let container = HeaderBar::new();

        // Sets the text to display in the title section of the header bar.
        container.set_title("HTML Articler");
        // Enable the window controls within this headerbar.
        container.set_show_close_button(true);

        // Create a button that will post the HTML article.
        let post = Button::new_with_label("Post");
        post.get_style_context().map(|x| x.add_class("suggested-action"));

        container.pack_end(&post);

        // Returns the header and all of it's state
        Header { container, post }
    }
}
```

## Content Implementation

This is where we will spend most of our time for this application. First we create a
**GtkPaned** container that will hold our resizeable left and right panes. Our right pane
will be a **GtkTextView**, whereas the left pane is a vertical **GtkBox**. Note that we
are interested in getting direct access to that text view's buffer, so we will also
initialize that before creating the view. The left pane's box will have a padding of `5`
between children, just so that they aren't smushed together.

```rust
// The main container will hold a left and right pane. The left pane is for user input,
// whereas the right pane is for the generated output.
let container = Paned::new(Orientation::Horizontal);
let left_pane = Box::new(Orientation::Vertical, 5);
let right_pane = TextBuffer::new(None);
let right_pane_view = TextView::new_with_buffer(&right_pane);
```

Then comes creating the **title** and **tags** entries, as well as the **content** view that
we will use to construct the left pane.

```rust
// The left pane will consist of a title entry, tags entry, and content text view.
let title = Entry::new();
let tags = Entry::new();
let content = TextBuffer::new(None);
let content_view = TextView::new_with_buffer(&content);
```

Note that we will also be storing a centered label above the **content** text view, followed
by adding some placeholder text within the entries, and tooltips to display when a mouse is
hovering over the entries.

```rust
// The label that we will display above the content box to describe it.
let content_label = Label::new("Content");
content_label.set_halign(Align::Center);

// Set placeholders within the entries to hint the user of the contents to enter.
title.set_placeholder_text("Insert Title");
tags.set_placeholder_text("Insert Colon-Delimited Tags");

// Additionally set tooltips on the entries. Note that you may use either text or markup.
title.set_tooltip_text("Insert the title of article here");
tags.set_tooltip_markup("<b>tag_one</b>:<b>tag two</b>:<b> tag three</b>");
```

Then ensuring that the right pane's text view is not editable, and both of the text views
have their text wrapped by words.

```rust
// The right pane should disallow editing; and both editors should wrap by word.
right_pane_view.set_editable(false);
right_pane_view.set_wrap_mode(WrapMode::Word);
content_view.set_wrap_mode(WrapMode::Word);
```

Now we just need to wrap the text views within some **GtkScrolledWindows** to enable the user
to scroll through the text, in the event that there is enough text that it cannot be displayed
all at once within the widget.

```rust
// Wrap the text views within scrolled windows, so that they can scroll.
let content_scroller = ScrolledWindow::new(None, None);
let right_pane_scrolled = ScrolledWindow::new(None, None);
content_scroller.add(&content_view);
right_pane_scrolled.add(&right_pane_view);
```

And to make our UI look better, we can apply some borders and margins accordingly.

```rust
// Paddin' Widgets
left_pane.set_border_width(5);
right_pane_view.set_left_margin(5);
right_pane_view.set_right_margin(5);
right_pane_view.set_top_margin(5);
right_pane_view.set_bottom_margin(5);
content_view.set_left_margin(5);
content_view.set_right_margin(5);
content_view.set_top_margin(5);
content_view.set_bottom_margin(5);
```

And all that remains is to pack our widgets into their panes and return a **Content** structure.

```rust
// First add everything to the left pane box.
left_pane.pack_start(&title, false, true, 0);
left_pane.pack_start(&tags, false, true, 0);
left_pane.pack_start(&content_label, false, false, 0);
left_pane.pack_start(&content_scroller, true, true, 0);

// Then add the left and right panes into the container
container.pack1(&left_pane, true, true);
container.pack2(&right_pane_scrolled, true, true);

Content { container, title, tags, content, right_pane }
```

Put it all together, and you should have an implementation that looks as follows:

```rust
impl Content {
    fn new() -> Content {
        // The main container will hold a left and right pane. The left pane is for user input,
        // whereas the right pane is for the generated output.
        let container = Paned::new(Orientation::Horizontal);
        let left_pane = Box::new(Orientation::Vertical, 5);
        let right_pane = TextBuffer::new(None);
        let right_pane_view = TextView::new_with_buffer(&right_pane);

        // The left pane will consist of a title entry, tags entry, and content text view.
        let title = Entry::new();
        let tags = Entry::new();
        let content = TextBuffer::new(None);
        let content_view = TextView::new_with_buffer(&content);

        // The label that we will display above the content box to describe it.
        let content_label = Label::new("Content");
        content_label.set_halign(Align::Center);

        // Set placeholders within the entries to hint the user of the contents to enter.
        title.set_placeholder_text("Insert Title");
        tags.set_placeholder_text("Insert Colon-Delimited Tags");

        // Additionally set tooltips on the entries. Note that you may use either text or markup.
        title.set_tooltip_text("Insert the title of article here");
        tags.set_tooltip_markup("<b>tag_one</b>:<b>tag two</b>:<b> tag three</b>");

        // The right pane should disallow editing; and both editors should wrap by word.
        right_pane_view.set_editable(false);
        right_pane_view.set_wrap_mode(WrapMode::Word);
        content_view.set_wrap_mode(WrapMode::Word);

        // Wrap the text views within scrolled windows, so that they can scroll.
        let content_scroller = ScrolledWindow::new(None, None);
        let right_pane_scrolled = ScrolledWindow::new(None, None);
        content_scroller.add(&content_view);
        right_pane_scrolled.add(&right_pane_view);

        // Paddin' Widgets
        left_pane.set_border_width(5);
        right_pane_view.set_left_margin(5);
        right_pane_view.set_right_margin(5);
        right_pane_view.set_top_margin(5);
        right_pane_view.set_bottom_margin(5);
        content_view.set_left_margin(5);
        content_view.set_right_margin(5);
        content_view.set_top_margin(5);
        content_view.set_bottom_margin(5);

        // First add everything to the left pane box.
        left_pane.pack_start(&title, false, true, 0);
        left_pane.pack_start(&tags, false, true, 0);
        left_pane.pack_start(&content_label, false, false, 0);
        left_pane.pack_start(&content_scroller, true, true, 0);

        // Then add the left and right panes into the container
        container.pack1(&left_pane, true, true);
        container.pack2(&right_pane_scrolled, true, true);

        Content { container, title, tags, content, right_pane }
    }
}
```