+++
title = "My journey trying to make a retro-like web game"
date = 2025-02-23T23:30:00-05:00
description = "Retro Games up-scaling, my nemesis"
+++

In a previous post I mentioned I wanted to do a, sort-of, return to my roots. To make some games. I decided upon to use KaboomJS to do that. I was in for an unexpected journey trying different game libraries and engines. Basically one year later, I return with a summary of this journey, and still nothing to show for now.

But Ax, ONE whole year later? Why so much time? I mean, I had to do this after work hours, in my spare time after some classes for things I was learning for my job. Getting better at my job was my priority last year and unfortunately because I was not given a proper manager for the Career Path, it took way longer than expected. But I digress. Things are sort of better now, so I have resumed working on this project.

## What game am I doing?

My goal is to make a short collection of small retro-like games. I started with a basic shoot 'em up game. Prototyping it across several game libraries and frameworks for web. The major issues encountered were:

- The up scale rendering of low res pixel art for modern high res displays
- Big physics engines that are overkill for simple retro games. No need for physics simulations for these types of games
- Fractional movement. Low Res is not a good fit for modern diagonal movement that needs subpixel positioning

## Problems with KaboomJS

There were two major issues with KaboomJS as I was experimenting with the library to create a retro game. The first one was the way it expects to create components for new entities. The second one was with the rendering of a retro style game.

### The issue with kaboom's components

KaboomJS uses an Entity Component System to develop games. That is fine on itself, if not for the fact that it expects global imports. I was stubborn to a fault, and I refused to do the global import.

This made my object creation to almost always pass a reference to the kaboom library object. Without it, it was impossible to use basic things like the Vector2D type. I didn't like that, but it worked. What broke things for me was when I needed to sync component actions.

Picture this: an enemy plane is flying straight down, then at a certain point, it shoots towards the player. After that, it turns either left or right to escape. Creating the motion and the shoot components was easy. Syncing the motion with the shoot components proved to be challenging. There was not easy way to make the enemy plane to shoot at the precise moment it needed to. The only way to make that work without hassle, was to combine both components into one.

That was a quick solution, not one I was happy with, but it worked. The next issue with components, didn't have a solution. There was no official way to remove a component from an entity. This was important to make quick editions during prototyping. Only deleting and recreating the game object was the solution I could use. This is not good for memory management, but I still marched on.

### The fuse that broke the kaboom's back

Despite not being happy with the sort-of-hacky workarounds, KaboomJS was doing well. It was not until I moved into up-scaling the retro game that I found two issues I could not find a quick workaround.

When computing the diagonal movement, it was jerky. It looked like going in a stair instead of a smooth diagonal. We want the diagonal movement speed to be the same as the horizontal and vertical ones. This requires computing some fractional speeds, that Kaboom ended up rounding. This caused the jerky movement.

I found a way to sort of fix that. I had to compute the direction vector separated from the velocity vector. But in the end, that didn't matter because of non-integer scaling.

The pixel art graphics were scaled to fit the viewport. This scaling was not a integer number and thus this caused some pixels to be bigger than others. This created a jarring effect in the graphics, uneven size in the objects.

There's a way to fix that, which is using a shader for pixel perfect scaling. The problem is, I couldn't find in a reasonable time a way to apply this shader in KaboomJS. And with that, I bombed out of using KaboomJS.

## Phasing out the next option

Next, I tried Phaser for prototyping the shoot 'em up game. It was bigger than KaboomJS, but it has a lot of examples, and it was not that hard to use. Everything was going well. The issues of syncing the actions and movement of enemies were not present there. They use good ol' classes. Even the player movement was not an issue despite scaling it up. Smooth diagonal movement obtained. But then, that jittering due to non-integer scaling.

