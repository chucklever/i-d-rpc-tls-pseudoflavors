---

title: New Pseudoflavors for Remote Procedure Calls with Transport Layer Security
abbrev: RPC TLS Pseudoflavors
docname: draft-cel-nfsv4-rpc-tls-pseudoflavors-latest
category: std
ipr: trust200902
area: Transport
workgroup: Network File System Version 4
obsoletes:
updates:
stand_alone: yes
pi: [toc, sortrefs, symrefs, docmapping]

author:

-
  name: Charles Lever
  org: Oracle Corporation
  abbrev: Oracle
  country: United States of America
  email: chuck.lever@oracle.com

--- abstract

Recent innovations in Remote Procedure Call transport layer security
enable broad deployment of encryption and mutual peer authentication
when exchanging RPC messages.
These security mechanisms can protect peers who continue to use
RPC_AUTH_SYS, which is not cryptographically secure, on open networks.
This document introduces several new RPC auth pseudo-flavors,
which an RPC service can use to indicate to their clients
that the use of encryption and mutual peer authentication
is required to access that service.

--- note_Note

This note is to be removed before publishing as an RFC.

Discussion of this draft occurs on the [NFSv4 working group mailing
list](nfsv4@ietf.org), archived at
[](https://mailarchive.ietf.org/arch/browse/nfsv4/). Working Group
information is available at
[](https://datatracker.ietf.org/wg/nfsv4/about/).

Submit suggestions and changes as pull requests at
[](https://github.com/chucklever/i-d-rpc-tls-pseudoflavors).
Instructions are on that page.

--- middle

# Introduction

Each ONC RPC transaction may be associated with a user and a set of groups.
The transaction's RPC auth flavor determines how the user/groups are
identified and whether they are authenticated.

The peers which host applications and RPC services may also be
identified and authenicated in each transaction,
depending on the RPC auth flavor.

Not all flavors provide peer and user identification and authentication.
For example, the traditional RPC auth flavor AUTH_NONE identifies no
user or group and no authentication or users or peers.

The traditional RPC auth flavor AUTH_SYS, as it is defined in {{!RFC5531}},
provides identification of peers, users and groups, but no authentication
of any of these.

Moreover, unlike some GSS security services, these RPC auth flavors
provide no confidentiality or integrity checking. Therefore AUTH_NONE
and AUTH_SYS are considered insecure.

Mutual peer authentication and encryption provided at the transport
layer is known to make the use of AUTH_NONE and AUTH_SYS more secure.

An RPC service might want to indicate to its clients that it will not
allow access via AUTH_NONE or AUTH_SYS unless such underlying encryption
or peer authentication services are in place. To do that, this document
requests the allocation of new RPC auth flavors that upper layers
such as NFS {{?RFC8881}} can use to enforce stronger security.

# Requirements Language

{::boilerplate bcp14-tagged}

# Example Usage

This document defines new protocol elements that any RPC consumer can employ.
This section presents examples of how commonly-used RPC consumers might
make use of these new pseudo-flavors.

Certain aspects of transport layer security are not new.
A deployment might choose to run NFS on a virtual private network
established via an ssh tunnel or over IPsec. The RPCSEC GSS v2
specification {{?RFC5403}} recognized the use of security provided
by transport services underlying RPC with the introduction of
channel binding.

TLS defines a channel binding {{?RFC5929}} that RPC consumers can use
to determine whether appropriate security is in place.
To determine whether an underlying transport security service
such as IPsec is in place,
an implementation employs APIs that are appropriate for that
and out of scope for this document.

## Network File System version 3

NFSv3 clients query which RPC auth flavors a server requires
using the MNT procedure, defined in {{Appendix I of ?RFC1813}}
as part of the MOUNT RPC program.

To require NFSv3 clients to employ underlying transport security
when using AUTH_NONE or AUTH_SYS, the NFS server includes one or
more of the new RPC auth pseudo-flavors defined in {{iana-cons}}
in the auth_flavors list that is part of a MNT response.

Once the peers have inspected their endpoint configurations to
ensure that any required peer authentication has been done and
encryption has been put into place, they can exchange RPC
transactions using the traditional AUTH_SYS or AUTH_NONE flavors
in each RPC.

## Network File System version 4

NFSv4 clients query which RPC auth flavors a server requires
using the SECINFO or SECINFO_NO_NAME procedures,
as defined in {{?RFC8881}}.

To require NFSv4 clients to employ underlying transport security
when using AUTH_NONE or AUTH_SYS, the NFS server includes one or
more of the new RPC auth pseudo-flavors defined in {{iana-cons}}
in the SECINFO4resok list that is part of a SECINFO or
SECINFO_NO_NAME response.

Once the peers have inspected their endpoint configurations to
ensure that any required peer authentication has been done and
encryption has been put into place, they can exchange RPC
transactions using the traditional AUTH_SYS or AUTH_NONE flavors
in each RPC.

# Implementation Status

This section is to be removed before publishing this document as an RFC.

This section records the status of known implementations of the
protocol defined by this specification at the time of posting of this
Internet-Draft, and is based on a proposal described in
{{!RFC7942}}. The description of implementations in this section is
intended to assist the IETF in its decision processes in progressing
drafts to RFCs.

Please note that the listing of any individual implementation here
does not imply endorsement by the IETF. Furthermore, no effort has
been spent to verify the information presented here that was supplied
by IETF contributors. This is not intended as, and must not be
construed to be, a catalog of available implementations or their
features. Readers are advised to note that other implementations may
exist.

There are currently no known implementations of the new RPC pseudo-flavors
requested by this document.

# Security Considerations {#security-cons}

To be expanded once the proposed set of IANA requests is finalized.

# IANA Considerations  {#iana-cons}

RFC Editor: In the following subsections, please replace RFC-TBD with
the RFC number assigned to this document. Furthermore, please remove
this Editor's Note before this document is published.

## New RPC Auth Flavors

Following Appendix B of {{!RFC5531}}, this document requests several new entries in the
[RPC Authentication Flavor Numbers](https://www.iana.org/assignments/rpc-authentication-numbers/rpc-authentication-numbers.xhtml)
registry.
The purpose of these new flavors is to indicate the use of transport
layer encryption or mutual peer authentication with insecure RPC auth flavors.
All new flavors described in the sections below are pseudo-flavors.

## Pseudoflavors for Secure AUTH_NONE

The fields in the new entries are assigned as follows:

| Identifier String | Flavor Name | Value | Description | Reference |
|:------------------|:-----------:|:-----:|:-----------:|----------:|
|   AUTH_NONE_MA    |  NONE_MA    |  TBD  | AUTH_NONE with mutual peer authentication | RFC_TBD |
|   AUTH_NONE_ENC   |  NONE_ENC   |  TBD  | AUTH_NONE with transport layer encryption | RFC_TBD |
|  AUTH_NONE_MA_ENC | NONE_MA_ENC |  TBD  | AUTH_NONE with peer authentication and encryption | RFC_TBD |

Please allocate the numeric values from the range 400000-409999.

## Pseudoflavors for Secure AUTH_SYS

The fields in the new entries are assigned as follows:

| Identifier String | Flavor Name | Value | Description | Reference |
|:------------------|:-----------:|:-----:|:-----------:|----------:|
|   AUTH_SYS_MA     |  SYS_MA     |  TBD  | AUTH_SYS with mutual peer authentication | RFC_TBD |
|   AUTH_SYS_ENC    |  SYS_ENC    |  TBD  | AUTH_SYS with transport layer encryption | RFC_TBD |
|  AUTH_SYS_MA_ENC  | SYS_MA_ENC  |  TBD  | AUTH_SYS with peer authentication and encryption | RFC_TBD |

Please allocate the numeric values from the range 410000-419999.

--- back

# Acknowledgments
{: numbered="no"}

The editor is grateful to Bill Baker, Rick Macklem, Greg Marsden,
and Martin Thomson for their input and support.

Special thanks to Transport Area Directors Martin Duke and
Zaheduzzaman Sarker, NFSV4 Working Group Chairs David Noveck and
Brian Pawlowski, and NFSV4 Working Group Secretary Thomas Haynes for
their guidance and oversight.
