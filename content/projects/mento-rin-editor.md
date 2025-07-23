+++
title = "Mento Rin Editor"
date = 2025-07-22T23:23:00-05:00
description = "A RPG Maker MZ UI Editor for configuring a custom plugin"

[extra]
icon="/icons/mentorineditor.png"
+++

{{ youtube(id="4QbVOeGSBfk") }}

### How it started

I got curious about the recent versions of RPG Maker, ever since I heard about it switching to use Javascript for plugin development instead of Ruby. Not helping at all, is that there was a Steam Sale at the time and RPG Maker MZ was cheap to get. What seal the deal was an [Indie Dev an artist I follow](https://x.com/noreemii) was looking to get a custom plugin done.

That was the perfect excuse. I reached out, asked about what was needed for the plugin and I was like: Yeah, I can do that, it seems like a good first plugin to do. Lo and Behold, I purchased RPG Maker MZ, created a project and started looking into how to do Plugins.

Surprisingly enough, the Project had basically all the Javascript in plain sight to use. The core library used and all of that. Turns out, it's just a web project using NW.js for creating the final executable. Pretty straight forward, more or less. What surprised me the most was the use of [Pixi.js](https://pixijs.com) version 5.3.12 as the core library for the project. I used that library to create playable ads at a job I had, which make feel right at home when looking at the code.

### The motivation

After creating the first version of the plugin, the major issue was the lack of a visual way, or even basic controls (like one for colors other than the ones in System) to customize the parameters of the plugin. I decided to do a Proof of Concept of creating a visual editor for the plugin, and it worked. I could read values from the RPG Maker project, and even modify them! This kickstarted the creation of the MentoRinEditor. It is technically still a proof of concept, although very long, as I used it to learn a lot and see what is possible.

#### Tech stack

The Tech Stack was more or less tied to what RPG Maker MZ uses:

- [NW.js](https://nwjs.io) for creating the executable of the editor and allow to leverage my knowledge of web development to make it
- React 18 from a Vite Template along with Typescript for building the UI
- [Ant Design](https://ant.design/components/overview) as the component library to put styling and a standard theme for the editor
- [Pixi.js](https://pixijs.com) version 8 as the library to build the preview of what the plugin does. This was due to the version 5 being reported with a security vulnerability
- [Preact Signals](https://preactjs.com/guide/v10/signals/) for managing and sharing state between Pixi and React
- [Fontkit](https://www.npmjs.com/package/fontkit) for reading the metadata in font files. This allowed me to get the family name from a font file and use it to load it using the Browser's Font API
- [Mustache](https://mustache.github.io/) for templating the exports of the plugins.js file and for the version 2 of the plugin
- and last but not least, [Zod](https://zod.dev/) for validating the plugin parameters configured in the editor

These were the final dependencies I ended up using for a release of the project. Unfortunately, I can't share the code. The project is private, but I learned a lot from it. It even helped with some things at my current job, like being able to select Preact Signals for a job project since I tested Zustand and Preact Signals thanks to the MentoRinEditor.
