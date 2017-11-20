# Conclusion & Review

If you click on the **Hit** button, the counter should decrement and the message should change.
Clicking on the **Heal** button should increment the counter and also change the message.After
running your program with `cargo run`, you should have a window that looks like so:

<img src="images/ch02_complete.png">

At this point, you should have a decent understanding of how **GtkBox**, **GtkButton**, and
**GtkLabel** works. It may be a good idea to revisit the previons
[GTK Objects Covered](./chapter_02_objects.md) section to review the specific details regarding
them.

## Practice Challenges

### Setting Inputs w/ Buttons

There isn't much that you can do with just buttons and labels. If you want a practice challenge,
try creating a program that displays a simple random math problem, and asks the user to use
buttons to set the value. If they get it correct, modify a label to tell the user that what they
entered was correct. This is an incredibly annoying interface design, so don't do this in the real
world!

### Bonus: Timed Answers

Do the same as the above, but also take advantage of
[gtk::timeout_add()](https://docs.rs/gtk/0.2.0/gtk/fn.timeout_add.html) to decrement
and update a timer label within the UI until the timer reaches zero.