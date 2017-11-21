# HTML Articler

<img src="images/ch03_complete.png" />

In this chapter, you will begin to write useful software that takes advantage of text entries
and text views, to allow the user to enter data within various input fields, and to generate
an output from those inputs after clicking a button. In addition, you will also be exposed
to the '**html!**' macro within the [horrorshow crate](https://docs.rs/horrorshow/).
You will be writing a program that takes inputs on the left pane, and generates minified HTML
within the right text view.

> Note that there isn't any external state to worry about in this chapter, as there was in the
> last. All of the state that we are concerned with is contained within the GTK objects that
> we will be interacting with.