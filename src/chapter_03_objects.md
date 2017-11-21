# Entries, Panes, Scrolled Windows, & Text Views

## GtkPaned

These are containers that may be oriented either vertically or horizontally, and feature two
resizeable children. These children are resized merely by clicking and dragging the divider
between them.

```rust
let container = Paned::new(Orientation::Horizontal);
let left_widget = ...;
let right_widget = ...;
container.pack1(&left_widget, true, true);
container.pack2(&right_widget, true, true);
```

Within the pack methods above, the two `true` values designate whether the child can shrink,
and whether the child can be resized.

## GtkEntry

Entries enable the UI to accept a line of text as input, which can be utilized by other widgets
to perform certain actions using that text as the input.

```rust
let entry = Entry::new();
entry.set_text("Some Text");
if let Some(text) = entry.get_text() {
    println!("{}", text);
}
```

## GtkTextView

Text views serve two purposes: the ability to display multiple lines of text within a text box,
and the ability to input multiple lines of text. These may be configured to be non-editable,
if editing a view is not desired. They also provide methods for controlling the word-wrapping
behavior. They aren't capable of handling formatted text, however, or useful as a code editor.
If you want text to be rendered in HTML, see **GtkWebView**; and if you want a code editor, see
**GtkSourceView**.

> Note that it is often times best to create and assign the **GtkTextBuffer** to your text view
> manually, in order to get a handle to that buffer which you can store, and avoid some
> indirection when programming your UI, and to get direct access to the text within.

```rust
// The buffer for the text view, with `None` as the parameter because we are
// not going to define any text tags for this buffer.
let text_buffer = TextBuffer::new(None);
// Then we shall assign the buffer to a new text view, which will automatically
// update itself as text is added or removed from the buffer.
let text_view = TextView::new_with_buffer(&text_buffer);
```

Getting text from a **GtkTextBuffer** is a little tricky, so here's an abstraction which you
can use to specify to grab the entire range of the buffer and return it as a **String**. As it
turns out, you may specify a specific range of text to obtain from this buffer.

```rust
/// Obtain the entire text buffer's contents as a string.
fn get_buffer(buffer: &TextBuffer) -> Option<String> {
    let start = buffer.get_start_iter();
    let end = buffer.get_end_iter();
    buffer.get_text(&start, &end, true)
}
```

## GtkScrolledWindow

These are single-element boxes that feature a scrollable window within. It can be useful to combine
them with text views to enable text views to scroll. This is precisely what we are about to do
within this chapter.

```rust
let scrolled_window = ScrolledWindow::new(None, None);
scrolled_window.add(&text_view);
```