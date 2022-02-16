---
title: "Svg to multisize ico"
date: 2018-05-04T10:43:42+02:00
subtitle: "Convert the github desktop svg logo to a multisize ico file to be used as a folder icon"
author: Gabriele Teotino
tags: ["windows", "ico", "inkscape", "gimp", "icon"]
categories: ["graphics"]
draft: false
---

I want to use the logo of github desktop for a folder. To do so I need to create a ico from the [svg file](https://desktop.github.com/images/desktop-icon.svg) downloaded from [GitHub](https://desktop.github.com/)

The standard icon sizes used by windows explorer are

- Extra large icons 256x256
- Large icons 96x96
- Medium icons and Tiles 48x48
- Content 32x32
- Small icons, List and Details 16x16

But windows uses only 16x16, 32x32, 48x48 and 256x256.

<!--more-->

Open the svg using Inkscape.

- File -> Export PNG image -> Select "Page" and set image size 256x256

Open the png in Gimp.

- Change the layer name to 256
- Duplicate and resize
  - Layer -> Duplicate Layer
  - Be sure that the new layer is selected
  - Change the layer name to 48
  - Layer -> Scale layer -> set to 48x48
- Again
  - Select the 256 layer so that gimp will always resize from the largest image
  - Layer -> Duplicate Layer
  - Be sure that the new layer is selected
  - Change the layer name to 32
  - Layer -> Scale layer -> set to 32x32
- And one more time for 16x16
- Save the file
- Export as ico
  - Leave all settings as 32 bpp, 8-bit alpha, no palette
  - Only for the 256x256 check **Compressed (PNG)**

Now the new ico is usable from explorer to customize my folder.
