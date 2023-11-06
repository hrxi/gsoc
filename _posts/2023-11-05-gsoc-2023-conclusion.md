---
layout: post
title: "End of the Google Summer of Code 2023"
tags: gsoc
permalink: /blog/gsoc-2023-conclusion
---

This Google Summer of Code was about adding
[Windows](https://en.wikipedia.org/wiki/Microsoft_Windows)/[MSVC](https://en.wikipedia.org/wiki/Microsoft_Visual_C%2B%2B)
support to Dino. [Dino](https://dino.im/) is an XMPP client,
[XMPP](https://xmpp.org/) is decentralized instant messaging standard. It is
written in [Vala](https://vala.dev/), a programming language that integrates
well with the [GNOME](https://www.gnome.org/) ecosystem.

The work was started by porting Dino to the [Meson](https://mesonbuild.com/)
build system; previously, Dino used the [CMake](https://cmake.org/) build
system. Doing this would help the Windows porting efforts, as a lot of
dependencies use Meson, especially the [GNOME](https://www.gnome.org/)
libraries. This porting was done in
[dino#1453](https://github.com/dino/dino/pull/1453) (plus a fix in
[dino#1500](https://github.com/dino/dino/pull/1500)).

As the next step, a base Dino (without any plugins) needed to be built on
Windows. This first involved problems with getting Vala itself to work, and
then fixing a couple of code generation issues
([vala!331](https://gitlab.gnome.org/GNOME/vala/-/merge_requests/331),
[vala!335](https://gitlab.gnome.org/GNOME/vala/-/merge_requests/335) and most
importantly
[vala!345](https://gitlab.gnome.org/GNOME/vala/-/merge_requests/345), not yet
fixed [vala#1483](https://gitlab.gnome.org/GNOME/vala/-/issues/1483)). Meson
also posed a little problem, but it could be worked around ([meson#2103
comment](https://github.com/mesonbuild/meson/issues/2103#issuecomment-1757737948)).

Afterwards, all the dependencies needed to be ported to Windows and made
compatible with Meson. For many GNOME packages, this was already the case. Many
were also already in Meson's [WrapDB](https://github.com/mesonbuild/wrapdb), a
dependency manager for Meson projects. Those that haven't found a place in
WrapDB were contributed there: gee
([wrapdb#1094](https://github.com/mesonbuild/wrapdb/pull/1094),
[wrapdb#1098](https://github.com/mesonbuild/wrapdb/pull/1098)), gdk-pixbuf
([wrapdb#1156](https://github.com/mesonbuild/wrapdb/pull/1156)), gtk4
([wrapdb#1206](https://github.com/mesonbuild/wrapdb/pull/1206)), pango
([wrapdb#1207](https://github.com/mesonbuild/wrapdb/pull/1207)), libadwaita
([wrapdb#1208](https://github.com/mesonbuild/wrapdb/pull/1208)), nghttp2
([wrapdb#1221](https://github.com/mesonbuild/wrapdb/pull/1221)), libsoup
([wrapdb#1222](https://github.com/mesonbuild/wrapdb/pull/1222)),
glib-networking
([wrapdb#1223](https://github.com/mesonbuild/wrapdb/pull/1223)), libpsl
([wrapdb#1260](https://github.com/mesonbuild/wrapdb/pull/1260)),
libsignal-protocol-c
([wrapdb#1276](https://github.com/mesonbuild/wrapdb/pull/1276)), qrencode
([wrapdb#1277](https://github.com/mesonbuild/wrapdb/pull/1277)).

Some upstream bugs were also filed
([libadwaita#739](https://gitlab.gnome.org/GNOME/libadwaita/-/issues/739),
[libadwaita#740](https://gitlab.gnome.org/GNOME/libadwaita/-/issues/740),
[wrapdb#1179](https://github.com/mesonbuild/wrapdb/issues/1179),
[libpsl#216](https://github.com/rockdaboot/libpsl/issues/216)) or pull requests
created
([libsoup!381](https://gitlab.gnome.org/GNOME/libsoup/-/merge_requests/381),
[meson#12394](https://github.com/mesonbuild/meson/pull/12394),
[libomemo-c#9](https://github.com/dino/libomemo-c/pull/9)).

As GnuTLS doesn't support building in MSVC, we decided to try to replace its
use with OpenSSL. This also avoided porting the complicated-to-build gpg-error.
For the most part, the porting to OpenSSL was uncomplicated, however porting
the DTLS/SRTP2 handshake proved non-trivial.

All in all, the most important functionality of Dino (base, http-files, omemo)
works on Windows now. It still lacks polish, for example installing doesn't
work in adequately on Windows yet. The results are currently in the
[pr\_meson\_windows](https://github.com/hrxi/dino/tree/pr_meson_windows)
branch. It's missing support for audio/video calls due to the aforementioned
difficulties with OpenSSL porting.

Additionally, the goal of supporting SCRAM together with keyrings was not
reached.

I'd like to thank my two mentors [fiaxh](https://github.com/fiaxh) and
[mar-v-in](https://github.com/mar-v-in) without whom this wouldn't have been
possible. They were quick to answer me when I struggled with problems.

I'd also like to thank my mentoring organization, the
[XSF](https://xmpp.org/about/xmpp-standards-foundation/) (XMPP standards
foundation) and especially emus who represented it for me.
