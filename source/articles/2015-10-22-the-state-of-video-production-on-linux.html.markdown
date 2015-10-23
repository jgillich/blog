---
title: The state of video production on Linux
date: 2015-10-22 21:55 UTC
tags:
---

As a regular listener of the Linux Action Show and similar shows JB produces,
I've always known video production on Linux isn't the best experience ever.
Never would I have imagined how bad things really are.

I have a really simple task. Record a video, add a few titles, done. Doesn't sound hard,
does it? Well, apparently it is - on Linux. I tried pretty much all editing software
that is available:

## Pitivi

Pitivi looks like a modern GNOME 3 app, great. Adding titles was very simple,
unfortunately the app freezes every two clicks and you have to kill it.

## Blender

Blender can actually do video editing, but Fedora does not compile it
with ffmpeg support, so it supports zero formats. From what I've read its really
not the best editing sofware anyway, so I didn't bother building it myself.

## Kdenlive

Great features, but some of them hidden at odd places. It has the potential to be
really great, but sadly I also experienced a lot of crashes - less than Pitivi,
but still, does anyone really work with this stuff?

## OpenShot

OpenShot is missing some basic features any program should
have. The actual video editing part is ok (yes, I had issues, but not as bad as with the others),
but you can not ever move any of the files used in a project because they hard code
a million paths in the project file. Paths to python, the local configuration directory
and even the desktop. Open a project file after moving anything and OpenShot just crashes right away.

## Lightworks

The only closed source app here. They announced they would go open source in 2011,
I doubt it's ever going to happen. It seems to be a pretty good software overall,
but the free version is basically demo mode - adding titles requires the paid version,
which I'm not really willing to get at this point.

## What now?

I was able to get the results I wanted with OpenShot. But I don't think I am ever going
to use it again because it is impossible to share the project files with anyone.
Renaming a single folder alone breaks your project, that's just not acceptable.

I have to agree with others that video production on Linux is nowhere near being
viable, unless you buy Lightworks. Kdenlive is probably the best open source
editor out there, but you still have to deal with it crashing, a lot.
