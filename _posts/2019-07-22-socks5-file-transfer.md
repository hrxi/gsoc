---
layout: post
title: "Seventh week: Socks 5 file transfer"
tags: gsoc
permalink: /blog/socks5-file-transfer
---

The blog posts have slipped somewhat, last blog post covering two weeks. Let's
try to fix that.

The next step for file transfers was to implement more Jingle transport
methods. Currently, only [in-band bytestreams are
implemented]({{site.baseurl}}/blog/socks5-file-transfer) as transport methods.
The inefficiencies have been discussed already, it mostly boils down to the
fact that in-band bytestreams exchange base64-encoded data over the XMPP
stream.

The next transport method to be implemented are
[SOCKS5](https://en.wikipedia.org/wiki/SOCKS) bytestreams (S5B,
[XEP-0065](https://xmpp.org/extensions/xep-0065.html),
[XEP-0260](https://xmpp.org/extensions/xep-0260.html)). They can work in direct
(peer-to-peer) or mediated (via a proxy server) mode, depending on whether the
clients are visible to each other or whether they want to share their IP
address.

[SOCKS5](https://en.wikipedia.org/wiki/SOCKS) is a relatively simple protocol,
it can basically be implemented after reading its [Wikipedia
page](https://en.wikipedia.org/wiki/SOCKS): After establishing a
[TCP](https://en.wikipedia.org/wiki/Transmission_Control_Protocol) connection
to the proxy server, the client sends its available authentication methods, the
server chooses one, the authentication happens. After successful
authentication, the client specifies where it wants to connect to and the
server acks that. Thereafter, one can use the TCP connection as if it was
connected to the previously specified IP address, sending and receiving data as
if one were connected directly.

SOCKS5 bytestreams use this protocol in a somewhat peculiar way. Instead of
specifying IP addresses to connect to, the SOCKS5 proxy more or less acts like
a rendez-vous point. Both sides that want to use the proxy as mediator connect
to it, and specify a common but somewhat random fictional domain name to
connect to. After one of the clients "activates" the connection via XMPP, data
can flow between these clients.

In theory, this is just another Jingle transport method, so one wouldn't have
to modify the Jingle code to add it. However, since we only have one transport
method yet, the abstractions are probably not quite right yet: For example, S5B
needs support for `<jingle action="transport-info">`, which IBB didn't.

Let's see how this works out.
