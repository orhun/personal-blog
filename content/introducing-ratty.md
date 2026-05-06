+++
title = "Introducing Ratty: A reimagined terminal emulator"
date = 2026-05-15

[taxonomies]
categories = ["Projects"]
+++

I built a terminal emulator that breaks the rules.

<!-- more -->

<video controls width="80%">
  <source src="ratty-demo-with-audio.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

<i>TL;DR: [Ratty](https://github.com/orhun/ratty) is a GPU-rendered terminal emulator that supports inline 3D graphics.<br>
It is inspired by TempleOS and built with Rust and Ratatui.<br>
</i>

TOC

---

## **Background**

If you have ever tried out [TempleOS](https://en.wikipedia.org/wiki/TempleOS) or seen videos of it, you probably know that the whole operating system feels like a fever dream or a psychedelic trip.

<video controls width="70%">
  <source src="templeos-menu.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

When I first got introduced to it by [this documentary](https://www.youtube.com/watch?v=UCgoxQCf5Jg&vl=en), I was shocked and impressed by the flashy colors, graphical sprites and uncomprehensible UI. There are so many things that makes it so unique, weird and fascinating at the same time, somehow.  
<small>(See a [A Constructive Look At TempleOS](http://www.codersnotes.com/notes/a-constructive-look-at-templeos/) if you want to read more about it.)</small>

But most importantly, I was really impressed by the fact that [Terry Davis](https://en.wikipedia.org/wiki/Terry_A._Davis) (RIP) designed the command line to handle sprites (graphical images) as first-class, insertable document entries (powered by custom [DolDoc](https://tinkeros.github.io/WbTempleOS/Doc/DolDocOverview.html) format), rather than just text. So you can put images, 3D meshes and even macros (clickable links) directly into the command line. This was intriguing!

<q>Well, what's the upside of that?</q>

Basically, the command line becomes the direct interface for everything. You can write code, interact with system and render graphics all in the same place, which why TempleOS feels so unusual compared to conventional operating systems.

In [one of the livestreams](https://youtu.be/urO4RTEsPBg?si=sQh2WZktOUoXmd5y&t=1040) where Terry was demonstrating the features of TempleOS, he first draws a 3D mesh and then directly preview it in the command line:

<video controls width="60%">
  <source src="templeos-commandline.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

Then he proceeds to import the sprite next the code and uses it there:

<video controls width="60%">
  <source src="templeos-editor.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

<q>So wait, in TempleOS a program can contain sprite data inline, so art assets for a game or demo can live in the same source file? Damn, Terry was really a genius.</q>

Yes, you can literally have a spinning 3D model of a rat as a comment in a source file. <small>(Foreshadowing?)</small>

In Terry's own words: <i>["f\*cking sprites, command-line... f\*cking... f\*ck yeah"](https://youtu.be/o48KzPa42_o?si=flrPpdM4oXjTur9F&t=17)</i>.

So, this whole <i>"sprites on the command-line"</i> idea was in the back of my mind for a while. I even [reported](https://github.com/raphamorim/rio/issues/346) to a modern terminal project, hoping that my good friend [Raphael Amorim](https://github.com/raphamorim/) would be interested. But so far, a terminal emulator like that didn't come up.

Until today.

---

## **Motivation**

When we launched [Terminal Tuesdays podcast](https://www.youtube.com/@TerminalCollectiveOrg), we needed a looping footage to play during the show. I wanted something visually pleasing to watch and something that fits our VT100 theme. We already had the rights to use [this 3D model](https://www.artstation.com/artwork/bK5yom), but I had no idea how to animate it. So I asked it on our [Grindhouse server](https://grindhouse.dev/) whether if anyone would have experience animating something like that. The next day, one of our talented members [gold-silver-copper](https://github.com/gold-silver-copper) came up with this amazing [rotating terminal animation](https://github.com/gold-silver-copper/rotating_terminal):

<video controls width="80%">
  <source src="rotating-terminal-loop.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

What really blew my mind was that the VT100's screen isn't a baked animation at all, it's a live terminal UI rendered by Ratatui. Rust code renders the 2D terminal output with [soft_ratatui](https://github.com/gold-silver-copper/soft_ratatui), composites it into the model's screen texture and Bevy displays that texture on the 3D terminal as the camera animates around it. In other words, it's a 2D terminal renderer feeding a 3D animation pipeline in real time.

<img src="rotating-terminal-diagram.png" width="70%"/>

The possibility of rendering TUIs in 3D space got me really excited. Then for the next two consecutive weekends, I hacked on Bevy and Ratatui integrations at our [Mercimek hackerspace](https://mercimek.space/) meetups. At first I really didn't know what I was building, so I just threw the [rotating_terminal](https://github.com/gold-silver-copper/rotating_terminal) repository to Codex and asked it to make things for me. At some point I even vibe coded a really broken game on [a livestream](https://www.youtube.com/watch?v=RTg6uL_j9Sc), which was fun:

<img src="cursed-rat-game.gif" width="70%"/>

That rectangle in the background was rendered with Ratatui, which made one thing apparent for me: the idea had a potential and I could weigh in on it to build something proper.

Coincidentally while these events were unfolding, [Raphael Amorim](https://github.com/raphamorim/) came up with a new terminal protocol called [Glyph Protocol](https://rapha.land/introducing-glyph-protocol-for-terminals/) which allows terminals to own font data and render glyphs as first-class objects. It also had [an example with Ratatui](https://github.com/raphamorim/glyph-protocol-examples) which showed how to render a TUI with custom fonts and glyphs. This was inspiring.

<img src="glyph-protocol-examples.png" width="60%"/>

And that's when it clicked. What I really wanted was not rendering 2D graphics in a 3D space, but the opposite. Basically I wanted to render 3D graphics in a 2D terminal and just follow what [Terry Davis did with TempleOS](#background). Reading the glyph protocol also gave me confidence that I could also design my own [terminal protocol](#ratty-graphics-protocol) similar to it.

And so, <g>Ratty</g> was born.

---

<img width="350" src="https://raw.githubusercontent.com/orhun/ratty/refs/heads/main/assets/img/ratty-logo.gif" />

<br>

## **Ratty, a new 3D terminal!**

In [Ratty](https://github.com/orhun/ratty),

- your terminal cursor is a spinning rat,
- your whole terminal is a 3D canvas,
- you can insert 3D models and sprites into the terminal.

<video controls width="80%">
  <source src="ratty-3d-with-audio.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

<q> Man, that's crazy, the fact you can pull the terminal like that!<br>Makes sense since it's all in 3D but still...</q>

Yes! Also check out the [3D drawing demo](https://github.com/orhun/ratty/blob/main/widget/examples/draw.rs):

<video controls width="80%">
  <source src="ratty-draw-with-audio.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

That's not it. Remember the documents in TempleOS with inline sprites?  
[Here it is](https://github.com/orhun/ratty/blob/main/widget/examples/document.rs), but in Ratty:

<video controls width="80%">
  <source src="ratty-document-with-audio.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

Ratty also supports the [Kitty Image Protocol](https://sw.kovidgoyal.net/kitty/graphics-protocol/) (how ironic!) so you can render images (like the TempleOS logo above).

<q>How the heck all of this works under the hood?</q>

### **Implementation**

Let's break down how the terminal output gets rendered on the screen in three stages:

<div style="display:flex; gap:24px; align-items:flex-start;">

<div style="width:50%;">

1. The terminal application or shell runs inside a PTY (via [portable-pty](https://docs.rs/portable-pty) crate) and terminal escape sequences are parsed (via [vt100](https://docs.rs/vt100) crate) to keep track of the terminal screen state.

2. [Ratatui](https://ratatui.rs) creates a terminal buffer from that screen state and renders it into a texture on the GPU (via [parley_ratatui](https://crates.io/crates/parley_ratatui) crate).
   Under the hood, [parley](https://docs.rs/parley) is used for text shaping such as font management and [Vello](https://docs.rs/vello) is used for GPU rendering.

3. [Bevy](https://bevyengine.org) takes that texture and renders it in a 3D scene, where you can have cameras, lighting, 3D models and animations.

Ratty separates terminal emulation from presentation: one side handles PTY I/O and terminal parsing, while the other turns the result into a GPU-rendered 2D or 3D scene.<br>
This allows for a lot of flexibility in how the terminal output is displayed (e.g. you can warp the whole damn thing).

</div>

<div style="width:40%;">
  <img src="ratty-rendering-diagram.png" width="100%"/>
</div>

</div>

As a concrete example of this rendering pipeline, let's talk about how the "spinning rat cursor" works.

<img src="ratty-cursor.gif" width="70%"/>

On the terminal side managed by Ratatui, the cursor is just another cell location.<br>
It is first queried and then rendered as a blank character:

```rust
use ratatui::buffer::Buffer;
use ratatui::layout::Rect;

let area = Rect::new(0, 0, cols, rows);
let mut buffer = Buffer::empty(area);

let cursor_row: u16 = 42;
let cursor_col: u16 = 69;

buffer[(cursor_col, cursor_row)].set_char(' ');
```

Separately, Bevy owns a cursor entity (e.g. 3D model of a rat):

```rust
commands.spawn((
    CursorModel,
    Mesh3d(mesh_handle),
    MeshMaterial3d(material_handle),
    Transform::from_xyz(0.0, 0.0, 10.0),
));
```

Once you know the terminal size and the viewport size, you can map a cell into scene space:

```rust
let cell_width = viewport_size.x / cols as f32;
let cell_height = viewport_size.y / rows as f32;

let x = -viewport_size.x * 0.5 + (cursor_col as f32 + 0.5) * cell_width;
let y = viewport_size.y * 0.5 - (cursor_row as f32 + 0.5) * cell_height;
```

And then it is a matter of animating it like any other Bevy object:

```rust
let spin = elapsed_secs * 3.0;
let bob = (elapsed_secs * 5.0).sin() * cell_height * 0.15;

transform.translation = Vec3::new(x, y + bob, 10.0);
transform.rotation =
    Quat::from_rotation_y(spin) * Quat::from_rotation_x(-0.25);
transform.scale = Vec3::splat(cell_width.min(cell_height));
```

That is the whole trick: the cursor is just a normal scene object that gets its position from the terminal state and then it can do whatever it wants in the 3D world.

For example, you can [configure the cursor](https://github.com/orhun/ratty#changing-the-cursor) to be your dog:

<div style="display:flex; gap:24px; align-items:flex-start;">
<div style="width:50%;">

  <img src="pasha.png" width="100%"/>

</div>

<div style="width:70%;">
<br>
  <img src="pasha-term.gif" width="100%"/>
</div>

</div>

And it's just a matter of tweaking some parameters in the config file:

```toml
[cursor.model]
path = "pug.obj"
color = "#000000"
scale_factor = 6.0
brightness = 0.0
x_offset = 0.5
plane_offset = 18.0
visible = true

[cursor.animation]
spin_speed = 2
bob_speed = 40
bob_amplitude = 0.1
```

<q>That is so cute! But how about rendering arbitrary 3D objects?</q>

That's where the graphics protocol comes in.

### **Ratty Graphics Protocol**

The underlying idea behind Ratty is that terminal can be a richer graphical interface rather than a text-only environment. But unlike TempleOS, the terminal still should be usable in today's world and should support the tools that we already use. In other words, Ratty is trying to coexist with the modern terminal ecosystem rather than replacing it.

This means that we're still dependent on legacy terminal stack (ANSI escape codes, VT100 control sequences, etc.), but we can design a new terminal protocol on top of it to extend the terminal's capabilities. That's where the [**Ratty Graphics Protocol (RGP)**](https://github.com/orhun/ratty/blob/main/protocols/graphics.md) comes in.

The core idea is simple:

- register a 3D asset,
- place it in terminal cell space (anchored to a row and column),
- let Ratty render it as part of the scene.

Below you can see the anchoring of a 3D model to a terminal cell in action:

<img src="ratty-document.gif" width="60%"/>

The <span style="color:#000; background:#fff; font-size: 1.3em;"> @</span> cell above indicates the anchor point of the 3D model inserted in the terminal. When the anchor cell moves, the 3D model moves with it.

<q>How do you communicate that to the terminal?</q>

RGP messages are carried over APC, the [Application Program Command](https://en.wikipedia.org/wiki/C0_and_C1_control_codes#C1_controls) control sequence:

```text
ESC _ ratty ; g ; <verb> [ ; <key=value> ... ] ESC \
      │       │    │             └── optional semicolon-separated fields
      │       │    └──────────────── verb / operation
      │       └───────────────────── graphics namespace
      └───────────────────────────── protocol namespace
```

RGP currently has four operations:

- `s` for support query
- `r` for registering an object asset
- `p` for placing an object into terminal cell space
- `d` for deleting an object

For example, to support if the terminal supports RGP, an application can send:

```text
ESC _ ratty;g;s ESC \
```

And if the terminal supports it, it can respond with:

```text
ESC _ ratty;g;s;v=1;fmt=obj|glb;path=1;anim=1;depth=1;color=1;brightness=1 ESC \
```

To register a 3D model and animate it (with certain color and attributes):

```text
ESC _ ratty;g;r;id=7;fmt=obj;path=CairoSpinyMouse.obj ESC \
```

```text
ESC _ ratty;g;p;id=7;row=5;col=10;w=3;h=2;animate=1;scale=1.0;depth=1.5;color=7fd0ff;brightness=1.0 ESC \
```

Here is a demo that places a [big rat](https://github.com/orhun/ratty/blob/main/widget/examples/big_rat.rs) in the terminal and tweaks some of its parameters:

<video controls width="80%">
  <source src="ratty-big-rat-with-audio.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

Of course, the protocol is still a work in progress ([in this document](https://github.com/orhun/ratty/blob/main/protocols/graphics.md)) and there might be things that need to be changed. But the general idea is to have a simple and extensible protocol that applications can use to leverage Ratty's graphics capabilities without needing to know about the underlying rendering pipeline.

<i>Speaking of...</i>

### Building applications

Today, a Ratatui widget called [`ratatui-ratty`](https://crates.io/crates/ratatui-ratty) exists for building applications on top of RGP.

To use it, just add the dependency to your `Cargo.toml`:

```
cargo add ratatui-ratty
```

At the API level, a graphic is described by a small settings struct:

```rust
use ratatui_ratty::{RattyGraphic, RattyGraphicSettings};

let graphic = RattyGraphic::new(
    RattyGraphicSettings::new("CairoSpinyMouse.obj")
        .id(7)
        .animate(true)
        .scale(1.0)
        .depth(1.5)
        .color([0x7f, 0xd0, 0xff])
        .brightness(1.0),
);
```

Then all you need to do is register the asset with Ratty and place it into a Ratatui region:

```rust
use ratatui_core::{buffer::Buffer, layout::Rect, widgets::Widget};

graphic.register()?;

let mut buf = Buffer::empty(Rect::new(0, 0, 80, 24));
(&graphic).render(Rect::new(10, 5, 24, 10), &mut buf);
```

> To reiterate, the widget does not directly draw an object into the terminal buffer. Instead, it writes the appropriate RGP messages to stdout to register and place the object, and then Ratty takes care of rendering it in the scene.

So if you have an application that already uses Ratatui, you can just add `ratatui-ratty` as a dependency and start rendering spinning rats, octopuses or whatever you want!<br>
See the [widget examples here](https://github.com/orhun/ratty/tree/main/widget/examples)!

## FAQ

AI?

<q>How feasible do you think it will be for other terminals to implement?</q>

Not feasible at all. The whole graphics protocol is designed around Ratty's architecture and rendering pipeline, so supporting that in a traditional terminal emulator would be really hard. Some possibilities are 1) they can either half way support the protocol where the graphics are rendered as ASCII-based 3D models or 2) this would be inspiring enough that we start re-thinking what terminal might be and it paves the way for other protocols (which is a more realistic goal to have).

questions: 600 deps, backwards?

600 deps for a terminal emulator?
game engine for a terminal emulator?
memory usage?

> A terminal is supposed to be a canvas. If the canvas cannot render what the application asks it to, the canvas is incomplete.

## Wrapping up

All I wanted was to build a terminal emulator with a spinning rat as a cursor.

Making a terminal emulator is difficult.

> If i restricted my coding projects to useful things, I would probably be a farmer by now
> [11:37 AM]Coko [NVIM], : these kinds of experiments are where creativity is born

Exporting TempleOS artifacts

I'm just trying to make terminal more powerful and fun to use.

If you want to see terminals move forward, support these new terminal protocols such as the Glyph protocol and respective projects. And let me know if Ratty is worth exploring further.

I'm obsessed with terminals.

Mention EuroRust talk
