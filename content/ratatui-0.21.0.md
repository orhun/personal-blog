+++
title = "Ratatui: Build rich terminal user interfaces using Rust"
date = 2023-05-29

[taxonomies]
categories = ["Ratatui"]
+++

"[**Ratatui**](https://github.com/ratatui-org/ratatui)" came a long way since its transition from the original [`tui-rs`](https://github.com/fdehau/tui-rs) crate. In this post, let's take a look at what's new in the latest version.

<!-- more -->

<center>

![ratatui](/ratatui-logo.png)

</center>

### Retrospective

To catch you up to speed:

- `14-08-2022`: The original author of `tui-rs` created [an issue](https://github.com/fdehau/tui-rs/issues/654) for discussing the future of the crate.
- `02-02-2023`: I and a couple of people who are interested in the project created a Discord server to discuss the possibility of forking the project.
- `08-02-2023`: The author got back to us and proposed a plan for transferring the ownership to the new maintainers. This was the last time we heard from him about the project :/
- `14-02-2023`: We created a fork to continue development.
- `19-03-2023`: The first version of `ratatui` is released. ([`0.20.0`](https://github.com/ratatui-org/ratatui/releases/tag/v0.20.0))

Ever since then, a number of people joined us for development and crates started to switch to `ratatui`. And today, we are proud the announce the new version of the project!

Before everything, big thanks to [Florian Dehau](https://github.com/fdehau) for making this possible and creating `tui-rs` in the first place! 💖

Now, let's see what's new in `ratatui`!

**Full changelog**: [https://github.com/ratatui-org/ratatui/releases/tag/v0.21.0](https://github.com/ratatui-org/ratatui/releases/tag/v0.21.0)

---

### New backend: `termwiz`

`ratatui` supports a new backend called `termwiz` which is a "Terminal Wizardry" crate that powers [wezterm](https://github.com/wez/wezterm).

To use it, enable the `termwiz` feature in `Cargo.toml`:

```toml
[dependencies.ratatui]
version = "0.21.0"
features = ["termwiz"]
default-features = false
```

Then you can utilize `TermwizBackend` object for creating a terminal. Here is a simple program that shows a text on the screen for 5 seconds using `ratatui` + `termwiz`:

```rs
use ratatui::{backend::TermwizBackend, widgets::Paragraph, Terminal};
use std::{
    error::Error,
    thread,
    time::{Duration, Instant},
};

fn main() -> Result<(), Box<dyn Error>> {
    let backend = TermwizBackend::new()?;
    let mut terminal = Terminal::new(backend)?;
    terminal.hide_cursor()?;

    let now = Instant::now();
    while now.elapsed() < Duration::from_secs(5) {
        terminal.draw(|f| f.render_widget(Paragraph::new("termwiz example"), f.size()))?;
        thread::sleep(Duration::from_millis(250));
    }

    terminal.show_cursor()?;
    terminal.flush()?;
    Ok(())
}
```

---

### New widget: Calendar

A calendar widget has been added which was originally a part of the [extra-widgets](https://github.com/sophacles/extra-widgets) repository.

Since this new widget depends on `time` crate, we gated it behind `widget-calendar` feature to avoid an extra dependency:

```toml
[dependencies.ratatui]
version = "0.21.0"
features = ["widget-calendar"]
```

Here is the example usage:

```rs
Monthly::new(
    time::Date::from_calendar_date(2023, time::Month::January, 1).unwrap(),
    CalendarEventStore::default(),
)
.show_weekdays_header(Style::default())
.show_month_header(Style::default())
.show_surrounding(Style::default()),
```

Results in:

```sh
     January 2023
 Su Mo Tu We Th Fr Sa
  1  2  3  4  5  6  7
  8  9 10 11 12 13 14
 15 16 17 18 19 20 21
 22 23 24 25 26 27 28
 29 30 31  1  2  3  4
```

---

### New widget: Circle

`Circle` widget has been added with the use-case of showing an accuracy radius on the world map.

Here is an example of how to use it with `Canvas`:

```rs
Canvas::default()
    .paint(|ctx| {
        ctx.draw(&Circle {
            x: 5.0,
            y: 2.0,
            radius: 5.0,
            color: Color::Reset,
        });
    })
    .marker(Marker::Braille)
    .x_bounds([-10.0, 10.0])
    .y_bounds([-10.0, 10.0]),
```

Results in:

```sh
 ⡠⠤⢤⡀
⢸⡁  ⡧
 ⠑⠒⠚⠁
```

---

### Inline Viewport

This was a highly requested feature and the original implementation was done by [@fdehau](https://github.com/fdehau) himself. Folks at [Atuin](https://atuin.sh) completed the implementation and we are happy to finally have this incorporated in the new release!

An inline viewport refers to a rectangular section of the terminal window that is set aside for displaying content.

In the repository, there is an example that simulates downloading multiple files in parallel: [https://github.com/ratatui-org/ratatui/blob/main/examples/inline.rs](https://github.com/ratatui-org/ratatui/blob/main/examples/inline.rs)

---

### Block: title on bottom

> Before you could only put the title on the top row of a Block. Now you can put it on the bottom row! Revolutionary.

For example, place the title on the bottom and center:

```rs
Paragraph::new("ratatui")
    .alignment(Alignment::Center)
    .block(
        Block::default()
            .title(Span::styled("Title", Style::default()))
            .title_on_bottom()
            .title_alignment(Alignment::Center)
            .borders(Borders::ALL),
    )
```

Results in:

```sh
┌─────────────────────┐
│       ratatui       │
│                     │
└────────Title────────┘
```

---

### Block: support adding padding

If we want to render a widget inside a `Block` with a certain distance from its borders, we need to create another `Layout` element based on the outer `Block`, add a margin and render the `Widget` into it. Adding a padding property on the block element skips the creation of this second Layout.

This property works especially when rendering texts, as we can just create a block with padding and use it as the text wrapper:

```rs
let block = Block::default()
    .borders(Borders::ALL)
    .padding(Padding::new(1, 1, 2, 2));
let paragraph = Paragraph::new("example paragraph").block(block);
f.render_widget(paragraph, area);
```

Rendering another widget should be easy too, using the `.inner` method:

```rs
let block = Block::default().borders(Borders::ALL).padding(Padding {
    left: todo!(),
    right: todo!(),
    top: todo!(),
    bottom: todo!(),
});
let inner_block = Block::default().borders(Borders::ALL);
let inner_area = block.inner(area);

f.render_widget(block, area);
f.render_widget(inner_block, inner_area);
f.render_widget(paragraph, area);
```

---

### Text: display secure data

A new type called `Masked` is added for text-related types for masking data with a mask character. The example usage is as follows:

```rs
Line::from(vec![
    Span::raw("Masked text: "),
    Span::styled(
        Masked::new("password", '*'),
        Style::default().fg(Color::Red),
    ),
])
```

Results in:

```sh
Masked text: ********
```

---

### `border!` macro

A `border!` macro has been added that takes `TOP`, `BOTTOM`, `LEFT`, `RIGHT`, and `ALL` and returns a `Borders` object.

An empty `border!()` call returns `NONE`.

For example:

```rs
border!(ALL)
border!(LEFT, RIGHT)
border!()
```

This is gated behind a `macros` feature flag to ensure short build times. To enable it, update `Cargo.toml` as follows:

```toml
[dependencies.ratatui]
version = "0.21.0"
features = ["macros"]
```

Going forward, we will most likely put the new macros behind `macros` feature as well.

---

### Color: support conversion from `String`

Have you ever needed this conversion?

```rs
"black" => Color::Black,
"red" => Color::Red,
"green" => Color::Green,
// etc.
```

Don't worry, we got you covered:

```rs
Color::from_str("lightblue") // Color::LightBlue
Color::from_str("10")        // Color::Indexed(10)
Color::from_str("#FF0000")   // Color::Rgb(255, 0, 0)
```

---

### `Spans` -> `Line`

`Line` is a significantly better name over `Spans` as the plural causes confusion and the type really is a representation of a line of text made up of spans.

So, `Spans` is renamed as `Line` and a deprecation notice has been added.

See [https://github.com/ratatui-org/ratatui/pull/178](https://github.com/ratatui-org/ratatui/pull/178) for more discussion.

---

### Other features

- `List` now has a `len()` method for returning the number of items
- `Sparkline` now has a `direction()` method for specifying the render direction (left to right / right to left)
- `Table` and `List` states now have `offset()` and `offset_mut()` methods
- Expose the test buffer (`TestBackend`) with `Display` implementation

---

### New apps

Here is the list of applications that has been added:

- [oxycards](https://github.com/BrookJeynes/oxycards): quiz card application built within the terminal.
- [twitch-tui](https://github.com/Xithrius/twitch-tui): twitch chat in the terminal.
- [tenere](https://github.com/pythops/tenere): TUI interface for LLMs.

Also, we moved `APPS.md` file to the [Wiki](https://github.com/ratatui-org/ratatui/wiki/Apps-using-Ratatui) so check it out for more applications built with `ratatui`!

---

### Migration from `tui-rs`

We put together a migration guide at the Wiki: [Migrating from TUI](https://github.com/ratatui-org/ratatui/wiki/Migrating-from-TUI)

Also, the minimum supported Rust version is `1.65.0`

---

## Contributing

Any contribution is highly appreciated! There are [contribution guidelines](https://github.com/ratatui-org/ratatui/blob/main/CONTRIBUTING.md) for getting started.

Feel free to [submit issues](https://github.com/ratatui-org/ratatui/issues/new/choose) and throw in ideas!

If you are having a problem with `ratatui` or want to contribute to the project or just want to chit-chat, feel free to [join our Discord server](https://discord.gg/pMCEU9hNEj)!

---

## Acknowledgements

Shout out to these awesome people for their contribution to the project:

- [@joshka](https://github.com/joshka)
- [@sophacles](https://github.com/sophacles)
- [@sayanarijit](https://github.com/sayanarijit)
- [@mindoodoo](https://github.com/mindoodoo)
- [@rhysd](https://github.com/rhysd)
- [@a-kenji](https://github.com/a-kenji)
- [@karolinepauls](https://github.com/karolinepauls)
- [@TimonPost](https://github.com/TimonPost)
- [@abusch](https://github.com/abusch)
- [@defiori](https://github.com/defiori)
- [@dflemstr](https://github.com/dflemstr)
- [@stevensonmt](https://github.com/stevensonmt)
- [@TheLostLambda](https://github.com/TheLostLambda)
- [@cjbassi](https://github.com/cjbassi)
- [@sectore](https://github.com/sectore)
- [@JosephFKnight](https://github.com/JosephFKnight)
- [@man0lis](https://github.com/man0lis)
- [@wose](https://github.com/wose)
- [@svenstaro](https://github.com/svenstaro)
- [@scauligi](https://github.com/scauligi)
- [@scvalex](https://github.com/scvalex)
- [@eminence](https://github.com/eminence)
- [@imsnif](https://github.com/imsnif)
- [@ClementTsang](https://github.com/ClementTsang)
- [@deepu105](https://github.com/deepu105)
- [@fujiapple852](https://github.com/fujiapple852)
- [@jeffa5](https://github.com/jeffa5)
- [@z2oh](https://github.com/z2oh)

And lastly, special thanks to [@fdehau](https://github.com/fdehau) once again!

🥂
