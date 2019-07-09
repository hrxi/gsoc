---
layout: post
title: "Fifth and sixth week: File receiving"
tags: gsoc
permalink: /blog/file-receiving
---

In the last two weeks, file receiving over Jingle has been implemented. This
means that basic Jingle file transfers are available in
[Dino](https://dino.im/) now (at least once the pull request
[#577](https://github.com/dino/dino/pull/577) has been merged).

As predicted by the [Jingle implementation
gist](https://gist.github.com/legastero/fa80e2366c448fd6e141), this was
actually a bit more than I would have guessed -- you basically have to
implement the Jingle and file transfer protocol again, but this time from the
other side. Doing so involved some refactoring of the original code in order to
fit better with Dino's internals. I moved the code from [signal-style
callbacks](https://wiki.gnome.org/Projects/Vala/SignalsAndCallbacks) to
[async](https://wiki.gnome.org/Projects/Vala/AsyncSamples)
[`IOStream`s](https://valadoc.org/gio-2.0/GLib.IOStream.html).
