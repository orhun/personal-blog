+++
title = "Write less code, be more responsible"
date = 2026-04-11

[taxonomies]
categories = ["Thoughts"]
+++

My thoughts on AI-assisted programming.

<!-- more -->

<center>

<img src="/rat-assisted-programming.jpg" width="75%"/>

<i>"AI-assisted programming?</i><br>
<i>Nah, I have a rat under my hat"</i>

</center>

These days, the discourse on AI-assisted programming, vibe coding and LLMs is getting a bit overwhelming and sometimes we are overexposing ourselves to it, whether we want it or not.  
The irony is, "not talking about AI" is still "talking about AI", so we might as well talk about it.

<q>
Wait, what? I can very much "not talk about AI". I mean, I don't do vibe coding.  
But I see more vibe coded projects coming out and I sometimes see cool stuff.
People seem to use Claude these days, probably becaus-
</q>

You are talking about AI right now!

<q>
You got me!
Well, with this blog post, you are becoming a part of this whole AI over exposure anyways.
</q>

Probably. But recently I realized that I might have some opinions to share while I was talking to my friends [on this podcast](https://www.youtube.com/watch?v=fyDE_tbAAlw). Also, I should mention, these thoughts probably have an expiry date of 6 months since things are changing fast!

## **Accepting the change** 💊

<g>Programming is fun.</g> Or should I say "was fun"?

<q>It can still be fun! Just because the tools have changed and you can now one-shot a SaaS project that would have taken 3 months to build in 2018 doesn't mean that the joy from crafting something is gone. It's just that the process might be different, but _you_ are still <g>responsible</g> for the end product. There is still a value that you are providing there.</q>

I'm in the basket of developers that think that AI has forced us to change and adapt, in the sense of utilizing AI-assisted programming tools. For a while I was already using [GitHub Copilot](https://github.com/copilot) for better completions and code generation, but I first experimented with proper AI-assisted development with [Codex](https://github.com/openai/codex), during [this livestream series](https://www.youtube.com/playlist?list=PLxqHy2Zr5TiWOHYcTgxYZMzHV36PxRVf9).
Long story short, I was building a terminal user interface for [cargo](https://doc.rust-lang.org/cargo/)'s tree command ([cargo-tree-tui](https://github.com/orhun/cargo-tree-tui)), which required me to design a tree data model and a compatible render layer with caching, without compromising the performance.

At first, I gave Codex the full power. Then I felt lost, confused and illiterate. Quickly realized it was a mistake, so I switched to a "commit-by-commit quality-checked AI-assisted" approach, where I solve a certain problem with AI, then read everything to the last semicolon and verify that I understand it fully. This made everything much better and I felt less dumb, but after a while I got bored. I was not writing code anymore but constantly doing code reviews.

<q>Ok I feel depressed listening to that. Maybe it would take the same or less amount of time and it would be more fun if you just don't use AI and write everything yourself?</q>

Yes, but I'm lazy. Even though I do that, I will probably use Codex for the other boring tasks...

I think the key is to find a <g>balance</g>, like everything else in life. So these days I'm trying a mixed approach where I use AI for boring things or things that I can't do quickly, and just write the fun parts myself and do a final pass to make sure everything is up to the quality I want.

⚠️ I think it's important to experiment and find what works for you, and not to feel guilty about it. I skipped doing some [livestreams](https://www.youtube.com/@orhundev) because of this exact reason (since the process would likely involve AI and I don't want to stream a prompting session), but I think it was a mistake. From now on I would like to be more open about my use of AI and don't treat it like a bad habit.

As a bonus, see [this interview](https://www.youtube.com/watch?v=LGrx9ueO3y0) in which I further talked about how I use AI and other related things.

## **Quality problem** 💯

I post about the [cool stuff that I found](https://blog.orhun.dev/800-rust-projects/) on my social media every day. This process involves following certain sources and maintaining a list of material actively. What I have recently realized is, I no longer can catch up with the new tools and my list is getting larger and larger everyday. My guess is, this is due to the influx of vibe-coded apps, since now it generally takes less time and effort to build things.

<q>So you are saying that the quality of the projects is going down?</q>

Not necessarily, but I think there is a risk of that. It's also related to the <g>maintainability</g> of those projects. Sometimes I discover a project that is truly wonderful but visibly vibe-coded. I start using it without the guarantee of next release not running `rm -rf` and wipe my system.

<q>Can we blame the developers for that?</q>

It's something ethical that I don't know the answer to. In my case, it was the guy's first ever open source project and he understandably went for the quickest way of creating an app. While I appreciate their contribution to open source, they should be responsible for the quality of what they put out there.

<q>I think this applies to everyone though. Nowadays many people are pushing AI-assisted code, some of them in a responsible way, some of them not. So... what do we do?</q>

Like I said, I don't know. The only thing that I can do is to be responsible myself and advise others to be responsible as well.

### **License question** ⚖️

There are obviously licensing questions as well. Can we use AI-generated code in an X licensed project? What will be the license of the generated code? How ethical is it to use AI-generated code in the open source context?

<q>I recently witnessed someone call the LLM industry toxic toward the FOSS movement.</q>

Well I don't know about that. In my internet experience, licensing disputes mostly lead to nowhere and I don't think this will be an exception. However, I would still be interested in hearing different opinions on this topic, that's why I'm putting this out there.

<q>Look, I ain't no lawyer as well. I'm just a rat.</q>

### **Wrapping up** 📝

I adopted ["Grinding"](https://blog.orhun.dev/open-source-grindset) a while ago as my motto and started [a community](https://grindhouse.dev/) around it [last year](https://blog.orhun.dev/2025-wrapped/#communities). I think it applies to this situation as well.

We should keep <g>grinding</g>, keep building cool & high quality stuff, and how we got there shouldn't be that important. AI is just another tool, and not a replacement for creativity and hard work.

<q>
Be responsible.
Don't drink and drive / don't vibe code and commit.
</q>

🧀
