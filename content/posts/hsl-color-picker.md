---
title: "HSL Color Picker"
date: 2022-11-09T22:40:36+03:00
draft: true
tags: ["hsl", "color", "colour", "CSS3", "Vue.js", "Javascript"]
---
##### HSL/A Colour Picker

<!--more-->

##### Background

Colour in my web development experience was always represented in the most common formats known to most people, `RGB/A` and `HEX`.
While reading through a book I picked up, I came across a new format: `HSL/A`. I thought I'd give this new format a try.

Diving deep into `HSL` _(Hue, Saturation, Lightness)_ and other related color formats, `HSB`, is way out of the scope of this
short blog post. I'll therefore leave out any esoteric vocabulary that only `colour people` dare use.

Working with `HSL` values as opposed to `RGB/A` in `CSS` was a breeze for once. Take this pair of colors for example:

```css
color: #03369E;
color: #507dd7;
```

```css
color: hsl(220, 95%, 34%);
color: hsl(220, 95%, 34%);
```

They both represent the same pair of close shades of blue. 
The first pair, in `HEX` show so much variance in the values used as opposed the second pair. 
A change to a few shades up/down/left/right, would change the hexadecimal representation to a whole new set of characters which aren't particularly easy to remember.

The [book](https://www.refactoringui.com/) that influenced my shift in color representation format preferences has a line that reads:

> HSL fixes this by representing colours using attributes the human-eye intuitively perceives: hue, saturation and lightness.

Armed with the indispensable knowledge of the different formats that can be used to represent color, I set out to put this newfound knowledge to good use.

##### Colour picker

I scoured the web for a great color picker and to no surprise, I came across some really good ones.
Adobe's [Color Wheel](https://color.adobe.com/create/color-wheel) is the first thing that comes to mind.

I wanted something different though. See, the book said something about preparing a palette of different colours and their respective shades in advance. 
9 was a number the book suggested would be great for the amount of shades of any colour one ought to have.

The goal was to create a generator of sorts. One that could not only produce any colour on request, but could also aid in the creation of a palette of colours.

##### My solution

I must say, there's no shortage of color pickers online, [iro.js](https://iro.js.org/). Nonetheless, I felt the need to build one that's suited to my specific needs.

[Vue.js](https://vuejs.org/) was my framework of choice. The aptly named `HSL Color Picker` manages to achieve the following goals:

- Provides a way to generate `HSL` representations of colors.
- Generates a 9 colour palette on request.
- Provides a means of copying generated colors to the clipboard.

The project is perfect for my daily use. Plus it's relatively straightforward to fire up and use.
I should mention that it's still under a continuous state of improvement and development.

[HSL Color Picker](https://bitbucket.org/japodhidev/hsl-color-picker/) is available in my repository. It's also hosted on `Vercel` at this [location]().

