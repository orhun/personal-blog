+++
title = "Am I a musician yet? - Superbooth 2025 Experience"
date = 2025-05-12

[taxonomies]
categories = ["Thoughts"]
+++

I went to Berlin for a music event and here is what happened.

<!-- more -->

## **Intro**

There were moments in my engineering career where I simply wanted to say f\*ck it and throw away my computer. Sometimes what we do as software laborers is either plain boring or easy to lead to burnout, so most of the people reading this have that feeling at least once I believe.

Apparently, I didn't drop everything or become violent towards my computer, but I often found myself playing around with other endavours when that happens, just to feel something again. It's subjective and different for everyone, for me, those f-it moments were always followed by the thought of becoming a musician, artist, rapper, or anything that is music-related. If you have been following me for a while on the internet, you might have gotten the hint from the [songs](https://open.spotify.com/artist/2Fq2iVvxFTmjqWOLUjEkSB/discography/all) that I share sometimes or music-related open source projects such as [Linuxwave](https://github.com/orhun/linuxwave), so I have been taking some steps towards that dream for a while now.

<center>

<div style="position: relative; width: 100%; max-width: 720px; margin: auto; padding-bottom: 56.25%; height: 0; overflow: hidden;">
  <iframe 
    src="https://www.youtube.com/embed/SLiEuvDmo8M?si=y7DU9YOof_KfQFwJ" 
    title="YouTube video player" 
    style="position: absolute; top: 0; left: 0; width: 100%; height: 100%; border: 0;"
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
    referrerpolicy="strict-origin-when-cross-origin"
    allowfullscreen>
  </iframe>
</div>

Also, a couple of months ago, I got inspired and encouraged by my good friend [Harun](https://github.com/harunocaksiz) and got myself an electric guitar to play some of my favorite shoegaze/grunge tracks:

<img src="https://static.orhun.dev/sb/guitar.png" width="65%"/>

</center>

Playing an instrument after a long time really opened my eyes to why I was interested in music in the first place: it's the most direct way of conveying my emotions in an improvisational way. And recently I have been wanting to build a project that combines my love for music and software.

If you are new around, I'm maintaining [Ratatui](https://github.com/ratatui/ratatui), a Rust library for building user interfaces in the terminal. There are already music-related projects built with it such as this [Granular Sampler](https://www.youtube.com/watch?v=XzJnMVo1ZkM) or this [Music Live Coding application](https://github.com/glicol/glicol-cli) so I definitely want to hop on that train.

Also, we recently landed ["no_std" support](http://areweembeddedyet.rs/), which means you can run Ratatui on embedded devices as well. See [this blog series](https://jslazak.com/are-we-embedded-yet-1/) for more information about that. But in a nutshell, you can now do things like:

<center>

<img src="https://github.com/j-g00da/mousefood/blob/599f1026d37c8d6308a6df64a234dbefaedc0c6f/assets/demo.gif?raw=true" width="80%"/>

</center>

There are endless possibilities for what to build! All I needed was some inspiration.

That's when my good friend [David Runge](https://github.com/dvzrv) came to my rescue. He's pretty active in the music scene in Berlin and maintains hella audio packages for [Arch Linux](https://archlinux.org). He mentioned [Superbooth](https://www.superbooth.com) to me and it was definitely what I was looking for — a place where I can meet audio nerds and learn more about what people are building in the analog world.

So in this post I will be documenting my experience of "going to Berlin for music" and sharing my _raw take_ on everything that I saw. It's going to be fun!

---

## **Day 0x0**

_<g>Thursday</g> - Travel to Berlin._

While I'm waiting for my flight, I wanted to compile some general information about Superbooth:

- A trade fair for electronic musical instruments.
- Features a lot of exhibitors, manufacturers, and independent artists.
- Held annually in Berlin, Germany.

Looking at the [website](https://www.superbooth.com/) was enough to get me overwhelmed with the sheer number of events and booths that I had never heard of before. I did a quick research and took some notes for the interesting stuff that I want to see/visit:

| **Exhibitor**                                                                                           | **Notes**                                                                                            |
| ------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| [KORG](https://www.superbooth.com/en/messe-and-exhibitors/exhibitors/korg.html)                         | Of course.                                                                                           |
| [FL Studio](https://www.superbooth.com/en/messe-and-exhibitors/exhibitors/fl-studio.html)               | The OG DAW                                                                                           |
| [Freaknoise](https://www.superbooth.com/en/messe-and-exhibitors/exhibitors/fraknoise.html)              | A synthesizer that produces nothing but melancholic sounds. WOW.                                     |
| [Silhouette](https://www.superbooth.com/en/messe-and-exhibitors/exhibitors/silhouette.html)             | Sample audio, convert it to a picture file and then reuse it as visual material for the synthesis 👀 |
| [MOOG](https://www.superbooth.com/en/messe-and-exhibitors/exhibitors/moog-music.html)                   | The OG synth builders.                                                                               |
| [Ableton](https://www.superbooth.com/en/messe-and-exhibitors/exhibitors/ableton.html)                   | Never used it but heard it's good. Might switch to it one day.                                       |
| [AKAI](https://www.superbooth.com/en/messe-and-exhibitors/exhibitors/akai-professional.html)            | Hey I have an AKAI MPK Mini MK3!                                                                     |
| [Flowfal](https://www.superbooth.com/en/messe-and-exhibitors/exhibitors/flowfal.html)                   | Using smartwatch to control Ableton Live... what!?                                                   |
| [Elektron](https://www.superbooth.com/en/messe-and-exhibitors/exhibitors/elektron.html)                 | Digitone II looks sick!                                                                              |
| [Arturia](https://www.superbooth.com/en/messe-and-exhibitors/exhibitors/arturia.html)                   | I heard about Microfreak before!                                                                     |
| [Landscape](https://www.superbooth.com/en/messe-and-exhibitors/exhibitors/landscape.html)               | Looks very interesting.                                                                              |
| [afk audio](https://www.superbooth.com/en/messe-and-exhibitors/exhibitors/afk-audio.html)               | Next-generation drum controller?? Interesting.                                                       |
| [Audio.computer](https://www.superbooth.com/en/messe-and-exhibitors/exhibitors/audio-computer.html)     | This looks very cute!                                                                                |
| [Beetlecrab.audio](https://www.superbooth.com/en/messe-and-exhibitors/exhibitors/beetlecrab-audio.html) | The vector synth demo is crazy.                                                                      |
| [Sonicware](https://www.superbooth.com/en/messe-and-exhibitors/exhibitors/sonicware.html)               | I need to get my hands on CyDrums.                                                                   |
| [dpw design](https://www.superbooth.com/en/messe-and-exhibitors/exhibitors/dpw-design.html)             | Apparently they do eurorack and pedals.                                                              |
| [Bela](https://www.superbooth.com/en/messe-and-exhibitors/exhibitors/bela.html)                         | They do open source embedded stuff, I definitely need to check this out.                             |
| [Leafaudio](https://www.superbooth.com/en/messe-and-exhibitors/exhibitors/leafaudio.html)               | They are making some interesting looking microphones.                                                |
| [Zlob Modular](https://www.superbooth.com/en/messe-and-exhibitors/exhibitors/zlob-modular.html)         | Kickass logo, also the modules look sick.                                                            |
| [Beetronics](https://www.superbooth.com/en/messe-and-exhibitors/exhibitors/beetronics.html)             | I play guitar, this is my sh\*t.                                                                     |

There is definitely more stuff to check out... but that's all I could filter with a quick glance. Tomorrow, I'm hoping to visit as many things as I can!

---

## **Day 0x1**

_<g>Friday</g> - First day of Superbooth for me._

I woke up early to get myself familiar with the [floorplan](https://www.superbooth.com/en/floorplan.html). This place is huuuuuge.

<img src="https://static.orhun.dev/sb/sign.jpg" width="65%"/>

Alright, let's go!

Here are my highlights...

---

### [Zlob Modular](https://zlobmodular.com/)

<img src="https://static.orhun.dev/sb/zlob-logo.png" width="30%"/>

First of all, they have a kickass logo and gear:

<img src="https://static.orhun.dev/sb/zlob-booth.jpg" width="90%"/>

> They are a small company that specializes in building modular synthesizers and effects, ranging from beginner-friendly utility modules to more advanced VCOs, VCFs, VCAs, LFOs, sample and hold modules, and attenuators.

I attended the DIY workshop and built the [Diode Chaos](https://zlobmodular.com/product/diode-chaos) module.

> Diode Chaos is a small [Eurorack](https://en.wikipedia.org/wiki/Eurorack) module that creates random wiggly voltages and a chaotic trigger signal. It is an unique module and was inspired by the article called ["A simple chaotic circuit with a light-emitting diode"](https://www.researchgate.net/publication/309351711_A_simple_chaotic_circuit_with_a_light-emitting_diode).

<center>

<img src="https://static.orhun.dev/sb/zlob-diode-1.jpg" width="75%"/>

<br>

<img src="https://static.orhun.dev/sb/zlob-diode-2.jpg" width="75%"/>

It took around 3 hours to complete the circuit.

<img src="https://static.orhun.dev/sb/zlob-diode-4.jpg" width="75%"/>

<br>

<img src="https://static.orhun.dev/sb/zlob-diode-5.jpg" width="75%"/>

_Last time I soldered was a while ago... Not that rusty, eh?_

<img src="https://static.orhun.dev/sb/zlob-diode-3.jpg" width="85%"/>

</center>

Here is the final result:

<center>

<img src="https://static.orhun.dev/sb/zlob-diode-6.jpg" width="70%"/>

<br>

<video controls width="80%">
  <source src="https://static.orhun.dev/sb/zlob-diode.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>

Thanks [Keven](https://onurzlobnicki.bandcamp.com) for the workshop and putting up with my boring questions! (:D)

I'm currently lacking the equipment to use this in my music production setup as of now but building it was fun and useful either way I think. At least it gave me my confidence back about electronics (which I dove into quite [a long time ago](https://github.com/orhun/picasso)).

---

### [FL Studio](https://www.image-line.com/)

I paid my necessary visit to the booth — learning how to use FL Studio was one of the biggest milestones in my music career.

<img src="https://static.orhun.dev/sb/fl-studio.jpg" width="90%"/>

Since I'm a super fan, I also got a FL Studio sticker and a legit tattoo in case I decide to get a tattoo of a carrot on my arm one day (💀).

<center>

<img src="https://static.orhun.dev/sb/fl-studio-tattoo.jpg" width="60%"/>

</center>

We had a fun conversation about running FL Studio on Linux. It seems like quite a bit people were also doing that... (see my [setup](https://github.com/orhun/dotfiles) below)

![21-06-2023](https://github.com/orhun/dotfiles/assets/24392180/6a8e1d69-0e02-4020-b473-4f19ac6dedc1)

Unfortunately FL is written in [Delphi](<https://en.wikipedia.org/wiki/Delphi_(software)>) so the guys at the booth told me that it's a bit difficult for them to release an official/native port on Linux anytime soon. Right now the only problem with FL running in Wine is that you are limited to the stock plugins... which is quite enough for me actually. But let's see!

---

### [SOMA](https://somasynths.com)

They have very interesting instruments!

Floating-hand-controlled thing (Soma Flux):

<center>
<video controls width="85%">
  <source src="https://static.orhun.dev/sb/soma-flux.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>

Or this thing (Soma Terra):

<center>

<img src="https://static.orhun.dev/sb/soma-terra.jpg" width="70%"/>

It's a wooden block with some metal spheres on it that you can touch. It produced heavenly sounds for me, but apparently you can use it for many purposes:

<video controls width="90%">
  <source src="https://static.orhun.dev/sb/soma-terra.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

</center>

---

### [Mad Sound Factory](https://madsoundfactory.com/)

I was just walking around the booths and all of a sudden found myself in the middle of a techno rave:

<center>
<video controls width="80%">
  <source src="https://static.orhun.dev/sb/msf-drop-2.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

They have this thing called "[Drop](https://madsoundfactory.com/drop/)" which produces amazing tunes.

I would love to get one for myself (or build something similar) one day!

<video controls width="70%">
  <source src="https://static.orhun.dev/sb/msf-drop-1.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

<br>

<img src="https://static.orhun.dev/sb/msf-drop.jpg" width="70%"/>

</center>

---

### [Microrack](https://microrack.org/)

They seem to have very easy-to-build synths that only require a breadboard!

<img src="https://static.orhun.dev/sb/microrack-1.jpg" width="90%"/>

Also, there was a funny synth that uses a microphone input, adds some effects and produces some autotune-like output.

<center>

<img src="https://static.orhun.dev/sb/microrack-2.jpg" width="70%"/>

</center>

---

### [any frequency](http://anyfrqncy.com/)

They have built a synth that uses AI to learn from a given signal and accompany it by generating a signal that matches the tune.

<center>
<video controls width="100%">
  <source src="https://static.orhun.dev/sb/any-frequency.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>

You can also tweak the learning speed and all kinds of other parameters as well.

---

### [Beetronics](https://www.beetronicsfx.com/)

As noted above, this was my sh\*t.

<img src="https://static.orhun.dev/sb/beetronics-1.jpg" width="55%"/>

They have some kickass pedals with hella functionality and it sounded amazing.

<img src="https://static.orhun.dev/sb/beetronics-2.jpg" width="80%"/>

Unfortunately they didn't bring a guitar to try things out which is a shame — I think it would be splendid to hear those with the guitar.

<center>
<video controls width="100%">
  <source src="https://static.orhun.dev/sb/beetronics.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

Also big props for their branding/marketing!

</center>

---

### [Chase Bliss](https://www.chasebliss.com/)

Another guitar-related booth which I like!

<img src="https://static.orhun.dev/sb/chase-bliss.jpg" width="100%"/>

And they had a small guitar ([Steinberger Spirit](https://www.steinberger.com/Steinberger-GT-PRO-Deluxe-Outfit.html)) to try out their modules!

---

### Boom Kick

Is an introduction needed? It's the <g>B O O M K I C K</g>.

<center>
<video controls width="100%">
  <source src="https://static.orhun.dev/sb/boom-kick.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>

---

### Acidbooth

Man I love acid.

<center>
<video controls width="90%">
  <source src="https://static.orhun.dev/sb/acidbooth.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>

---

### [Silhouette](http://www.silhouette-synthesizer.de/)

> Sample audio, convert it to a picture file and then reuse it as visual material for the synthesis

Saw that in action!

<center>
<video controls width="100%">
  <source src="https://static.orhun.dev/sb/silhouette.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>

---

### Technoise

[Keven](https://onurzlobnicki.bandcamp.com) from Zlob and his friend [Eliot](https://www.gc.cuny.edu/people/eliot-bates) were kind enough to invite me to a party that was happening in the evening after Superbooth.

<center>

<img src="https://static.orhun.dev/sb/technoise.jpg" width="70%"/>

</center>

I showed up and it was literally an office turned into a stage with a bunch of synthesizers and free drinks :D

<center>
<video controls width="80%">
  <source src="https://static.orhun.dev/sb/technoise-1.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>

The lineup was pretty interesting. Here are my highlights:

- [Keven Onur Żłobnicki](https://onurzlobnicki.bandcamp.com)

Definitely interesting and different vibes.
I felt like I was committing a financial crime the whole time.

<center>
<video controls width="90%">
  <source src="https://static.orhun.dev/sb/keven-1.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>

<center>
<video controls width="90%">
  <source src="https://static.orhun.dev/sb/keven-2.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>

- [Makamqore](https://makamqore.bandcamp.com/)

Damn, Goblincore to the moon baby!

<center>
<video controls width="90%">
  <source src="https://static.orhun.dev/sb/goblincore.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>

- [RMM (Robot Man Machine)](https://rmmchicagoofficial.bandcamp.com/)

Man I love acid.

<center>
<video controls width="90%">
  <source src="https://static.orhun.dev/sb/rmm.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>

And here are some other videos that I took from the event:

<center>
<video controls width="90%">
  <source src="https://static.orhun.dev/sb/technoise-2.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>

<center>
<video controls width="90%">
  <source src="https://static.orhun.dev/sb/technoise-3.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>

---

## **Day 0x2**

_(<g>Saturday</g> - Feeling tired already...)_

While checking the schedule on my way in the morning, I realized there are still tickets available for [Clacktronics](http://clacktronics.co.uk/) workshop. So I went for it!

---

### [Clacktronics](http://clacktronics.co.uk)

They design and manufacture DIY synth and Eurorack products. They also have a "[Build Your Own Modular](http://clacktronics.co.uk/2024/Build-Your-Own-Modular-book.html)" book which I find very useful!

In the workshop there were a couple of kits:

- VCO (voltage-controlled oscillator): for generating audio signals.
- VCF (voltage-controlled filter): for filtering audio signals.
- Arpeggiator (stepped ramp generator): for generating arpeggios.

I went with VCO since it's one of the fundamental modules in a synthesizer setup.

<center>

<img src="https://static.orhun.dev/sb/clacktronics-1.jpg" width="85%"/>

<br>

<img src="https://static.orhun.dev/sb/clacktronics-2.jpg" width="70%"/>

This was a quite fun kit to build!

<img src="https://static.orhun.dev/sb/clacktronics-3.jpg" width="70%"/>

<br>

<img src="https://static.orhun.dev/sb/clacktronics-4.jpg" width="70%"/>

<br>

<video controls width="65%">
  <source src="https://static.orhun.dev/sb/clacktronics-1.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>

In the video you can hear the signals produced by the module and me playing with the knob:

<center>
<video controls width="85%">
  <source src="https://static.orhun.dev/sb/clacktronics-2.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>

<center>
<video controls width="65%">
  <source src="https://static.orhun.dev/sb/clacktronics-3.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>

At some point we added other modules (drums, filters, effects) and turned the workshop into an acid rave (Man I love acid btw):

<center>
<video controls width="85%">
  <source src="https://static.orhun.dev/sb/clacktronics-4.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>

Thanks Ben for helping out with the workshop & Simon letting me play around with his setup, it was super fun!

Now I need to get myself a _rack_ to play around with these modules myself when I get back home. 💀

---

### Playing Around

I spared the rest of the day for playing around with the various synthesizers, modules, instruments at the booths. There were many things that I touched and I definitely lost time at some point.

I didn't take specific notes about the things that I tried out but here are some highlights:

- [Yamaha Seqtrak](https://de.yamaha.com/de/products/music_production/music-production-studios/index.html)

It's just amazing. I cooked up some bangers on this. It is surprisingly easy to use and you can do many things.

<img src="https://static.orhun.dev/sb/seqtrak-1.jpg" width="90%"/>

<br>

<img src="https://static.orhun.dev/sb/seqtrak-2.jpg" width="90%"/>

- [Body Synths](https://bodysynths.com/)

Man I love acid.

<img src="https://static.orhun.dev/sb/body-synths.jpg" width="90%"/>

- [Syntonie](https://syntonie.fr/)

Video synth. Aye look that's me!

<center>

<img src="https://static.orhun.dev/sb/syntonie.jpg" width="70%"/>

</center>

Here are the other things that I played / tried out:

![](https://static.orhun.dev/sb/korg.jpg)

![](https://static.orhun.dev/sb/moog.jpg)

![](https://static.orhun.dev/sb/modal.jpg)

![](https://static.orhun.dev/sb/asm.jpg)

![](https://static.orhun.dev/sb/casperbastl.jpg)

![](https://static.orhun.dev/sb/yamaha-drum.jpg)

![](https://static.orhun.dev/sb/ableton.jpg)

![](https://static.orhun.dev/sb/nord-drum.jpg)

While you're walking around, it's possible to run into some random-ass cook sessions like these:

<center>
<video controls width="100%">
  <source src="https://static.orhun.dev/sb/random-cook.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>

Lastly, RIP [theresyn](http://theresyn.com/).

![](https://static.orhun.dev/sb/rip-theresyn.jpg)

---

### Concerts

I was going to meet David but I got distracted by this kickass techno event in the middle of the forest.

It was [Jessica Kert](https://www.instagram.com/jessyandthechords) playing... it was an amazing set!

<center>
<video controls width="100%">
  <source src="https://static.orhun.dev/sb/concert-1.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>

After that, I was really impressed by the huge setup at the stage:

<center>
<video controls width="90%">
  <source src="https://static.orhun.dev/sb/concert-2.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>

I can summarize my second day with this T-shirt I guess:

<center>

<img src="https://static.orhun.dev/sb/art-sex-music.jpg" width="60%"/>

</center>

---

## **Day 0x3**

_(<g>Sunday<g/> - Superbooth is over but the grind continues.)_

Today I'm heading to [**DIY Kit Day**](https://berlinmodularsociety.com) at [Klunkerkranich](http://klunkerkranich.org/)!

It's where you can buy DIY kits and assemble them, listen to some live modular music and just hang out with audio folks at a nice rooftop!

<center>

<img src="https://static.orhun.dev/sb/rooftop-1.jpg" width="60%"/>

<br>

<img src="https://static.orhun.dev/sb/rooftop-2.jpg" width="80%"/>

</center>

I bought and assembled an amazing kit there: <g>Squeaking Rat!!!</g>

<center>

<img src="https://static.orhun.dev/sb/squeaking-rat-1.jpg" width="80%"/>

<br>

<img src="https://static.orhun.dev/sb/squeaking-rat-2.jpg" width="40%" align="left"/>

<img src="https://static.orhun.dev/sb/squeaking-rat-3.jpg" width="40%" align="right"/>

</center>

It's a VCO that produces a squeaky sound and is very fun to play with!

<center>
<video controls width="80%">
  <source src="https://static.orhun.dev/sb/squeaking-rat-1.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>

Look at the eyes!

<center>
<video controls width="40%">
  <source src="https://static.orhun.dev/sb/squeaking-rat-2.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>

Also, got myself a fuzz pedal called [Fur Face](https://pedalmarkt.com/pages/ff) for assembling it when I get back home:

<img src="https://static.orhun.dev/sb/fur-face.jpg" width="80%"/>

Bought the album of Keven from Zlob! I'm glad that there is a CD player in my car.

<center>

<img src="https://static.orhun.dev/sb/pukeintheicetray.jpg" width="60%"/>

</center>

And one really surprising thing happened... I got recognized.

Some people approached me and said they were big fans of Ratatui...

<img src="https://static.orhun.dev/sb/technorave.png" width="90%"/>

It was nice to meet you Juan and Gustavo!

Lastly, we wrapped up the day with some amazing sets, most notably from [Shakmat](https://www.shakmatmodular.com/)'s founder's live set:

<center>
<video controls width="90%">
  <source src="https://static.orhun.dev/sb/shakmat-1.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>

<center>
<video controls width="90%">
  <source src="https://static.orhun.dev/sb/shakmat-2.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</center>
</center>

---

## **Outro**

My Berlin music adventure has ended. It was a very packed 3 days and it really felt like 3 weeks of learning new things!

I'm very glad that I did this. My vision has definitely broadened, I had many ideas for my projects and most importantly met so many new people!

<center>

<img src="https://static.orhun.dev/sb/friends.jpg" width="80%"/>

</center>

Ah, also I now have these modules:

<center>

<img src="https://static.orhun.dev/sb/modules.jpg" width="70%"/>

</center>

<q> So what's the plan going forward? </q>

I have been doing something called <g>talk-driven development</g>.

1. Think of a crazy project idea.
2. Turn that idea into a talk proposal for a conference (and get accepted).
3. Speedrun the development of the project.
4. Profit (or crash and burn).

That's what I did with [Ratzilla](https://github.com/orhun/ratzilla), see my talk at [FOSDEM 2025](https://www.youtube.com/watch?v=iepbyYrF_YQ). I think it's a great way to beat [the comfort of delay](https://www.youtube.com/watch?v=sHxDZPSLvKk) and keep myself on the grind.

And yes, I am doing <g>talk-driven development</g> again these days... but for a music-related project this time. I will start small with a learning tool for my guitar and then maybe I will build a full-fledged Eurorack or some kind of music production workspace in the future.

I'm planning to [livestream](https://blog.orhun.dev/livestreaming/) my whole journey and I'll start in the following weeks. So make sure you follow me [on YouTube](https://youtube.com/@orhundev) if you don't want to miss the new tunes & TUIs!

If you liked this post, or anything that I'm building, gimme a [sponsorship on GitHub](https:/github.com/sponsors/orhun) :)

Special thanks to [David Runge](https://github.com/dvzrv) for letting me know about the Superbooth!

<small>

As a beginner in the music sphere, I would appreciate any ideas or tips about starting out.

As always, you can leave your comments below!

</small>

Love,
~o
