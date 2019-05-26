---
layout: post
title: "Peer-to-peer file transfers for Dino"
tags: gsoc
permalink: /blog/peer-to-peer-file-transfers-for-dino
---

Hello!

I'm hrxi, a mathematics student from Germany. I got accepted into Google Summer
of Code with the [XMPP software
foundation](https://xmpp.org/about/xmpp-standards-foundation.html) as the
mentoring organisation. In the next three months, I'm going to work on
[Dino](https://dino.im/), a modern [XMPP](https://en.wikipedia.org/wiki/XMPP)
client for the desktop.

The goal of this summer of code project is to implement peer-to-peer file
transfers in Dino, in addition to the HTTP file transfers that are already
available in the client. Peer-to-peer file transfers will allow faster and
larger file downloads if both clients are online at the same time.

XMPP is a messaging protocol. For the user, it tackles a similar problem as
other messengers, such as Signal, Telegram or WhatsApp. Its inner workings are
quite different though: It is as an *open standard*, so anyone can write client
and server implementations. Unlike the above messengers, it is also
*decentralized* in a way that allows anyone to run their own XMPP server, and
more importantly still allows you communicate with the rest of the XMPP world –
quite similar to how email works.

Modern XMPP usage consists of many XMPP Extension Protocols (XEPs). The basis
for the underlying connection between clients will be the Jingle protocol
([XEP-0166](https://xmpp.org/extensions/xep-0166.html)). This protocol is also
the basis for audio/video calls in XMPP, so implemting it will allow other
people to build support for these on top of that work. The peer-to-peer file
transfer will follow the Jingle File Transfer protocol
([XEP-0234](https://xmpp.org/extensions/xep-0234.html)).
