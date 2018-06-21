---
title: "Compress pdf file with linux"
date: 2018-06-21T11:00:27.143+02:00
subtitle: "A good command line ghostscript command to reduce the size of big pdf files"
author: Gabriele Teotino
tags: ["pdf", "ghostscript"]
categories: ["command line"]
draft: true
---

A pdf files with a lot of scanned images is a common problem.

I reduced a file from 307MB to 68MB with limited quality loss.

<!--more-->

The original [askubuntu answer](https://askubuntu.com/a/256449).

Use the following [ghostscript](http://ghostscript.com/doc/current/Ps2pdf.htm) command:

```shell
    gs -sDEVICE=pdfwrite -dCompatibilityLevel=1.4 -dPDFSETTINGS=/screen -dNOPAUSE -dQUIET -dBATCH -sOutputFile=output.pdf input.pdf
```

- `-dPDFSETTINGS=/screen` lower quality, smaller size.
- `-dPDFSETTINGS=/ebook` for better quality, but slightly larger pdfs.
- `-dPDFSETTINGS=/prepress` output similar to Acrobat Distiller "Prepress Optimized" setting
- `-dPDFSETTINGS=/printer` selects output similar to the Acrobat Distiller "Print Optimized" setting
- `-dPDFSETTINGS=/default` selects output intended to be useful across a wide variety of uses, possibly at the expense of a larger output file

I tested this approach with a document full of scanned pages. The original file was 306.7MB, the results of the different quality setting are:

method  |size MB|difference MB|difference %|quality
--------|-------|-------------|------------|---------
screen  |   25.1|       -281.6|          8%|shit
ebook   |   68.4|       -238.3|         22%|good slight loss
default |  221.8|        -84.9|         72%|no visible difference
printer |  340.7|        +34.0|        111%|-
prepress|  512.2|       +205.5|        167%|-
