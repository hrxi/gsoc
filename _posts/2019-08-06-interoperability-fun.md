---
layout: post
title: "Ninth and tenth week: Interoperability fun"
tags: gsoc
permalink: /blog/interoperability-fun
---

After finishing the SOCKS5 bytestreams transport for Jingle (S5B,
[XEP-0065](https://xmpp.org/extensions/xep-0065.html),
[XEP-0260](https://xmpp.org/extensions/xep-0260.html)), I was asked whether I
had already done interoperability testing with other clients for the fallback
to in-band bytestreams.

>flow: […] which other transports do you support? How far has interoperability testing between different implementations be done?
>
>hrxi: only socks5 and ibb, conversations and gajim both work fine
>
>flow: […] did you also test the socks5 to ibb fallback?
>
>hrxi: no, that doesn't work yet
>
>flow: uh, maybe you find the time to implement that in the remaining two weeks, or are there other plans?

I guess I should've seen it coming at this point. Here's how the fallback
should work, according to the Jingle SOCKS5 XEP
([XEP-0260#Fallback](https://xmpp.org/extensions/xep-0260.html#fallback)),
excluding acks and simplified (dropping a lot of elements that need to be
present):

```

1. <jingle action="session-initiate"> <transport xmlns="j-s5b" /> </jingle>
                                 ==>

2. <jingle action="session-accept"> <transport xmlns="j-s5b" /> </jingle>
                                 <==

3. <jingle action="transport-info"> <transport xmlns="j-s5b"> <candidate-error /> </transport> </jingle>
                                 <=>

4. <jingle action="transport-replace"> <transport xmlns="j-ibb" /> </jingle>
                                 ==>

5. <jingle action="transport-accept"> <transport xmlns="j-ibb" /> </jingle>
 				 <==

6. <open xmlns="ibb" />
                                 ==>
```

First, the normal Jingle session initiation happens, the client offering a file
sends a `session-initiate` including SOCKS5 proxy information and waits. Then,
when the receiving user accepts the file, or if the receiving client
automatically accepts the file based on some conditions, it sends a
`session-accept` including its SOCKS5 proxy information.

In point 3, we start to deviate from the normal "happy path"; in order to test
the fallback, I made Dino send no local candidates and skipped checking all of
the remote candidates, leading to both clients sending a `transport-info` with
a `candidate-error`, meaning that none of the candidates offered by the peer
work.

Now (4), according to the XEP, the *initiator* (left side) should send a
`transport-replace` to change the transport method to in-band bytestreams.

In 5, the responder accepts this change.

After getting this response, the initiator is supposed to open the in-band
bytestream ([XEP-0261#Protocol
flow](https://xmpp.org/extensions/xep-0261.html#protocol-flow)) to complete the
negotiation.

It took a few tries until I had Dino-Dino fallback working. The other clients
were also fun:

**Dino → Conversations** The best case. Fails in step 2, but only because of
some minor problems, the block size negotiation doesn't take into account what
Dino sent. Dino asks for a block size of 4096 bytes, Conversation is only
allowed to lower this value but sets the consensus to 8192 bytes. I ignored
this and the fact that Conversations did not send a `sid` and got a working
fallback.
[Conversations#3515](https://github.com/siacs/Conversations/issues/3515).

**Conversations → Dino** <s>Fails in step 4, for some reason Conversations
doesn't send a `transport-replace` after the SOCKS5 transport failed.
[Conversations#3514](https://github.com/siacs/Conversations/issues/3514). </s>
**EDIT** Seems to be my mistake, couldn't reproduce it when trying to test it
with the Conversations developer.

**Dino → Gajim** Fails in a funny way, in step 5. Gajim responds to
`transport-replace` with a `session-accept`. `session-accept` is only valid in
response to `session-initiate`, so I don't know how that happens. Some
Conversations user already reported that issue.
[Gajim#9692](https://dev.gajim.org/gajim/gajim/issues/9692).

**Gajim → Dino** Gets stuck in step 6, Gajim doesn't open the in-band
bytestream even though it is the initiator.
[Gajim#9784](https://dev.gajim.org/gajim/gajim/issues/9784).

Of course I'm not sure that I diagnosed all of these issues correctly, these
might be mistakes on my part and in my code, too. Let's see how these issue
threads develop.

**EDIT** And of course there was at least one mistake by me, Conversations →
Dino seems to work.
