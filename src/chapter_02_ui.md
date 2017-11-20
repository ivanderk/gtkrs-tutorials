# Creating the UI Structure

Using the previous chapter's structure as a template, we can expand that to include the new UI
elements that we are going to use within our program. It is important to note that you only
need to store elements that you are going to later program after the UI is constructed.

In this program, we are going to add two **GtkButtons** to the header bar, along with using
a vertical and horizontal **GtkBox** with some labels to display information about our
application's state. The following chart is the new diagram of our structure.

<img src="images/ch02_diagram.png" />

Which translates to the following Rust data structures:

```rust
pub struct App {
    pub window:  Window,
    pub header:  Header,
    pub content: Content,
}

pub struct Header {
    pub container: HeaderBar,
    pub hit:       Button,
    pub heal:      Button,
}

pub struct Content {
    pub container: Box,
    pub health:    Label,
    pub message:   Label,
}
```

## Creating the App Structure

Starting with our **App** structure, we will do as the last tutorial, but our **new()** metheod
shall take a **&HealthComponent** as an input to set the initial value in the UI, later on
down the road within our **Content** structure. One will note that we have added a new
**content** variable of type **Context**, which takes that health reference.

```rust
impl App {
    fn new(health: &HealthComponent) -> App {
        // Create a new top level window.
        let window = Window::new(WindowType::Toplevel);
        // Create a the headerbar and it's associated content.
        let header = Header::new();
        // Contains the content within the window.
        let content = Content::new(health);

        // Set the headerbar as the title bar widget.
        window.set_titlebar(&header.container);
        // Set the title of the window.
        window.set_title("App Name");
        // Set the window manager class.
        window.set_wmclass("app-name", "App name");
        // The icon the app will display.
        Window::set_default_icon_name("iconname");
        // Add the content box into the window.
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

## Creating the Header

Then we shall also implement the same method for our header, which shall now contain two
**GtkButtons** -- the hit and heal buttons. Also take note that we are assigning some style
classes to these buttons, to give them a more informative visual flair.

```rust
impl Header {
    fn new() -> Header {
        // Creates the main header bar container widget.
        let container = HeaderBar::new();

        // Sets the text to display in the title section of the header bar.
        container.set_title("App Name");
        // Enable the window controls within this headerbar.
        container.set_show_close_button(true);

        // Create the hit and heal buttons.
        let hit = Button::new_with_label("Hit");
        let heal = Button::new_with_label("Heal");

        // Add the corresponding style classes to those buttons.
        hit.get_style_context().map(|c| c.add_class("destructive-action"));
        heal.get_style_context().map(|c| c.add_class("suggested-action"));

        // THen add them to the header bar.
        container.pack_start(&hit);
        container.pack_end(&heal);

        // Returns the header and all of it's state
        Header { container, hit, heal }
    }
}
```

## Creating the Content

Now it's time to create the content for our window. You will almost reach for **GtkBoxes** when
constructing your UI, creating your interface with a tree-like diagram. These boxes, when initialized,
must be specified as either **Horizontal** or **Vertical** orientations.

You will amost certainly reach for **GtkBoxes**
for configuring your UI. These can be created with either a **Horizontal** or **Vertical**
alignment. These boxes are where you will add all of your widgets, where they will be stacked
according to the alignment of the box they are attached to.

We shall create a vertical box that will contain two children: a vertical **GtkBox** that contains
a label and a value, followed by a simple **GtkLabel** underneath.

```rust
impl Content {
    fn new(health: &HealthComponent) -> Content {
        // Create a vertical box to store all of it's inner children vertically.
        let container = Box::new(Orientation::Vertical, 0);

        // The health info will be contained within a horizontal box within the vertical box.
        let health_info = Box::new(Orientation::Horizontal, 0);
        let health_label = Label::new("Current Health:");
        let health = Label::new(health.get_health().to_string().as_str());

        // Set the horizontal alignments of each of our objects.
        health_info.set_halign(Align::Center);
        health_label.set_halign(Align::Start);
        health.set_halign(Align::Start);


        // Add the health info box's children
        health_info.pack_start(&health_label, false, false, 5);
        health_info.pack_start(&health, true, true, 5);

        // Create a message label that will later be modified by the application, upon
        // performing a hit or heal action.
        let message = Label::new("Hello");

        // Add everything to our vertical box
        container.pack_start(&health_info, true, false, 0);
        container.pack_start(&Separator::new(Orientation::Horizontal), false, false, 0);
        container.pack_start(&message, true, false, 0);

        Content { container, health, message }
    }
}
```

### Set Alignments

You may have noticed that the above code is setting horizontal alignments. Widgets can optionally
be supplied an alignment enum to their `set_halign()` and `set_valign()` methods, which, as you
would guess, modifies the alignment of the child within the UI.

```rust
// Set the horizontal alignments of each of our objects.
health_info.set_halign(Align::Center);
health_label.set_halign(Align::Start);
health.set_halign(Align::Start);
```