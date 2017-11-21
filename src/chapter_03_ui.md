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