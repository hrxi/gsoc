---
layout: post
title: "Windows support for Dino"
tags: gsoc
permalink: /blog/windows-support-for-dino
---

Hello, I'm back!

It's been four years since I participated in [my first Google Summer of
Code](the-end). I'm hrxi, a mathematics student from Germany. I got accepted
into this year's Google Summer of Code program with the [XMPP software
foundation](https://xmpp.org/about/xmpp-standards-foundation/) as the mentoring
organisation. I chose the [extended
timeline](https://developers.google.com/open-source/gsoc/timeline), so for the
next 22 weeks, I am going to work on [Dino](https://dino.im/), a modern
[XMPP](https://en.wikipedia.org/wiki/XMPP) client for the desktop.

The goal of this summer is to port the previously Linux-only client to
[Microsoft Windows](https://en.wikipedia.org/wiki/Microsoft_Windows) in order
to reach more users who want to use a modern XMPP client on the desktop.

Quoting myself from last time:
> XMPP is a messaging protocol. For the user, it tackles a similar problem as
> other messengers, such as Signal, Telegram or WhatsApp. Its inner workings
> are quite different though: It is as an *open standard*, so anyone can write
> client and server implementations. Unlike the above messengers, it is also
> *decentralized* in a way that allows anyone to run their own XMPP server, and
> more importantly still allows you communicate with the rest of the XMPP world
> – quite similar to how email works.

Dino is written in
[Vala](https://en.wikipedia.org/wiki/Vala_(programming_language)) and uses many
libraries from the Gnome ecosystem. All of these make it possible to target
Windows. In fact, there's already an inofficial Windows port at
https://github.com/LAGonauta/dino.

In order to make building on Windows easy, a
[Meson](https://en.wikipedia.org/wiki/Meson_(software)) build will be added
which will first complement and then replace the existing
[CMake](https://en.wikipedia.org/wiki/CMake) build system. Meson has a better
cross-platform story and is more integrated into the Gnome ecosystem.

Afterwards, all of the building parts of Dino will be built on Windows,
starting at easy libraries like `qlite` which only depends on
[SQLite3](https://en.wikipedia.org/wiki/SQLite), later doing the UI parts which
depend on [GTK4](https://en.wikipedia.org/wiki/GTK), and then ending with the
plugins which have increasingly difficult-to-build dependencies like
[libsoup](https://wiki.gnome.org/Projects/libsoup),
[libnice](https://libnice.freedesktop.org/) or
[GStreamer](https://en.wikipedia.org/wiki/GStreamer).

Finishing up the Windows support, I am going to make Dino buildable on Windows
CI and create a [Windows Store](https://en.wikipedia.org/wiki/Microsoft_Store)
compatible build and fixing known (in the existing inofficial port) and unknown
bugs in Windows support.

Finally, unrelated to porting to Windows, I am going to work on support for
keyrings and
[SASL](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)
[SCRAM](https://en.wikipedia.org/wiki/Salted_Challenge_Response_Authentication_Mechanism).

Timeline
--------

All the times specify by what time the sub-tasks are supposed to be done.

- 2023-06-19: All plugins ported to Meson.
- 2023-07-10: Dino without plugins buildable on Windows.
- 2023-08-28: Plugins buildable on Windows.
- 2023-09-04: Windows CI
- 2023-09-11: Windows Store
- 2023-09-25: Major bugs fixed.
- 2023-10-02: Keyrings
- 2023-10-09: SASL SCRAM

This leaves a couple of weeks of buffer at the end of the coding period which
can be used to tackle minor issues with the Windows build.
