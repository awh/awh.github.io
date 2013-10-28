---
layout: post
title: "(Five Years) Late to the Party"
date: 2013-07-21 09:10
comments: false
categories: 
---
I have obtained a Mac only recently, and even more recently started using it as a workstation for development. As a long time Linux user it has taken a bit of effort to get comfortable, and now that I am I thought I would document my findings in case it helps any remaining straggling refugees from the Desktop Wars:

* **Homebrew** http://mxcl.github.io/homebrew

It won't be long before your're itching to `apt-get install` or `yum install` something. There are a number of options here; MacPorts, Fink and HomeBrew. After a detour through MacPorts (engendered by one foolhardy Sunday morning attempt to add support for Lion fullscreen mode to VirtualBox, but that's another story entirely) I settled on HomeBrew as it seems to do its thing without spray-gunning chaff into your filesystem Windows style. Once you've got it installed you will want to `brew install git mosh mtr nmap pstree rbenv irssi sbt etc etc`

* **TextWrangler** http://www.barebones.com/products/textwrangler

This excellent free editor is a great companion to a full fledged IDE. It supports Lion fullscreen mode and it properly indents soft-wrapped lines, a godsend for those of us unlucky enough to be editing XML (such as DocBook) by hand.


* **Parallels** http://www.parallels.com

As with package management systems there are a number of options here; VMWare, VirtualBox and Parallels. I have been using VirtualBox as part of my Software Defined Networking Coursera, and it seems entirely capable albeit lacking proper Lion fullscreen support (see above). Consequently I tried Parallels and found it to be truly excellent, for supported guests in particular - for example it understands the Ubuntu 12.04 LTS installer well enough to hand hold it to completion, and injects a paravirtualised video driver with hardware accelerated(!) OpenGL support. Being able to three-finger-swipe to a full screen Kubuntu that's indistinguishable from a bare metal install still feels like magic even when you understand how it's done.

* **Scrivener** https://www.literatureandlatte.com/scrivener.php

This is a great tool for research and writing of any kind, including technical authorship - whenever I start a new project I do so in Scrivener and use it to keep track of research papers and whatever else pertains. Incidentally, I was surprised to find that whilst the OSX Preview tool is quite laggy on some large PDFs the viewer in Scrivener is buttery smooth - go figure. Despite being inexpensive Scrivener comes with a generous family licence as standard, an unexpectedly humane gesture which has secured me as a loyal customer for life!

* **Scapple** https://www.literatureandlatte.com/scapple.php

From the same folks as Scrivener, this Ideation Support Tool (I just made that up btw) has become indispensible. Unlike other mind mapping softwares which force you to start from a central concept and link outward in a proscribed fashion, Scapple lets you blast unrelated thoughts into it willy-nilly and then link them together (or not!) at your leisure. Life's a graph, not a tree...

* **OmniGraffle** http://www.omnigroup.com/omnigraffle

Communication and presentation are critically important in conveying design and architecture, so a good diagramming tool is essential. Whilst I have out of necessity achieved some level of proficiency with LibreOffice Draw (enough so that my audience isn't immediately sick for example) in OmniGraffle creating beautiful diagrams is effortless. It's the most expensive item on this list, however they don't gouge you if you buy the standard version first and upgrade to the pro version later (in fact when I checked it was $0.01 cheaper that way!)

* **Divvy** http://mizage.com/divvy/

Having grown up on a diet of [fvwm](http://www.fvwm.org) and [enlightenment](http://www.enlightenment.org) I don't know which I find more alien - the paucity of features in the OSX window manager, or having to pay to rectify the problem :) Divvy lets you quickly move resize windows accurately with either the mouse or key bindings - essential for that cinema display!
