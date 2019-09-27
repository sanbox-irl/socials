# Sprites, from PNG to Rendering: A System

My name is Jack Spira -- I'm a Unity programmer by day and a Rust fiddler by night. In my Rust work, I've been making a game engine from scratch -- I don't have any grand ambitions, but it's a fun hobby project. If you want to hear more updates about those projects, feel free to give me a follow on Twitter [here](https://twitter.com/sanbox_irl).

This article is about making a Sprite Renderer in Rust using Vulkan. It doesn't cover everything (notably, only parts of the Renderer itself will be discussed, though the API of interacting it will be created. For more on how to make a great Renderer used Vulkan in Rust, look [here](https://rust-tutorials.github.io/learn-gfx-hal/)). There aren't enough articles out there on making a complete system in a game, and I would like to make a contribution towards fixing that gap; equally, I have been inspired by [Meseta's blog posts](https://medium.com/meseta-dev) on his MMO development, and would like to make something similar for other aspects of game development.

Well, enough preamble -- let's get going.

## Sprite System

When I say a "system", or more specifically, a "gamedev system", I mean a system which handles what computer scientists call a "cross-cutting concern". This means that a "system" here spans multiple domains, and frequently moves _laterally_ across your programs APIs. To put this bluntly, you can't write a system up in one Java class and call it a day -- it's going to need to spill out into more than just that.

So, like anything in computer science, our sprite system has an input and it has an output. The goal of this post is to show you how I turned my inputs into my outputs, and the techniques (and in some cases, hacks) that I used along the way.

### The Output

When working in a large system, it's best generally to think about it from the perspective of the output -- the whole point is to get that output anyway, isn't it? Starting from the end then, our goal is simple: draw images to the screen.

Okay, well, that's not really the goal (we could already draw things to the screen!). Let's take a step back and really think of what our implementation will look like:

```rs
renderer::draw(sprite);
```

Okay...this makes sense, but how does the renderer possible know what to draw? How can our `sprite` tell the Renderer what to draw? We've got a few options with that, but, to understand, we're going to need to go to the other end.

### The Input

All of our PNGs are separate files right now. Here's what our `assets/texture/` folder looks:

```
test_files/
-> zelda.png
-> link.png
-> center_dot.png

kenny_characters/
-> kenny_player.ase
-> kenny_player_sprite_sheet.png
-> kenny_player_sprite_sheet.json
```

As you can see, a few things going on here. First, we have three test files -- "zelda", "link", and "center_dot". Each of those is a single image/png, and we've been drawing with our renderer in the past by just loading them in as a single image. That's worked just fine, but now we want to draw "kenny_player_sprite_sheet", which is a Horizontal Sprite sheet animation of a character.

INSERT IMAGE HERE!

In this animation, we have _two_ things going on -- first, each "frame" is layed out horizontally, and, secondly, there are multiple animations here.

This hoses our old strategy of uploading each image directly and then drawing them. Let me explain why:
When we draw, we need to declare which image we want to draw (we'll have previously uploaded them).

For the sake of sanity, at this point in time, our implementation looked like an enum:

```rs
pub enum SpriteName {
    Zelda,
    Link,
    CenterDot
}
```

Here's the problem: each of these sprite enums, in some way in our program, will need to be linked to a texture for the GPU to draw. If we make a new enum entry called, say, `KennyPlayer`, we've got a problem -- Kenny Player refers to, at least, 12 images. We only want to draw one at a time. The solution to that problem, if we *must* stick with this implementation, would be to make a new enum variant for *every single animation in KennyPlayer*. This is terrible, and completely hoses this strategy, so we're going to need something better.

When we look back at the end of this process, needing to figure out how to signfiy to the GPU what texture we want to draw, we can combine these two points:

How do we tell the GPU where in the Kenny texture to draw?

Well...by telling it where in the texture to draw, of course! We give it some offsets, and tell it to start drawing from here, and end there. 
