# Horrorshow HTML Templating

Although not related to GTK development, the [horrorshow crate](docs.rs/horrorshow/) provides
very useful macro-based HTML templating capabilities, which allows you to efficiently generate
an HTML string in memory using a DSL (domain-specific language) combined with Rust, which can
be invoked through usage of the `@` sigil.

```rust
#[macro_use]
extern crate horrorshow;
use horrorshow::helper::doctype;

let title = "Title";
let content = "A string\nwith multiple\n\nlines";
let html_string = format!(
    "{}",
    html!{
        : doctype::HTML,
        html {
            head {
                style { : "#style { }" }
            }
            body {
                h1(id="style") { : title }
                @ for line in content.lines().filter(|x| !x.is_empty()) {
                    p { : line }
                }
            }
        }
    }
);
```