+++
title = "Palette Maker"
date = 2024-03-23T13:23:00-05:00
description = "A helper utility created for helping to choose colors for UI Themes!"

[extra]
icon="/icons/palettemaker.svg"
+++

You can access [Palette Maker here](https://axwain.github.io/PaletteMaker)!

I'm not that good at art or color theory, so I created this application to help me out with that. Originally, it was intended only for choosing 16 colors based in the standard unix ones.

I wanted those specific 16 colors because I was installing Archlinux and customizing it for keyboard only use with i3-wm. I ended up creating a small palette used across i3, polybar and Alacritty. The end result was something I was happy enough. ([Dotfiles are avialable here](https://github.com/axwain/dotfiles/tree/main/.config))

React was used to build this web app, and not much personalization of controls was added, to make it as fast as possible. I decided to extend the colors available so I can use them for building the styling for the side projects I'll work on. This website's colors were choosen using Palette Maker v1!

Since this is purely a front-end app, it doesn't save anything, so any user has to manually Copy and Paste the json object with the palette information. One thing I like a lot, if that I can get colors with AA contrast.

#### Tech stack

Nothing too fancy. Vite is used as the build tool, React v18 + Typescript, and, polished + colord for the color operations. Reactivity is not what I would like it to be, but when I revisit this project for an updated version, I'll improve that part.
