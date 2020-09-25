---
title: WebRTC URI for remote resource identification and local configuration
abbrev: WebRTC URI
docname: draft-seaduboi-webrtc-uri-latest
date:
category: std

ipr: trust200902
keyword: Internet-Draft

stand_alone: yes
pi: [toc, sortrefs, symrefs]

author:
  -
    ins: S. DuBois
    name: Sean DuBois
    org: Pion
    email: sean@pion.ly

--- abstract

This document describes a Uniform Resource Identifier (URI) that establishes a
WebRTC session. This URI serves two purposes. First it will identify a remote a
WebRTC agent. Second it will configure the local WebRTC agent.

This provides an alternative to the SDP Offer/Answer exchange {{!I-D.ietf-mmusic-sdp-offer-answer}}.
The Offer/Answer is exchanged via a signaling plane that is controlled by the user {{!I-D.ietf-rtcweb-jsep}}.

--- middle

# Introduction

The WebRTC framework specifies protocol support for direct
interactive rich communication using audio, video, and data between
two peers. WebRTC was originally designed with the browser in mind. Usage
of WebRTC has extended far beyond that. This WebRTC URI is designed for users
outside of the browser. In the browser these behaviors could be achieved with
ORTC interfaces.

The flexibility of WebRTC's signaling plane has made adoption difficult for some
use cases.  The signaling plane also presents challenges around security, privacy and
operational burden. However WebRTC's media plane is unambiguous. Once two
WebRTC Agents have enough details from signaling it is easy to establish rich communication.

The signaling plane is only necessary when two clients don't have enough
information to establish a session. In some cases signaling is not necessary. If the
two WebRTC agents agree on some basic details signaling is not required. The greatest difficulty
comes with establishing connectivity between two ICE Agents. {{!I-D.ietf-ice-rfc5245bis}}
If these two ICE Agents use mDNS {{!I-D.cheshire-dnsext-multicastdns}} and are in the same
network you can establish a Zeroconf session.  If one ICE Agent is World Routable connectivity is
also easily possible. The WebRTC URI format does not allow connectivity between two ICE Agents that
would require STUN or TURN.

Multiple sessions can be established using the same set of WebRTC URIs. This document
describes a URI that contains these details.

Discussion of this work is encouraged to happen on the MMUSIC IETF mailing list
<mmusic@ietf.org> or on the GitHub repository which contains the draft:
<https://github.com/sean-der/webrtc-uri>.

## Specification of Requirements

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
"SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and
"OPTIONAL" in this document are to be interpreted as described in BCP 14
{{?RFC2119}} {{?RFC8174}} when, and only when,
they appear in all capitals, as shown here.

# Use Cases
Please note that this section is informational only.

## Use cases for Zeroconf
- Two users can play a P2P multiplayer video game. No backend components
  are required for signaling.

- Two users can exchange a file without depending on a 3rd party server.

- A user can connect to their security camera in the same network.

- A user can cast their desktop or a video to another device.

## Use cases for World Routable host
- A user can publish media real-time to a CDN

- A user can connect to a CDN to watch a real-time video

# The WebRTC URI Scheme
The WebRTC URI takes two possible forms. The only difference is how the
authority is handled.

## Zeroconf
Zeroconf is used for connecting two WebRTC Agents in the same network.
These agents don't need to know a address/port ahead of time since the discovery
is done via mDNS.

    webrtc://<ice-user-fragment>:<ice-password>@<mDNS name>?<parameters>

## World Routable
World Routable URIs are used to connect to a host that is listening on a Address/Port.
The Agent with the URI needs to know the Address/Port ahead of time.

    webrtc://<ice-user-fragment>:<ice-password>@<Address/Port>?<parameters>

## Parameters
The following parameters are recognized, more will need to be added.

