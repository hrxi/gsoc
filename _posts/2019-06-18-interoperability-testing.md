---
layout: post
title: "Third week: Interoperability testing"
tags: gsoc
permalink: /blog/interoperability-testing
---

I managed to get the basic in-band bytestream file transfers working. It still
doesn't have any user interface, that's depending on a project by my mentor.
For now, it's simply triggered by a hardcoded target JID that gets a file
transfer when it sends a presence.

After finishing the proof of concept, I added some error handling and did some
code cleanup. With that done, I did some interoperability testing. With
[Gajim](https://gajim.org), the file transfers now worked well, so I moved on
to test with [Conversations](https://conversations.im/). Here, something funny
happened, my Dino code sent (abbreviated for brevity):

```xml
<iq type='set'>
  <jingle action='session-initiate'>
    <content>
      <description xmlns='urn:xmpp:jingle:apps:file-transfer:5'>
      	…
      </description>
      <transport xmlns='urn:xmpp:jingle:transports:ibb:1' block-size='4096' />
    </content>
  </jingle>
</iq>
```

And Conversations responded:
```xml
<iq type='set'>
  <jingle action='session-accept'>
    <content>
      <description xmlns='urn:xmpp:jingle:apps:file-transfer:5'>
      	…
      </description>
      <transport xmlns='urn:xmpp:jingle:transports:s5b:1'>
        <candidate host='146.255.57.229' type='proxy' jid='proxy.jabber.ccc.de' port='5224' />
      </transport>
    </content>
  </jingle>
</iq>
```

In essence, my Dino code asked to open a Jingle session for a file transfer,
using in-band-bytestreams as the transport. Then, Conversations responded that
it accepts my offer of Socks 5-based Jingle transport for the file transfer,
accepting an offer I didn't make. I *think* this is a violation of the Jingle
XEP ([XEP-0166](https://xmpp.org/extensions/xep-0166.html)): If the other party
wants to negotiate a different transport, it should probably use the
`transport-replace` Jingle action. I created an issue
([Conversations#3478](https://github.com/siacs/Conversations/issues/3478))
which was quickly resolved. I have yet to test the fix, I don't have a
development setup for Conversations yet.

The in-band bytestream file transfers I tested were quite slow (<2KB/s),
probably some rate-limiting of the servers, I didn't investigate furtherly.
