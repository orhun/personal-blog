+++
title = "Building a guitar trainer with embedded Rust"
date = 2026-03-28

[taxonomies]
categories = ["Projects"]
+++

All I wanted was to learn how to play guitar, but ended up building a DIY kit for it.

<!-- more -->

<g>TL;DR:</g>

<iframe width="560" height="315" src="https://www.youtube.com/embed/tZm7cLaHAR0?si=ilAY4rk8yqG-OIfX" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

[https://github.com/orhun/tuitar](https://github.com/orhun/tuitar)

---

In the beginning of 2025, I picked up playing guitar again after a long break, thanks to the encouragement of a friend.
It was fun at first but my engineer mind wanted to optimize the learning process somehow. I'm sure it would be better if I took my time, but it wasn't for me to wait until I could play properly.
I needed practical knowledge right away, so that I could include simple chords and solos in my music. I needed a shortcut to unblock myself, otherwise I felt like my creativity was stifled.

That's when I decided to build a tool to practice electric guitar, without even an idea of what that would look like. As if that wasn't enough craziness, I also submitted this project idea to the [Rust Forge conference](https://rustforgeconf.com/) so that I could speedrun the development if the talk got accepted.
(i.e. _talk-driven development_: set a deadline and become responsible to deliver something)

Coincidentally, the [Ratatui](https://ratatui.rs/) project had a new backend called [Mousefood](https://github.com/ratatui/mousefood) at the time. This allowed running terminal UIs on embedded devices (e.g. ESP32). This seemed like a perfect opportunity to hack with terminal UIs, embedded and guitar tooling. However, since I still wasn't confident about what to build, I took a trip to Berlin in May to visit [Superbooth](https://www.superbooth.com/) (a huge synthesizer fair). In the end, I had [more inspiration](https://blog.orhun.dev/am-i-a-musician-yet/) than I needed.

<q>
Ok, it seems like you are getting a bit sidetracked. I mean, getting excited about synthesizers is cool and all, but what about the guitar tool?
</q>

Oh yeah, that. My talk got accepted. So it was time to roll up my sleeves.
I realized if I overthink this, I would never get started. So I decided to make a very simple guitar tuner first. (Of course, not embedded yet, just a terminal app.)

1\. Get audio input from the microphone (using [cpal](https://docs.rs/cpal/))  
2\. Perform FFT on the audio input (using [rustfft](https://docs.rs/rustfft))  
3\. Find the fundamental frequency and map it to a musical note (using [pitchy](https://docs.rs/pitchy))  
4\. Plot everything on a chart (using [ratatui](https://docs.rs/ratatui))

<img src="/fft.png" width="80%"/>

That was some progress and it motivated me to keep going. The next step was to port this simple MVP to embedded. I quickly locally-purchased an [MAX4466 microphone module](https://www.analog.com/en/products/max4466.html) and [ESP32 T-Display](https://lilygo.cc/products/t-display) to start experimenting.

For context, the Mousefood backend works by translating terminal cells into [embedded-graphics](https://docs.rs/embedded-graphics) text primitives and pushes them onto any [DrawTarget](https://docs.rs/embedded-graphics/latest/embedded_graphics/draw_target/trait.DrawTarget.html) (usually a display driver e.g. in my case it is the tiny screen on ESP32 T-Display).

<q>
So it's basically a framebuffer... How about the toolchain needed for this?
</q>

On ESP32, the main Rust frameworks are [ESP-HAL](https://github.com/esp-rs/esp-hal) (no_std) and ESP-IDF-HAL (std). [ESP-IDF-HAL](https://github.com/esp-rs/esp-idf-hal) is a wrapper around Espressif's official SDK (ESP-IDF) and it has better support for peripherals. However, it is a bit heavy for small applications since it includes FreeRTOS and other components. ESP-HAL is a pure Rust implementation and it is more lightweight.

Today, Ratatui/mousefood supports no_std [after the 0.30 release](https://ratatui.rs/highlights/v030/). Back then, my only option was to go with ESP-IDF-HAL and include the standard library and a bunch of other C dependencies.

<q>
It's fine though! We are only prototyping, right?
</q>

Unfortunately, it was more painful than you would expect.

Setting up the toolchain was so cursed that at some point I needed to symlink a legacy soname (libxml2.so.2.13.8 as libxml2.so.2) for compatibility. In the end, I was able to render a rectangle using Ratatui though.

<img src="/ratatui-rectangle.png" width="50%"/>

<q>
Is this how the progress look like? Just a tiny rectangle?
</q>

Chill... let me hook up the microphone. It's just a matter of changing the cpal input with a MAX4466 connected to an ADC pin and we should be able to do pitch detection inside of this tiny rectangle. Just need to change the callback and the rest should work the same.

```rust
let sample_callback = |data: &[i16], _| { };
let sample_rate = supported_config.sample_rate.0;
```

Just change that with:

```rust
let sample = mic_adc_channel.read().unwrap_or(0);
let sample_rate = /* ??? */;
```

Wait... we know the sample rate of my computer's microphone. But what's the sample rate of this tiny analog microphone module? That doesn't really work with what I have already. And why all of a sudden this `rustfft` function is crashing now? I forgot I had only 520 KB of RAM. Ugh!

<q>
Just use <a href="https://docs.rs/microfft">microfft</a> for doing FFT bro. But I ain't know nothing about that sample rate issue. Maybe ask a friend.
</q>

Yeah, that's what I did. We had a fun one hour trying to figure out stuff and went over the rough edges.

<img src="/fft-paper.jpg" width="80%"/>

_The real engineering sometimes happens on a piece of paper... that no one can read._

It turns out, I can just use a dynamic sample rate. I tried many fancy things (like using scoped threads to read the samples) but the following is a simple loop which just worked fine:

```rust
let mut samples = Vec::with_capacity(512);
let adc = AdcDriver::new(peripherals.adc1);
let mut mic_adc_channel = AdcChannelDriver::new(adc, peripherals.pins.gpio36, &cfg);

loop {
    let instant = Instant::now();
    while samples.len() < 512 {
        let sample = mic_adc_channel.read().unwrap_or(0);
        samples.push(sample);
    }

    transform.process(&samples);

    let elapsed = instant.elapsed();
    let sample_rate =
        samples.len() as f64 / elapsed.as_secs_f64();

    /* render UI */
}
```

_Continuously fill a buffer with 512 samples as fast as possible, process them, then derive the sample rate by dividing the number of samples by the elapsed wall-clock time. Profit._

Next, add some Ratatui magic (e.g. widgets) on top and I got myself a tiny guitar tuner:

<p align="right">
<center>
<video controls width="80%">
  <source src="/tuner.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>
</p>

<q>
That's cool, I guess this alone is already helpful for learning guitar. So the next step is to practice some jams and start preparing the talk?
</q>

I mean I could do that. But how about we push this thing a bit further? I mean, you are a rat and I'm surprised why you haven't asked about that rat magic. How about we implement a Ratatui fretboard widget and show the played notes real-time?

<q>
You got me. I forgot we are literally rendering terminal UIs on a tiny screen. Rendering a fretboard with unicode characters would be straightforward
</q>

It was!

```rust
let fretboard = Fretboard::default();
let mut state = FretboardState::default();
state.set_active_note(Note::A(4));
frame.render_stateful_widget(fretboard, area, &mut state);
```

```text
E4 ║─┼───┼───┼───┼───┼⬤─┼───┼───┼───┼───┼───┼───║
B3 ║─┼───┼───┼───┼───┼───┼───┼───┼───┼───┼⬤─┼───║
G3 ║─┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───║
D3 ║─┼───┼───┼─•─┼───┼─•─┼───┼─•─┼───┼─•─┼───┼───║
A2 ║─┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───║
E2 ║─┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───║
     1   2   3   4   5   6   7   8   9  10  11  12
```

I also made the [ratatui-fretboard](https://crates.io/crates/ratatui-fretboard) very customizable so that if I get a bass guitar in the future I could also support that. (I recently got a bass guitar btw)

<img src="/ratatui-fretboard.png" width="60%"/>

<small>
Tbh you can also render things like this but pls don't.
</small>

The result was satisfying to see:

<img src="/ratatui-fretboard-tft.png" width="70%"/>

At that stage, I was still testing things with an [online tone generator](https://www.szynalski.com/tone-generator/). I would simply play 440Hz and expect to see that A4 note detected on the display. Or I would simply play my classical guitar sometimes.

With the excitement of seeing this fretboard widget work, I decided to pick up my electric guitar and give this a spin. But guess what was wrong... it wasn't possible to get a reliable read without cranking up the guitar amplifier's volume and waking up my neighbors.

<q>
Ah I see... Maybe connect your electric guitar directly to this somehow? I mean... It's electric, right? Just connect the 6.35mm jack to the same ADC pins that you use for reading the samples from the MAX4466 module.
</q>

Uh... I listened to the advice of a rat and it didn't work...

<q>
Maybe the signal is too weak? Use a low-power dual operational amplifier (op-amp) such as <a href="https://en.wikipedia.org/wiki/LM358">LM358</a> to amplify the signal. It's a tiny electrical component that has 8 pins. You simply power it from an external source and it makes small voltage signals bigger and easier for the ADC to read.
</q>

But then we will need more voltage, right?

<q>
Essentially, yes. Let's power everything from a 9V battery. You need to regulate the voltage to power the ESP32 though... It uses 3V.
</q>

Yes, yes. I tried out [AMS1117](http://www.advanced-monolithic.com/pdf/ds1117.pdf) for regulating the voltage but that heats up too much and makes the display jittery. I think I will go with a buck converter such as the [MP1584](https://www.monolithicpower.com/en/mp1584.html) for more efficiency. It might be more noisy on the circuit compared to AMS1117 but at least it runs cool.

But hold on a second, how do you know all of these electronics things again?

<q>
That's how we started programming, remember? Hacking DIY projects back in the <a href="https://en.wikipedia.org/wiki/PIC_microcontrollers">PIC microcontrollers</a> era and making our own <a href="https://github.com/orhun/picasso">development board</a> as a competitor to Arduino.
It was all before Rust and the world was a different place back then.
But hey, did it work?</q>

Yes sir! I connected my guitar to this and can see the played notes real-time!

<p align="right">
<center>
<video controls width="80%">
  <source src="/fretboard.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>
</p>

<q>
Looks cool, but how do you even see this tiny screen if you place this device far from you?
</q>

Uhhh... I don't...

That's why it was time to upgrade. Instead of using ESP32 T-Display, I wanted an external display and another ESP32 controller. I locally sourced these two components:

<table>
<tr>
<td>

TFT SPI 120×160 (v1.1)

| **Spec**   | **Value** |
| ---------- | --------- |
| Size       | 1.8"      |
| Resolution | 120×160   |
| Driver IC  | ST7735    |
| Interface  | SPI       |
| Voltage    | 3.3 V     |

</td>
<td>

**ESP32-WROOM-32D**

| **Spec** | **Value**      |
| -------- | -------------- |
| MCU      | ESP32-D0WD     |
| Cores    | 2 (Xtensa LX6) |
| Clock    | Up to 240 MHz  |
| Flash    | 4 MB           |
| RAM      | 520 KB SRAM    |
| Crystal  | 40 MHz         |
| Voltage  | 3.0 – 3.6 V    |

</td>
</tr>
</table>

I figured out the correct pins, connected everything together and it worked.

```rust
let peripherals = Peripherals::take()?;
let spi = peripherals.spi2;
let sclk = peripherals.pins.gpio14;
let sdo = peripherals.pins.gpio13;
let sdi = Option::<esp_idf_svc::hal::gpio::Gpio0>::None;
let cs = Some(peripherals.pins.gpio25);
let rst = PinDriver::output(peripherals.pins.gpio33)?;
let dc = PinDriver::output(peripherals.pins.gpio27)?;
let driver_config = Default::default();
let spi_config = SpiConfig::new().baudrate(40u32.MHz().into());
let spi = SpiDeviceDriver::new_single(spi, sclk, sdo, sdi, cs, &driver_config, &spi_config)?;
let mut display = ST7735::new(
    spi, dc, rst, true, false, 160, 128
);
let backend = EmbeddedBackend::new(&mut display, EmbeddedBackendConfig::default());
```

Then it didn't work for a while. Then it worked again. I have no idea what's going on…

Hey rat chef, can you take a look at the connections for me and tell me what might be wrong?

<img src="/breadboard.png" width="60%"/>

<br>

<q>
Holy cheese... It looks like doing everything on a breadboard might be the problem here. The connections might be loose, there might be noise from the components or anything could happen. Let's make a PCB and wrap this up.
</q>

While we're at it, let's have a proper name for this project and a logo. I have so many ideas to implement too!

<q>
How about <b>Tuitar</b>? GUIs vs TUIs, you know...
You are essentially running a TUI to learn GUItar.
</q>

Haha brilliant!

Next steps: create schematics, design PCB, order it from JLC, solder everything, hope for the best.

<img src="/schematics.png" width="100%"/>

<br>

<img src="/tuitar-pcb.png" width="70%"/>

And here is the final PCB:

<img src="/tuitar-board.jpg" width="70%"/>

While doing this, I also wrote every step down the assembly (with pictures) and printed a small booklet from it. Thinking that this will be a DIY kit one day.

<q>
How about we add a couple of other modes to this though? For example, a “Tuitar” hero, or something that you can load songs and practice?
</q>

Good idea. I can also polish the UI/UX a lot! I have 2 buttons and 2 potentiometers to tweak things. I also need to add more colors and styles.

Here is the random mode (aka Tuitar hero):

<img src="/tuitar-fretboard-random.gif" width="70%"/>

A random note on the fretboard is shown, you play it in the given time and get points.

Implementing the "song mode" was more difficult though. Firstly, I had to purchase a pro plan from [Songsterr](https://www.songsterr.com) to download the MIDI files of the songs that I want to load to the device. Speaking of, I decided to support both MIDI and Guitar Pro formats with [midly](https://docs.rs/midly) and [guitarpro](http://docs.rs/guitarpro) libraries.

<q>
We can't parse these MIDI files during runtime though. 512kb RAM, remember?
</q>

Yup, that's why I parse them during build time and simply ship them as a part of the firmware for now. I know I know… I can do so many other things instead (e.g. this ESP32 device has Wi-Fi and Bluetooth). But remember, I'm still prototyping!!! (if you believe that)

Parsing MIDI files looks like this:

```rust
Smf::parse(data)
    .tracks[track] // select track
    .iter()
    .filter_map(|e| match e.kind {
        TrackEventKind::Midi {
            message: MidiMessage::NoteOn { key, vel },
            channel,
        } if channel != 9 && vel > 0 => {
            Some(
                // midi to note
            )
        }
        _ => None,
    })
    .map(|n| vec![n]) // group notes per beat (simplified)
    .collect()
```

And then I generate a "notes array" at build time (i.e. in build.rs):

```rust
let mut code = String::new();
code.push_str("pub struct Song { pub name: &'static str, pub notes: &'static [&'static str] }\n\n");
code.push_str("pub const DEMO: Song = Song {\n");
code.push_str("    name: \"Demo Song\",\n");
code.push_str("    notes: &[\n");
for n in notes { code.push_str(&format!("        {n},\n")); }
code.push_str("    ],\n};\n");
```

This way I can practice the songs by playing note-by-note!

<img src="/tuitar-fretboard-song.gif" width="70%"/>

In other words, if I play the shown note it disappears and the next one is being shown.
There is no metronome or other controls yet though.

Fast forward to the future...

<q>
Did you go to New Zealand?
</q>

Yes... I live-demoed Tuitar and gave away one of the prototype kits. It has already been built and working fine! Hopefully it will help someone learn the guitar in other parts of the world.

<img src="/tuitar-thumbnail.png" width="80%"/>

<br>

<iframe width="560" height="315" src="https://www.youtube.com/embed/es48dmNWMVQ?si=inoj7sPPmLEPlJXV&amp;start=29905" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<br>
<br>

<img src="/tuitar-promo-pcb.jpg" width="100%"/>

P.S. All of the development was recorded.  
I built Tuitar on livestream as a part of a series called <a href="https://www.youtube.com/playlist?list=PLxqHy2Zr5TiUiLYsNbFF8ACf_Er_7MgP-">Becoming a Musician</a>  
(38 episodes as of now, 100+ hours of content)  
You can literally watch me suffer...

---

# Debugging a strange crash

I experienced a really strange firmware crash while building Tuitar.
I say "strange", but it actually makes sense when you understand what's going on.

Simply put: the firmware would only **run correctly** when built using a specific Cargo target directory on my system. If I do a fresh build, I would always get a crash during startup and the program would go into an infinite boot-loop.

The logs were like the following:

```
Guru Meditation Error: Core 0 panic'ed (LoadProhibited). Exception was unhandled

Core 0 register dump:
PC      : 0x40068775  PS      : 0x00060333  A0      : 0x8008bc17  A1      : 0x3ffb44c0
0x40068775: _xPortEnterCriticalTimeout

A2      : 0x124e170  A3      : 0xffffffff  A4      : 0x00001000  A5      : 0x00006323
A6      : 0x00000000 A7      : 0x00000000  A8      : 0x00060320  A9      : 0x3ffb44c0
A10     : 0x00000000 A11     : 0x00060320  A12     : 0x00000000  A13     : 0x00000009
A14     : 0x00000000 A15     : 0x00000000  SAR     : 0x0000001f  EXCCAUSE: 0x0000001c
EXCVADDR: 0x124e170  LBEG    : 0x4008cc20  LEND    : 0x4008cc20  LCOUNT  : 0x00000000

Backtrace: 0x40068775:0x3ffb44c0 0x4008bc14:0x3ffb44f0 0x40083991:0x3ffb4510 0x4008bb50:0x3ffb4540
0x4008d127:0x3ffb4560 0x4012d7fe:0x3ffb4580 0x400dffba:0x3ffb45b0 0x400d4366:0x3ffb45d0
0x4008d127:0x3ffb45e0 0x4012d7fe:0x3ffb4580 0x400dffba:0x3ffb45b0 0x400d4366:0x3ffb45d0
0x4008d127:0x3ffb45e0 0x4012d7fe:0x3ffb4580 0x400dffba:0x3ffb45b0 0x400d4366:0x3ffb45d0

Backtrace:
0x40068775: _xPortEnterCriticalTimeout
    at ????:??
0x4008bb50: heap_caps_aligned_alloc_offs
    at ????:??
0x40083991: heap_caps_aligned_alloc_base
    at ????:??
0x4008bc14: heap_caps_aligned_alloc
    at ????:??
0x4008d127: posix_memalign
    at ????:??
0x4012d7fe: _rustc:: __rdl_alloc
    at ????:??
0x400dffba: _rustc:: __rust_alloc
    at ????:??
0x400d4366: <tuitar_firmware::transform::Transform as tuitar_core::transform::Transformer>::find_fundamental_frequency
    at ????:??
0x400d4fc9: tuitar_core::state::State<T>::process_samples
    at ????:??
0x400de742: tuitar_firmware::main
    at ????:??
0x4014e33f: std::sys::backtrace::__rust_begin_short_backtrace
    at ????:??
0x400d4e8c: std::rt::lang_start::{{closure}}
    at ????:??
0x4013e293: std::rt::lang_start_internal
    at ????:??
0x400de923: main
    at ????:??
0x400d4777: main
    at ????:??
0x4016d713: main_task
    at ????:??

ELF file SHA256: 0000000000000000

Rebooting...
```

> Guru Meditation Error: Core 0 panic'ed (LoadProhibited). Exception was unhandled.

<q>
Huh? Guru Meditation Error? That sounds scary and spiritual.
</q>

It refers to a [famous critical system crash notification](https://en.wikipedia.org/wiki/Guru_Meditation) that originated from the Amiga era of computing.

<q>
Got it. One thing that I'm sure is that this is some obscure toolchain bug, just wipe your system, reboot and it will be fine, most likely.
</q>

Uhh, to make this matter worse, this issue turned out to be reproducible from 16,500 km away from me. Remember the person that I gave away the DIY kit in New Zealand? He sent me those logs above... He built the entire kit and got stuck running the firmware.
I'm also getting the same issue if I build with any target directory other than _/home/orhun/gh/esp32-playground/spi_display_example/target_

<q>
Wait, are you still using that target directory from the ESP32 playground repo we cloned back in March? It's been months man!
</q>

Yes, yes I know... but I had no other option. I mean, I tried making sense of the logs but they simply indicate that the firmware starts correctly, but very early in execution it attempts a misaligned or invalid memory access during heap allocation. Also the [fatal errors section](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-guides/fatal-errors.html) in the Espressif documentation didn't give me any leads to follow:

<img src="/load-prohibited.png" width="85%"/>

I kept reading the logs to figure out what might be wrong:

```
0x4012d7fe - __rustc::__rdl_alloc
    at ????:??
0x400dffba - __rustc::__rust_alloc
    at ????:??
0x400d4366 - <tuitar_firmware::transform::Transform as tuitar_core::transform::Transformer>::find_fundamental_frequency
    at ????:??
0x400d4fc9 - tuitar_core::state::State<T>::process_samples
    at ????:??
0x400de042 - tuitar_firmware::main
```

<q>
So it consistently crashes while executing "find_fundamental_frequency" function? I'm assuming it also calls __rustc::__rdl_alloc and __rustc::__rust_alloc which means this might be a memory allocation issue? What is that fundamental frequency function doing anyways?
</q>

I guess it might be allocating more memory than it should? I tried executing nothing in there and reflashed the firmware, but now it crashes at one of the "render" functions. Ugh... It is so strange.

<q>
So you basically moved the crash to another function?
</q>

Yes, it shows that the issue is not related to any code path, it simply happens when the execution reaches a sufficiently deep or memory-intensive call.
But I still don't understand the significance of /home/orhun/gh/esp32-playground/spi_display_example/target. Maybe it has a deeper meaning and I need to become a target-directory-Guru and start meditating to understand it

<q>
Nah come on, it's just a target directory. What is in it anyways?
</q>

It simply contains build artifacts, compiled objects and a bunch of C/SDK artifacts produced by the ESP-IDF. Wait a minute... Maybe it is something caused by [esp-idf-sys](https://github.com/esp-rs/esp-idf-sys)? It essentially downloads esp-idf, its gcc toolchain, and builds it. If something is incompatible or goes wrong then it might lead to this strange situation.
Hey rat, get the tools, we're diving into the target directory.

<q>
Ahoi chef! I got <a href="https://github.com/GNOME/meld">meld</a> to compare directories.
</q>

After some digging and diffing, I found the root cause:

<img src="/esp-idf-sys-log.png" width="100%"/>

The old target directory has the stack size set to <g>8192</g> whereas the newly created targets always have <g>3584</g> which is the default. It's a <g>stack overflow</g> issue!

<q>
Wait a minute... I thought we were already overriding that value in the sdkconfig.defaults file. Even the available Rust templates out there override that value to 8000, as you can see <a href="https://github.com/esp-rs/esp-idf-template/blob/master/cargo/sdkconfig.defaults">here</a>. Why isn't that taking effect for the Tuitar firmware?
</q>

I figured out the rest by just thinking. Normal human thinking.

It's because <g>sdkconfig.defaults</g> lives in the `firmware/` directory. And it's only being read if it is at the project root. This is not documented anywhere…

I guess I moved that file to a subdirectory while I was splitting up the project into workspace crates in the past.
So the fix was simply move it back to the workspace root:

<img src="/tuitar-fix.png" width="100%"/>

> renamed: firmware/sdkconfig.defaults ⟶ sdkconfig.defaults

Time to notify the "customers":

<q>
Oh boi, finally everyone's happy.
</q>

Now, I also found the [exact line](https://github.com/esp-rs/esp-idf-sys/blob/dc61350013fd375a532d7d93ee91edf117433267/build/pio.rs#L114) in the esp-idf-sys:

<img src="/esp-idf-sys-bug.png" width="90%"/>

Moral of the story? Maybe I should contribute to their documentation.

---

Tuitar GitHub: [**https://github.com/orhun/tuitar**](https://github.com/orhun/tuitar)

- [Hardware details](https://github.com/orhun/tuitar/tree/main/hardware)
- [Firmware details](https://github.com/orhun/tuitar/tree/main/firmware)
- [Promo video](https://www.youtube.com/watch?v=tZm7cLaHAR0)
- [RustForge talk](https://www.youtube.com/watch?v=es48dmNWMVQ&t=29905s)

Hackster article: [This ESP32 Gadget Makes Guitar Practice a Breeze](https://www.hackster.io/news/fret-not-this-esp32-gadget-makes-guitar-practice-a-breeze-dcc26bcef0ba)

<img src="/tuitar-bell-curve.jpg" width="60%"/>

Hope you enjoyed the read! 🐁