* ice-controlling = If Local ICE Agent is Controlling ( BOOL ) (defaults to true )
* dtls-client     = If Local DTLS Agent is Client ( BOOL) (defaults to false)
* local-ice-ufrag = Local ICE Agent's user fragment ( STRING ) (defaults to remote ufrag value)
* local-ice-pwd   = Local ICE Agent's password ( STRING ) (defaults to remote pwd)
* datachannel     = Should a SCTP Assocation be started ( BOOL ) (defaults to true)
* fingerprint     = DTLS fingerprint for local certificate (defaults to previous/configured value for same URI)

# Examples of WebRTC URI Syntax

## Zeroconf
The following two URIs would allow two WebRTC Agents in the same network to connect and have
bi-directional text communication. The first agent would assume the mDNS name `alice.local` and
the second would be `bob.local`.

    webrtc://eu6k:cae6@alice.local?local-ice-ufrag=adu3&local-ice-pwd=eew4&datachannel&ice-controlling&dtls-client

    webrtc://adu3:eew4@bob.local?local-ice-ufrag=eu6k&local-ice-pwd=cae6&datachannel

## World Routable
The following URI allows a WebRTC Agent to connect to a World Routable agent.

    webrtc://eu6k:cae6@example.org:5001?local-ice-ufrag=adu3&local-ice-pwd=eew4&datachannel&ice-controlling&dtls-client

# Sharing URIs
The typical flow for crafting and sharing WebRTC URIs will differ depending on the use case.

## Zeroconf
For Zeroconf the two WebRTC Agents will need to agree on details ahead of time. The user will
pick two unique mDNS names ahead of time and other details.

In some cases you could have a device with a static name. It wouldn't need to know any details
about the remote peer. You could have `my-security-camera.local`. Anyone could connect if they
know the ICE authentication details.

## World Routable
A World Routable URI would be shared by the owner of the World Routable WebRTC Agent. This is
something that a user could copy out of a dashboard and then could input into their local
user agent.

# Security, Privacy and Availability Benefits of WebRTC URIs
Without the WebRTC URI a signaling plane is required to establish a WebRTC session.

If an attacker is able to modify messages in the signaling plane they could MITM a WebRTC
Session.  The attacker could replace the candidates and certificate fingerprint with their
own. They could then route all traffic through a host they control. The two users would connect
to each other and not realize that anything is wrong.

If an attacker is able to observe messages they can extract valuable information around a session.
The attacker is able to determine when a call is taking place. They are also able to determine how
many media tracks are being exchanged.

Many times two WebRTC agents are in the same LAN. The signaling plane is usually run on a World Routable
server. The WebRTC Agents may have direct connectivity with each other, but not be able to connect to the
signaling plane.

# Compatibility with Existing WebRTC Implementations
A WebRTC URI can be parsed and used by a WebRTC implementation that doesn't support URIs. The values in
the URI could be expanded to be a SetLocalDescription and SetRemoteDescription. These options could also be
handled by a ORTC Implementation.

Given the following URI

    webrtc://eu6k:cae6@example.org:5001?local-ice-ufrag=adu3&local-ice-pwd=eew4&datachannel&ice-controlling&dtls-client

You get this Local Description


    v=0
    o=- 0 1 IN IP4 127.0.0.1
    s=-
    t=0 0
    m=application 9 DTLS/SCTP 5000
    c=IN IP4 127.0.0.1
    a=ice-ufrag:adu3
    a=ice-pwd:eew4
    a=setup:actpass
    a=mid:0
    a=sctpmap:5000 webrtc-datachannel 1024
    a=fingerprint:tbd


And this Remote Description

    v=0
    r=- 0 2 IN IP4 127.0.0.1
    s=-
    t=0 0
    m=application 9 DTLS/SCTP 5000
    c=IN IP4 127.0.0.1
    a=candidate:1966762134 1 udp 2122260223 example.org 5001 typ host
    a=ice-ufrag:eu6k
    a=ice-pwd:cae6
    a=setup:passive
    a=mid:0
    a=sctpmap:5000 webrtc-datachannel 1024
    a=fingerprint:tbd



# Security Considerations

# IANA Considerations

# Acknowledgments
