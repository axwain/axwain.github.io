+++
title = "Mento Rin Editor and Plugin for RPG Maker MZ"
date = 2025-07-23T06:00:00-05:00
description = "I dabbled into making RPG Maker MZ Plugins and this is the result"
+++

Hey, I said [I won't take another year](@/posts/web-game-dev-journey.md#what-s-next) for the next post.

Hello to anyone reading this. Thank you! I appreciate it. I hope things are going relatively well. Some things happened on my end, mostly related to my job. I entered crunch mode, of my own volition, because I set a goal I wanted to achieve. Unfortunately, I didn't achieve it, but nevertheless, March and April were very busy months. I took May off from Side Projects, while I adjusted to my new team and took rest from that crunch mode. My current team at my job is a wonderful one! I'm learning new stuff and I have been able to apply lots of things I have learned. But enough chit-chat. Let's talk about the [Mento Rin Editor](@/projects/mento-rin-editor.md)

## Curiosity led to RPG Maker MZ Plugins

Ever since I saw a video about RPG Maker from [Sonata](https://www.youtube.com/@Shushiroto/featured) (see below) I got bitten by the curiosity bug when I heard that recent versions use Javascript for scripting instead of Ruby. I couldn't shake the feeling of wanting to see what was all that about.

{{ youtube(id="wjYB_ppZDD0", caption="Sonata's video about RPG Maker") }}

It was around February of 2025, there was a Steam sale and RPG Maker MZ was cheap for grabs. I was at my edge until [Noree](https://x.com/noreemii), and indie game developer and artist I follow through a friend mentioned about a custom save screen for a RPG Maker MZ game. And that was the "excuse" I needed to get RPG Maker and get into learning about making plugins for it.

Part of it was due to the quote of much it would have cost get the plugin made. I thought it was too much, so I set another goal of testing if it was a fair price (spoiler, it is a fair price, surprisingly enough). Driven merely by my curiosity and desire to learn, I accepted making the custom plugin in exchange of Noree testing the web mini games I'm making.

## Version 1 of Mento Rin Save Screen

Pleasantly enough, RPG Maker MZ Projects are basically vanilla Javascript web projects, using [NW.js](https://nwjs.io) for the Desktop version of the game. The core library source code was there to read, and with it, the sample save screen plugin and some documents online (which later I learnt were a translation of the Japanese documentation) I started experimenting. One big plus was that RPG Maker MZ uses [Pixijs](https://pixijs.com) for handling the core of the game. They use version 5, which I used in the past to make playable ads in a previous job. I felt right at home, sorta.

In about 20 hours, I was able to complete the version 1 of the plugin. It has most of the features needed. One thing that was not good, was RPG Maker's limited custom UI for the plugins. Unlike engines like Godot or Unity, it is not possible to extend the UI to add custom controls. That made editing the parameters of the plugin sort of hard.

To make things worse, the way to define UI controls for parameters of the plugin, is through special comments, yeah you read that right, COMMENTS, in the code. Most of the themes I use in VS Code put the comments in a low contrast color which made the experience of writing them not that good.

## What I missed

I definitively missed a lot Typescript. I ran into a bunch of errors due to typos in the code, like putting an uppercase X instead of a lower case one. Another common error was putting the arguments in a function in the wrong order, or accessing properties in objects that have not been initialized yet (there are like four steps for that: constructor, initialize, create and start). These error will surface again when dealing with version 2 of the plugin.

Nevertheless, what I missed the most was having a more visual way of editing things. It was tedious having to edit the values, then run the game, and entering the Save Screen to get the plugin code to run and see if things look good or not. It was possible to use a white board and calculate more or less the values needed, but I was not satified with that. I got an idea

## The Custom Editor

Well, since RPG Maker uses Pixi.js and NW.js, I thought I could use those too for the custom editor. What I needed was a nice UI Component Library to edit all those parameters and embed a preview for them.

One of the things I have wanted to use in a project was the [Play Canvas UI](https://github.com/playcanvas/pcui), which is the open source UI component library used by the [Play Canvas engine](https://playcanvas.com/). It was like the perfect tool for the job. I could build an editor for the parameters of the plugin, and even show a preview of how it would look in RPG Maker. It has a [color picker](https://playcanvas.github.io/pcui/storybook/?path=/docs/components-colorpicker--docs) a [tree view](https://playcanvas.github.io/pcui/storybook/?path=/docs/components-treeview--docs) and a React version!

I started to work on the Editor, to create a Proof of Concept as soon as possible. I quickly created a React-Typescript project with Vite, and oh boy, I was in for a Ride.

### The first issues

Everything was going well. I managed to create some forms to edit the parameters, and sort of linked them with the Tree View, but that Tree View was the downfall of PCUI for this project. For some reason, it refused to behave as expected. It didn't start with the element selected, it didn't store the state of expanded items, it refused to let an item to be selected outside of it. I couldn't figure out in a time box why that was happening, and I didn't want to spend time digging into the issue, I wanted to build a Proof of Concept, so, I decided to ditch the work done until that point and search for an alternative Component Library.

### Promising Replacement

I found out about [Ant Design](https://ant.design/). It has a [color picker](https://ant.design/components/color-picker) that supports selecting colors for gradients, and a [Tree](https://ant.design/components/tree) component too. The Tree component has no support for right click, like the PCUI one, but I was willing to do that trade-off if it behaved the way I wanted:

- it should allow to manually set which items are expanded
- it should allow to set non-selectable items
- it should allow to manually set which item is selected

I went ahead and tested that I could do all of that and yes, it supports all of that. I settled to use Ant Design for the project and migrated everything I had from PCUI to it. I had to learn about its [Form](https://ant.design/components/form) component and hooks. It has a curious set of features.

_Side Note_: I learned a couple of things from what Ant Design did for their component library. I didn't know it was acceptable or even possible to sent React Components in Props. This later helped my at my job to create a nice pattern that removed the headache of updating some tests and documentation.

Progress was going well as I could also create a React component that wrapped a Pixi App view. The next step was how I was going to communicate between Pixi.js and the React Application.

### Zustand hired for managing state

I definitively didn't want to use React's Contexts to manage state, or to sent the functions to edit the values in Pixi.js. They aren't exactly built for that use case, not to mention that either a context with a lot of states was needed, or tons of contexts were going to be created for each part that needed editing. Neither of those were appealing to use.

After searching about Redux and some alternatives, I stumbled upon [Zustand](https://github.com/pmndrs/zustand). I saw the examples to how to communicate state between React and elements outside of React. I did a quick test, and it worked like a charm.

I decided to use Zustand to handle the state in the application. There was a lot boilerplate and repetitive code involved, due to how the functions for modifying state were accessed and defined, but it was certainly better that using tons of contexts. That was until...

### Preact Signals enter the ring

After talking with some friends, I learned about [Preact Signals](https://preactjs.com/guide/v10/signals/). They were way leaner than Zustand (i.e., require less code) and I could set them up faster. I ended up preferring them over Zustand for this project, and then migrated from using Zustand to Preact Signals. Laters, I also replaced React useState hooks with useSignal instead.

I still need to learn to use them effectively, since I was still running into a lot of component redraws, and the `effect` function in the Pixi.js side of things was triggering changes like crazy, but no time to do that, mostly because I wanted to finish this project, and second because I didn't want to replace NW.js for a better view of what was happening with it.

### Issues with NW.js

For a future project, I'll choose Electron instead of NW.js. The developer experience was less than ideal with NW.js. Not having access to Node typing, was making hard the integration for operating with files. I managed to get everything working, but it wasn't a nice experience at all.

The hot reload didn't work, and there are these two quirks of NW.js that were more or less annoying but I got used to them. The first was that if I had dev tools open and then reloaded NW.js to read the newest build of the editor code... It will load in the Dev Tools the App, and the previous window will go to Google Search. I had every time, close the dev tools, then reload, and re-open the Dev Tools. The second quirk was that the application crashed and burned if the build was not ready. It should not crash for not finding the files upon reload, but no, it crashed eveytime. I had to wait for the build to finish and the go to NW.js and hit reload.

I tried to enable hot module reloading instead... And it was basically just running the App's code as a web page, with the Node Context being injected by NW.js after adding a configuration option about it in the manifest file (the package.json). It didn't work smoothly at all. A lot of reported errors that didn't appear at all in the previous (but clunky) way of running the dev builds. The Node parts were not working, like the files were not being read, causing some issues in the application. I later learned that it was mostly due to fontkit, but more on that later.

Thanks to that experience, I gained an appreciation of Electron's way of doing things. Despite not liking the inter communication being done through custom events, Electron's way of doing things allow to a better decoupling between the Desktop application only code and the Web App code.

### Three.js and Effekseer

One feature I wanted to add, was the possibility of adding particles. RPG Maker uses [Effekseer](https://github.com/effekseer/EffekseerForWebGL), so I searched about how to render them with Pixi.js and stumbled upon using [Three with Pixi](https://pixijs.com/8.x/examples/advanced/three-and-pixi). I followed the examples, and I managed to load RPG Maker's Effects in the Editor, but things were going too smoothly, so, something had to fail. Pixi's object were not triggering events! I was not able to select and drag and move the objects at all!

This is not explained in the documentation, and perhaps it is a bug, but the solution for that was to manually hook the EventSystem to the Pixi Renderer. If we inspected the code, the Pixi Renderer was already with an EventSystem, but it was not attached to the canvas in use, so I had to manually create another instance of the EventSystem, and hook that to the canvas. After that, Pixi's objects started to trigger events again.

The funny part of this, is that I didn't end up using this at all in the final release. RPG Maker doesn't like infinite effects, and while I could code a way to handle that as I did it with the editor, the biggest issue was that the copy of Effekseer I had, didn't provide I way to know which assets an effect loads. This is important because I need to mark those assets in the Plugin Parameters as required so they are bundled by RPG Maker. Since it was not possible to get those assets, I switched to use Pixi's barebones Particle Container instead.

## Lessons Learned

There were a lot of things I encountered while making this editor. I ended having a lot more fun than with the Gacha Games I play (in fact, I spent more time with the editor than with the Gacha Games this month!) but despite all of that, there are a few things I would do differently if I did this again.

- _Use Electron instead of NW.js_: I ended up preferring Electron's Developer Experience over NW.js. Although, depending on the project, I may use a native solution or even an IMGUI instead. I'll cross that bridge when the time comes
- _Use the same version of Pixi.js that RPG Maker uses_: I didn't use version 5 due to the security alerts NPM was throwing, but in retrospective I should have used version 5. There are quite a few differences, and the most notorious one is the Text Rendering. Unfortunately, it's not an 1:1 match, and I couldn't find a nice constant to make the position of both renderings nearly the same
- _Use a state management that uses subscribe_: I may need to experiment more on this, but Preact Signal's effect function was not the same as a plain old subscribe. Tons of calls were made, for example, when selecting an object, and tons of calls and rerenders still happened. I'm not sure if it was because I was missing something about its effective use, or because they work way better with Preact than with React
- _Have more consistency_: This one impacted more near the end of the project. Not following the same order of arguments in functions, the same name conventions, and overall the same name patterns between the form fields and the final exported parameters caused issues. These issues were mostly present when using Zod to validate the values and when migrating the code from the editor to the Plugin to use with RPG Maker
- _Implement validation from the beginning_: Yeah, I ended using a log modal to show what values are incorrect. The Zod schema object was very different to how the data was organized for the forms, causing an incredible mismatch. I didn't want to write a mapping option, or going through each input, as I wanted to finish this project and move on. I'll definitively use validation from the beginning in a new project
- _Learn more about animations_: Although I improved coding animations, learning more about them can pay off. There were some issues with the Fast Scroll animation, mainly that some elements of the UI suddenly jumped causing it to look awkward. That was because I was setting only two keyframes for such a fast animation that passes through a lot of places. Only when adding more keyframes, it was possible to smooth the animation and remove the sudden jarring jump

## What's Next?

I had fun with this project, and thanks to it I know I want to do more of this. If I see a job that is for making tools like these, I'll try to get it! But besides that, I'll return to my web cart games. I want to finish those, preferrably before the end of the year.

Outside the Web Cart Games, there's also a project that I will be giving to my brother so he can practice some coding skills. I need to capture the requirements before doing the hand over.

Oh, and about the Web Cart Games, I'll definitively try some good ol' SDL for making a port that natively runs on an [Anbernic's RG35XX H](https://handhelds.wiki/Anbernic_RG35XX_H). I finally got one of those, I purged the included SD Card, and installed MuOS. I'll try Knulli later to see how it behaves for running native games. I installed some games I own from steam to Port Master. Too bad La-Mulana requires a DRM-free version to run into that device. Shovel Knight and Stardew Valley run like a charm there!

That will be all for now. I'll come back again to write sort of a post-mortem of a project at my job, which requires stripping some details and being generic-sounding unfortunately, but nevertheless, it will be good to gather my thoughts about that project and record what I will do better for next time. See you later!
