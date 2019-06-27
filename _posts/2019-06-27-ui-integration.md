---
layout: post
title: "Fourth week: Some UI integration"
tags: gsoc
permalink: /blog/ui-integration
---

File sending over Jingle (with in-band-bytestream transport) has now been
integrated into the UI. This has fortunately been quite easy as
[Dino](https://dino.im/) already had support for file sending, via HTTP
uploads. Thus I only needed to hook into the existing UI to also offer Jingle
file transfers. This means that now you can click on the "attach" paper clip to
send a file. Since prioritization of different transport methods and file
receiving haven't been implemented so far, these changes aren't in the master
branch yet.

Prioritization is important so that HTTP file uploads are preferred where
possible (file small enough, server supports it): Jingle file transfers only
work to a single target and only when it is online at the same time as the
sender.

Since the ability to receive files via Jingle can also be reasonably expected
if the client supports sending them, we basically have two blockers for
merging.

![Screenshot of Dino showing the paper clip button and a lot of file transfers]({{site.baseurl}}assets/2019-06-27-ui-integration-screenshot.png)

Above you can see the chat window after some testing. ;)
