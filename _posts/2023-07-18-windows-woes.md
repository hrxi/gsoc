---
layout: post
title: "Windows woes"
tags: gsoc
permalink: /blog/windows-woes
---

Sorry for the radio silence.

Progress report: I ported the Dino build to Meson (while maintaining the CMake
build) and made a pull request, [#1453](https://github.com/dino/dino/pull/1453)
for it. In the process of porting, I found out that some plugins were
unnecessarily split into two parts, a Vala-wrapper for a library and the actual
plugin. I merged `gpgme-vala` into `openpgp` and `signal-protocol` into
`omemo`, simplifying the build process. Now each subdirectory of `plugins` is
its own plugin. ;)

Then I moved on to the Windows environment. In theory, the goal is to compile
everything in a "normal" Windows environment, without
[WSL](https://en.wikipedia.org/wiki/Windows_Subsystem_for_Linux) and
[MSYS2](https://en.wikipedia.org/w/index.php?title=Mingw-w64&oldid=1165513393#MSYS2).
However, I quickly ran into problems.

Vala distribution
-----------------

There seems to be no easily downloadable native Vala binaries for Windows.
There's a project page
[ValaOnWindows](https://wiki.gnome.org/Projects/Vala/ValaOnWindows) in the
GNOME wiki that suggests using MSYS2 (which has native Vala binaries but
creates a UNIX-like environment) or using WSL (which is just a nicely
integrated Linux virtual machine, so it's out completely. There's also a small
section about compiling Vala natively, it mostly claims that it's undocumented
and links to a dead page that's still live on the [Wayback Machine:
MSVCCompilationOfGTKStack](https://web.archive.org/web/20160809011235/https://wiki.gnome.org/Projects/GTK+/Win32/MSVCCompilationOfGTKStack).

I went with taking the MSYS2 binaries outside of their environment, which
worked fine.

Compiling a simple Vala library
-------------------------------

Using this, I went on to port `qlite` to Windows. In the introductory post, I
pointed out how this should be easy because it only depends on the
[SQLite](https://en.wikipedia.org/wiki/SQLite) library. However, when trying it
out, I found out that even the more foundational `gee` library doesn't actually
support building with Meson, although there's an open issue
([libgee#25](https://gitlab.gnome.org/GNOME/libgee/-/issues/25)) and a
four-year-old pull request
([libgee!1](https://gitlab.gnome.org/GNOME/libgee/-/merge_requests/1)) for it.
I used the pull request to create a Meson
[WrapDB](https://mesonbuild.com/Wrapdb-projects.html) project, but haven't
asked for its inclusion yet.

In the process of that, a number of issues building the library on Windows
arose. First of all, the Microsoft `stdlib.h` header seems to define the macros
`min` and `max` (lowercase!) unless you compile with the standards-compliant
[`/Za`](https://learn.microsoft.com/en-us/cpp/build/reference/za-ze-disable-language-extensions?view=msvc-170)
mode (note that this is separate from the problem that `windows.h` does the
same unless the `NOMINMAX` preprocessor macro is defined).

Enabling the `/Za` mode however, comes with another problem though. In
standards-compliant mode, MSVC (like gcc with `-pedantic`) rejects empty
translation units which `valac` can generate. I opened
[vala#1457](https://gitlab.gnome.org/GNOME/vala/-/issues/1457) about that.

Additionally, `GOnce` doesn't seem to compile in MSVC
([vala#1459](https://gitlab.gnome.org/GNOME/vala/-/issues/1459), closed as a
duplicate of [vala#1272](https://gitlab.gnome.org/GNOME/vala/-/issues/1272)).

Then there's a smaller problem that I haven't yet reported upstream. Meson only
treats a dependency as a Vala dependency if it's compiled as a Vala dependency
or [if it's a `pkg-config`
dependency](https://github.com/mesonbuild/meson/blob/7824ff80dcaa457706a4f762e976383e6bd03daf/mesonbuild/backend/backends.py#L1044-L1051).
`gee` depends on `gio-2.0` which is available as a WrapDB package but doesn't
ship its own Vala API spec (`gio-2.0.vapi`) because it is just a C library.
`gio-2.0.vapi` ships with the Vala compiler, but Meson isn't looking for it.
