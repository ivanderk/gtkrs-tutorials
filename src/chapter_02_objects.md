# Boxes, Buttons, & Labels

The purpose of this section is to give an explanation of the objects that are about to used,
before demonstrating how they are used in practice in subsequent sections.

## GtkBox

A **GtkBox** is effectively a UI equivalent of vectors in Rust, and must be defined with an
**Orientation**, which defines whether elements should be aligned from left to right, or
top to bottom. For those who have experience with modern HTML5/CSS3 designs, a **GtkBox**
is equivalent to a flex box -- they expand to cover the full space, and widgets contained
within are expanded according to rules applied when packing the children.

### Creating Boxes

In the following example, a horizontal and vertical box will be created with 0 padding between
children contained within the box. Once you have created your box, you can assign widgets to
the box using the `pack_*` methods.

```rust
let padding_between_children = 0;
let horizontal_box = Box::new(Orientation::Horizontal, padding_between_children);
let vertical_box = Box::new(Orientation::Vertical, padding_between_children);
```

### Packing Boxes

You may notice that the `pack_*` methods take a lot of miscellanious parameters. The first
parameter should be a reference to the widget that you are adding to the container. The
second and third parameters define the expand a fill parameters respectively. The final
parameter then defines how many units of space should be between children in the box.

> To further elaborate on the expand and fill parameters, expand defines whether the
> given widget should attempt to use all of the extra space that it can. Each widget that has
> the expand parameter set will equally share that extra space. Meanwhile, fill defines whether
> the extra spaced should actually have that widget fill to cover that extra space, or should
> merely use that extra space as padding.

```rust
health_info.pack_start(&health_label, false, false, 5);
health_info.pack_start(&health, true, true, 5);
```

## GtkLabel

A **GtkLabel** is simply a widget that consists solely of text. It's fairly self-explanatory
as a result. All you truly need to memorize is how to create a label, and change a label.

```rust
let information_label = Label::new("Specific Information: ");
let value = Label::new("Linux");
value.set_label("Redox");

let horizontal_box = Box::new(Orientation::Horizontal, 5);
horizontal_box.pack_start(&information_label, false, false, 0);
horizontal_box.pack_start(&value, true, false, 0);
```

## GtkButton

### Creating Buttons

A **GtkButton** is a simple button that contains a text label, and/or an image to represent
the action that is to be performed upon clicking that button.

```rust
let text_button = Button::new_with_label("Ok");
let image_button = Button::new_from_icon_name("icon-name", 32);
```

### Styling Buttons

Widgets within GTK can be styled so that they stand out from other widgets in the UI. Buttons
in particularl support two style classes: destructive-action, and suggested-action. If you have
a critical button that needs to stand out in the UI, you can set them like so:

```rust
// Add the corresponding style classes to those buttons.
delete_button.get_style_context().map(|c| c.add_class("destructive-action"));
new_button.get_style_context().map(|c| c.add_class("suggested-action"));
```

Each **GtkWidget** provides a **get_style_context()** method, which returns an
**Option<StyleContext>**, which thereby provides an **add_class()** method, which is then used
to set style classes. Got it? Good. The most important classes to know for buttons are the
`destructive-action` and `suggested-action` buttons. Typically, a destructive action sets the
button to a red color, while the suggested action uses a blue color. The actual color will depend
upon which GTK theme that the user is using, though.