No easy way to apply the shader to fix that, mostly because some shaders refused to work in the browser. I found some shader examples that rendered to dark objects in some browsers (\* cough \* firefox \* cough \*). To make things worse, the bundle size was bigger and the Physics engine was bloated for what I needed. In the end, I decided that Phaser was not a good fit for a retro-like game. It was a short phase.

## Farming MelonJs

I tried MelonJs too. It is a simple game framework that I have used in the past. This was an even short ride that Phaser. I programmed only the player character and the scaling options were awful. The non-integer scaling attacked again, and the jerky diagonal movement were present too.

I also remembered why I didn't stick to MelonJs in the past. When we scale objects, their bounding box and collision boxes become a mess. They don't scale at all, or scale poorly. We have to adjust then, but even then, it is buggy most of the time. I returned the melon to the farm.

## A ray of hope with ExcaliburJs

Thanks to the help of my brother, ExcaliburJs entered my radar. At the time of writing, it is still in development. It has the pixel art shader for non-integer scaling. It is small, and sort of easy to use. It has some modern features (like the ECS) that may be a little too much for a small pixel art retro-like game, but it's manageable. Everything was going smooth. I coded the prototype for the n-th time and it was promising, until... Bitmap Fonts. It doesn't have support for Bitmap fonts. The fnt format to be precise. It does have Sprite Fonts, but you need a good monospace font for it to be good. I was not going to go to the rabbit hole that is font rendering for this one. I was putting ExcaliburJs back to the stone unfortunately.

## Godot is... Inevitable

I was pretty burn-out of coding the same prototype so many times, and doing workarounds for the issues. I wanted to have tooling that didn't need me to distract from programming gameplay. In the end, I decided to try a established engine. Cocos was out of the question due to requiring an account to use. Unity and Unreal were overkill for a retro pixel art game. Game Maker, despite having a free option, it didn't allow for web export without a license. Only one option left... Godot.

I have been using it and it is a joy. I mean, I still miss some things from typed languages that I use. The visual editor helps a lot too. Everything was going well until...

### Godot web export

I prototyped some basic shooting and movement, and then decided to do a web export and see how it was... It wasn't good. There's a 40~MB wasm module in the export. That's waaaaay bigger than I was willing to accept. The bigger the export, the more expensive will be to host the game, so unfortunately I had to kiss good bye to Godot for this project.

## So, is that it? No more small retro-like games collection?

I mean, I got burned out of trying to many different things and not finding one that worked well for my goals. I could of course, make my own web game engine or use something like PixiJs and other libraries to assemble something to do a full game, but I didn't have enough time to do all of that.

I believe it was around August of 2024 that I put this project in the backburner and switched to fully focus to my career advacement at my Job. Good news, around December, my efforts at my job paid-off so I was finally one less thing to worry about.

I also completed a personal matter related milestone this January of 2025, which fully freed me of immediate things to worry about. Yes, that means I returned to this project.

## Returning to the sword in the Stone

So, Godot was a bust for my needs, but then it hit me. What if... I don't need a bitmap font for retro like text in a web game? Did I not learn in a previous job that scaling up text from Canvas is not good? That it is better to use html text?

Then I returned to excaliburjs, and there it was, the holy grail I needed: [Web Fonts](https://excaliburjs.com/docs/web-fonts)

Sure enough, that was it! I could even style the text using that. ExcaliburJs was removed from the stone and the Project was alive again!

## What's next?

I did more progress with ExcaliburJs. I managed to prototype more of the shoot 'em up and I'm in a good place to start assembling the mini game. I have completed the lore / story the game is going to have, and decided upon the style the enemies will have.

I have taken a detour making a RPG Maker MZ plugin, since that was small enough to test some things I wanted, but this post is long enough as it is, so, I'll leave that for, well, another post!

Thanks for reading my journey trying to start making a web pixel art retro-like game. Take care and have a nice day! Until the next time.

P.D: No, I won't take another year for my next post. I'll talk about that RPG Maker MZ plugin and I'll put it under projects too. Look forward to it
