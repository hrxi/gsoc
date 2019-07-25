---
layout: post
title: "Eighth week: Standards"
tags: gsoc
permalink: /blog/standards
---

Sometimes, XEPs are really imprecise or even lack information about some
interactions. Most of the time, it's about error handling where it's not really
specified what to do in error cases, as the XEP mostly deals with the "happy
path" when everything is working.

This week, I tried to get SOCKS5 bytestreams (S5B,
[XEP-0065](https://xmpp.org/extensions/xep-0065.html),
[XEP-0260](https://xmpp.org/extensions/xep-0260.html)) working for file
transfers. Most of the stuff simply worked after implementing the S5B
transport, since the Jingle module was offering a transport-agnostic bytestream
to the outside world, or in this case Jingle file transfers
[XEP-0234](https://xmpp.org/extensions/xep-0234.html). However, file transfers
with Conversations got stuck at 100%. After some debugging, I found out that
Conversations doesn't close the SOCKS5 connection after sending the file. Since
I relied on the the peer closing the connection after sending the complete
file, this led to Conversations waiting for me to acknowledge the file transfer
and me waiting for Conversations to close the connection. I opened
[Conversations#3500](https://github.com/siacs/Conversations/issues/3500) about
this inconsistency.

Reading into [XEP-0234](https://xmpp.org/extensions/xep-0234.html), I tried to
find out how to detect the end of the file transfer. The two relevant sections
I found were

>Once a file has been successfully received, the recipient MAY send a Jingle
>session-info message indicating receipt of the complete file, which consists
>of a `<received/>` element qualified by the
>`'urn:xmpp:jingle:apps:file-transfer:5'` namespace. The `<received/>` element
>SHOULD contain `'creator'` and `'name'` attributes sufficient to identify the
>content that was received.

[(8.1 Received)](https://xmpp.org/extensions/xep-0234.html#received)

and 

>Once all file content in the session has been transfered, either party MAY
>acknowledge receipt of the received files (see Received) or, if there are no
>other active file transfers, terminate the Jingle session with a Jingle
>session of `<success/>`. Preferably, sending the `session-terminate` is done
>by the last entity to finish receiving a file to ensure that all offered or
>requested files by either party have been completely received (up to the
>advertised sizes).

[(6.2 Ending the Session)](https://xmpp.org/extensions/xep-0234.html#sect-idm46562772093488)
(link might die in the future, has a weird anchor).

I wasn't able to find the relevant information, so I looked at Conversation's
source code to find out how it determines when the file transfer is complete.
Turns out it's simply checking whether it has already read as many bytes as the
integer in the `<size>` field in the initial file offer. Adding that as a
"filetransfer complete" condition, I can now receive files from Conversations.
This however, is not a general solution, as the `<size>` field only SHOULD be
present when offering a file, so we can't rely on it being available when
receiving files over Jingle.

If anyone knows what the correct way to detect a completed file transfer is,
please tell me.
