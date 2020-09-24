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
of WebRTC has extended far beyond that.

The flexibility of WebRTC's signaling plane has made adoption difficult for some
use cases.  The signaling plane also presents challenges around security, privacy and
operational burden. However WebRTC's media plane is unambiguous. Once two
WebRTC Agents have enough details from signaling it is easy to establish rich communication.

The signaling plane is only necessary when two clients don't have enough
information to establish a session. In some cases signaling is not necessary. If the
two WebRTC agents agree on some basic details signaling is not required. The greatest difficulty
comes with establishing connectivity between two ICE Agents. {{!I-D.ietf-ice-rfc5245bis}}
If these two ICE Agents use mDNS {{!I-D.cheshire-dnsext-multicastdns}} and are in the same
network you can establish a Zeroconf session.  If one ICE Agent is world routable connectivity is
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

## Use cases for World routable host
- A user can publish media real-time to a CDN

- A user can connect to a CDN to watch a real-time video

# The WebRTC URI Scheme

The WebRTC URI takes two possible form

    webrtc://<ice-user-fragment>:<ice-password>@<mDNS name>?<paramaters>

    webrtc://<ice-user-fragment>:<ice-password>@<IP/Port>?<paramaters>


# Examples of WebRTC URI Syntax
## Zeroconf
## Public server

# Security, Privacy and Availability Benefits
Without a WebRTC URI a signaling plane is required to establish a WebRTC session.

If an attacker is able to modify messages they could MITM a WebRTC Session. The attacker
could replace the candidates and certificate fingerprint with their own. They could then
route all traffic through a host they control. The two users would connect to each other
and not realize that anything is wrong.

If an attacker is able to observe messages they can extract valuable information around a session.
The attacker is able to determine when a call is taking place. They are also able to determine how
many media tracks are being exchanged.

Many times two WebRTC agents are in the same LAN. The signaling plane is usually run on a world routable
server. The WebRTC Agents may have direct connectivity with each other, but not be able to connect to the
signaling plane.

# Security Considerations

# IANA Considerations

# Acknowledgments
