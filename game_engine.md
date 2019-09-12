# So You Want to Make a Game Engine

Three weeks ago, I had something unexpected on my hands: free time.

You see, typically I work as a contractor on indie games. I get most of my contracts pretty lazily - I get a referral, I reach out when I see something on Twitter, or I gently bother someone into paying me for all the things I've been doing for them for free. This strategy is perfect for my work schedule - I'm another one of those indie devs with a game cooking in my backpocket at all times, so having some extra hours in a day to spend on my own projects is welcome. But, unexpectedly, I had *weeks*, not hours, free. It was just a natural gap in the work schedule, and, fortunately for my wallet and unfortunately for my free ambitions, it has ended for the foreseeable months.

I decided to spend that time writing a game engine from "scratch". Here are some of the things I learned.

## Writing A Single Purpose Game Engine Is Easier than You Think

I typically use Unity in my games, and my conception of a "game engine" is based on what it can do. To name a few qualities, a game engine can render 2D or 3D, it can provide a variety of physics implementations, it can of course make an RTS just as well as it can make an FPS, it is extensively scriptable, and, most of all, it is highly performant. I, a lowly C# and Rust programmer, couldn't possibly make something so complicated as a game engine!

And that was completely correct, thanks for reading my article -- just kidding. It *is* true, though, that I can't make Unity, I can't make Godot, hell I couldn't even make GameMaker, and that's because those engines are general purpose game engines. They support any kind of game you want, and as such, their systems are very broad. If you work at Unity, you can't make a camera for your users -- you have to make a camera that can be completely configured to work with a variety of projections, scales, zooms, aspect ratios, and that's not even talking about the build process, which of course must be completely cross platform (so all your libraries better be as well). 

There's a second kind of game engine. It's not really a "game engine" -- it's a factory. And just like a real life car factory, it doesn't make any kind of car -- it makes one car, and it makes it really well. Our camera here doesn't have to be general purpose -- if we're making a 2D game, we can make our camera only orthogonal, hard code in 16:9 as our aspect ratio, we can even make it not use matrices at all, and write out graphics pipeline specifically to handle simple 2D transforms! In short, when we make our own engine, we can scope the engine to exactly the game we're making, and by doing so, we very quickly go from writing an "engine" to writing our game. Because our engine only makes one kind of game -- our game. 

These engines are all over the place. In a recent Kotaku article [JACK STICK A LINK HERE], BioWare engineers complained that the Frostbite engine, made for FPSes, couldn't make 3rd person RPGs very well. 

To my Unity ears, this idea made no sense. How could a game engine be locked? Now, it makes sense. Maybe the Camera wasn't using any of the technology that was needed for a 3rd person game, maybe the camera was so tightly integrated into the rest of the engine that retrofitting new controls meant rewriting multiple parts of the engine, maybe the camera just generally sucked. And you know what, that's okay, because that camera wasn't meant to be a general camera -- it was meant for one game. And that's good enough.

Could I make a Unity? No. But could I make a game engine that could make one specific game? Yes. The difference between these two extremes can't be overstated.

## Graphics Are Harder than Everything Else
I spent about 