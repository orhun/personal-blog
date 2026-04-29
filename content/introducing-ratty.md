+++
title = "Introducing Ratty: A reimagined terminal emulator"
date = 2026-04-29

[taxonomies]
categories = ["Thoughts"]
+++

I built a terminal emulator that isn't what a terminal emulator is supposed to be.

<!-- more -->

<i>TL;DR: [Ratty](https://github.com/orhun/ratty) is a terminal emulator that supports 3D objects.<br>It is inspired by TempleOS and built with Rust and Ratatui.</i>

![](main ratty demo)

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

So, this whole <i>"sprites on the command-line"</i> idea was in the back of my mind for a while. I even [reported](https://github.com/raphamorim/rio/issues/346) to a modern terminal project, hoping that my good friend [Rapha](https://github.com/raphamorim/) would be interested. But so far, a terminal emulator like that didn't come up.

Until today.

---

## **Motivation**

When we launched [Terminal Tuesdays podcast](https://www.youtube.com/@TerminalCollectiveOrg), we needed a looping footage to play during the show. I wanted something visually pleasing to watch and something that fits our VT100 theme. We already had the rights to use [this 3D model](https://www.artstation.com/artwork/bK5yom), but I had no idea how to animate it. So I asked it on our [Grindhouse server](https://grindhouse.dev/) whether if anyone would have experience animating something like that. The next day, one of our super grind friends [gold-silver-copper](https://github.com/gold-silver-copper) came up with this amazing animation:

> https://github.com/gold-silver-copper/rotating_terminal

![]()

What really blew my mind was that the VT100's screen isn't a baked animation at all, it's a live terminal UI rendered by Ratatui. Rust code renders the 2D terminal output with [soft_ratatui](https://github.com/gold-silver-copper/soft_ratatui), composites it into the model's screen texture and Bevy displays that texture on the 3D terminal as the camera animates around it. In other words, it's a 2D terminal renderer feeding a 3D animation pipeline in real time.

![pipeline]()

The possibility of rendering TUIs in 3D space got me really excited. Then for the next two consecutive weekends, I hacked on Bevy and Ratatui integrations at our [Mercimek hackerspace](https://mercimek.space/) meetups. At first I really didn't know what I was building, so I just threw the rotating_terminal repository to Codex and asked it to make things for me. At some point I even vibe coded a really broken game on a livestream, which was fun.

Coincidentally while these events were unfolding, [Rapha] came up with a new terminal protocol called [Glyph Protocol](https://rapha.land/introducing-glyph-protocol-for-terminals/) which allows terminals to own font data and render glyphs as first-class objects. It also had an example with Ratatui which showed how to render a TUI with custom fonts and glyphs. This was inspiring.

And that's when it clicked. What I really wanted was not rendering 2D graphics in a 3D space, but the opposite. Basically I wanted to render 3D graphics in a 2D terminal and just follow what [Terry Davis did with TempleOS](#background). Reading the glyph protocol also gave me confidence that I could also design my own [terminal protocol](#rgp) similar to it.

And so, Ratty was born.

## Ratty

![]

## Implementation

### RGP

A protocol that I have created to allow TUIs to own font data https://rapha.land/introducing-glyph-protocol-for-terminals/

Example with ratatui https://github.com/raphamorim/glyph-protocol-examples

> A terminal is supposed to be a canvas. If the canvas cannot render what the application asks it to, the canvas is incomplete.

## Wrapping up

> man, thats crazy, the fact you can pull the terminal like that
> makes sense since its all in 3D but still, feels weird when you see it the first time around

> I cant believe u are still on it 😂
> No comments, just support.

> If i restricted my coding projects to useful things, I would probably be a farmer by now
> [11:37 AM]Coko [NVIM], : these kinds of experiments are where creativity is born

Exporting TempleOS artifacts

600 deps for a terminal emulator?
game engine for a terminal emulator?
memory usage?

I'm just trying to make terminal more powerful and fun to use.

If you want to see terminals move forward, support these new terminal protocols such as the Glyph protocol and respective projects. And let me know if Ratty is worth exploring further.

I'm obsessed with terminals.

Mention EuroRust talk
