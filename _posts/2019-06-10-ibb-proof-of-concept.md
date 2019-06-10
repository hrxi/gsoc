---
layout: post
title: "Second week: IBB Proof of Concept"
tags: gsoc
permalink: /blog/ibb-proof-of-concept
---

As the first task, we planned to get a working, proof-of-concept Jingle file
transfer over an "in-band bytestream"
(IBB, [XEP-0047](https://xmpp.org/extensions/xep-0047.html),
[XEP-0261](https://xmpp.org/extensions/xep-0261.html)). To get to this point, I
planned to write a module for basic Jingle interactions, supporting the
necessities for file transfers, and a non-final API for transport methods on
the other side.

What do in-band bytestreams look like? "In-band" means that the file data will
go through the same connection as the rest of the XMPP stream that also
contains your chat messages and metadata like read receipts, etc. It has to fit
into the XML stream, an example with an 28-byte file below:

```xml
<iq xmlns="jabber:client" to="tish@example.com/gajim" from="blagovest@example.com/dino" type="set" id="e8656828-2da8-48a5-828d-804542945851">
	<data xmlns="http://jabber.org/protocol/ibb" seq="0" sid="514ef10b-22e7-4e58-bf46-cca01629e826">
		SGVsbG8sIGluLWJhbmQgYnl0ZXN0cmVhbXMhCg==
	</data>
</iq>
```

As you can see, this is quite inefficient. The `<iq>`/`<data>` tags amount to
roughly 256 bytes. The actual data has to be
[base64](https://en.wikipedia.org/wiki/Base64)-encoded because XML cannot
contain arbitrary binary data, leading to an extra 33% overhead. With the
default blocksize of 4096 bytes as specified in the IBB XEP, we get a total
overhead of 40%. Nevertheless, this transport is simple to implement as it does
not need additional network connections beyond the already existing.

---

While trying different approaches for the Jingle transport API,
[fiaxh](https://github.com/fiaxh/) (my mentor) helped me decide: I'm now using
abstract classes for the different transports, and signals for the users of a
Single session, a pretty Vala-esque approach. After this planning, I went ahead
and tried to realize this in actual code. This resulted in basic session
opening support (only initiating), support for in-band bytestreams and file
transfers.

What's still missing for merging this? I was pretty happy that the code almost
worked after fixing syntax error and obvious oversights. It still has a pretty
serious bug: Currently, when testing the file transfer from Dino to Gajim,
Gajim drops the last chunk we send. Additionally, the code needs proper error
handling, currently it just writes a message to the standard error output.

---

During the weekly organization chat, flow sent me an interesting
[document](https://gist.github.com/legastero/fa80e2366c448fd6e141)
written by someone who implemented the Jingle protocol in multiple clients and
wrote about the "enlightenment" he achieved in doing so. Thanks for that, I'll
check it out the next week.
