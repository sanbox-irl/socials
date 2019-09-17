# Sprites, from PNG to Rendering: A System

My name is Jack Spira -- I'm a Unity programmer by day and a Rust fiddler by night. In my Rust work, I've been making a game engine from scratch -- I don't have any grand ambitions, but it's a fun hobby project. If you want to hear more updates about those projects, feel free to give me a follow on Twitter [here](https://twitter.com/sanbox_irl).

This article is about making a Sprite Renderer in Rust using Vulkan. It doesn't cover everything (notably, only parts of the Renderer itself will be discussed, though the API of interacting it will be created. For more on how to make a great Renderer used Vulkan in Rust, look [here](https://rust-tutorials.github.io/learn-gfx-hal/)). There aren't enough articles out there on making a complete system in a game, and I would like to make a contribution towards fixing that gap; equally, I have been inspired by [Meseta's blog posts](https://medium.com/meseta-dev) on his MMO development, and would like to make something similar for other aspects of game development.

Well, enough preamble -- let's get going.

## Sprite System

When I say a "system", or more specifically, a "gamedev system", I mean a system which handles what computer scientists call a "cross-cutting concern". This means that a "system" here spans multiple domains, and frequently moves *laterally* across your programs APIs. To put this bluntly, you can't write a system up in one Java class and call it a day -- it's going to need to spill out into more than just that.

So, like anything in computer science, our sprite system has an input and it has an output. The goal of this post is to show you how I turned my inputs into my outputs, and the techniques (and in some cases, hacks) that I used along the way.

### The Output

When working in a large system, it's best generally to think about it from the perspective of the output -- the whole point is to get that output anyway, isn't it? Starting from the top then, we have
