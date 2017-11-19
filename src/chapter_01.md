# Creating a Window w/ Header Bar

## Creating the GTK Application Structures

<img src="images/ch1_diagram.png" />

After you've planned out what your application will look like, you will start by constructing the
interface of your application, beginning with the window and an optional header bar. First
construct a structure that will hold the state of every widget in your interface that you will
later program.

```rust
extern crate gtk;
use gtk::*;
use std::process;

pub struct App {
    pub window: Window,
    pub header: Header,
}

pub struct Header {
    pub container: HeaderBar
}
```

Next is to generate our UI with Rust, and store it in the newly-defined structures. First is the
**App** structure, which will be contain the overall structure of our UI in a well-defined
hierarchy of data structures and associated data. The following code example provides in-line
comments to describe each of the methods that are being used to configure it.

```rust
impl App {
    fn new() -> App {
        // Create a new top level window.
        let window = Window::new(WindowType::Toplevel);
        // Create a the headerbar and it's associated content.
        let header = Header::new();

        // Set the headerbar as the title bar widget.
        window.set_titlebar(&header.container);
        // Set the title of the window.
        window.set_title("App Name");
        // Set the window manager class.
        window.set_wmclass("app-name", "App name");
        // The icon the app will display.
        Window::set_default_icon_name("iconname");

        // Programs what to do when the exit button is used.
        window.connect_delete_event(move |_, _| {
            main_quit();
            Inhibit(false)
        });

        // Return our main application state
        App { window, header }
    }
}

impl Header {
    fn new() -> Header {
        // Creates the main header bar container widget.
        let container = HeaderBar::new();

        // Sets the text to display in the title section of the header bar.
        container.set_title("App Name");
        // Enable the window controls within this headerbar.
        container.set_show_close_button(true);

        // Returns the header and all of it's state
        Header { container }
    }
}
```

## Initializing and Launching the Application

Now that we are ready, we simply need to initialize GTK, create our GTK application structure,
show all of the widgets within that structure, and start the GTK main event loop.

```rust
fn main() {
    // Initialize GTK before proceeding.
    if gtk::init().is_err() {
        eprintln!("failed to initialize GTK Application");
        process::exit(1);
    }

    // Initialize the UI's initial state
    let app = App::new();

    // Make all the widgets within the UI visible.
    app.window.show_all();

    // Start the GTK main event loop
    gtk::main();
}
```

Once the main thread has entered the event loop, it will poll across each of the widgets for
actions that have been triggered, such as the `connect_delete_event()` method that we used
above to program the exit button to exit the program.

## The Result

With all of this complete, you should now have a program up and running that looks like so.

<img src="images/headerbar.png" />