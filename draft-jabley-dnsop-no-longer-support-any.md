---
title: "Continuing Reduction in Support for QTYPE=ANY in the DNS"
#abbrev: "TODO - Abbreviation"
category: std
updates: RFC 1034, RFC 1035, RFC 8482

docname: draft-jabley-dnsop-no-longer-support-any-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "Domain Name System Operations"
keyword:
 - dns
 - any
venue:
  group: "Domain Name System Operations"
  type: "Working Group"
  mail: "dnsop@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/dnsop/"
  github: "ableyjoe/draft-jabley-dnsop-no-longer-support-any"
  latest: "https://ableyjoe.github.io/draft-jabley-dnsop-no-longer-support-any/draft-jabley-dnsop-no-longer-support-any.html"

author:
 -
    fullname: "Joe Abley"
    organization: Cloudflare
    email: "jabley@cloudflare.com"
 -
    fullname: "Brian Haberman"
    organization: Fastly
    email: brian@innovationslab.net

normative:

informative:


--- abstract

The DNS specification from its earliest day supported a special
query type (QTYPE) ANY. The handling of queries with QTYPE=ANY is
observed to vary between implementations. Queries with QTYPE=ANY
are known to facilitate amplification that can be abused and used
by malicious actors to attack third parties, and minimally-sized
responses are often constructed in order to mitigate those security
risks.  While queries with QTYPE=ANY can be used for troubleshooting
in some cases, the substantial inconsistency in how such queries
are handled makes them at best an unreliable signal.

This document continues the process of dropping support for QTYPE=ANY
from the DNS protocol.

--- middle

# Introduction

{{!RFC1034}} and {{!RFC1035}} define a special query type (QTYPE)
called "*" (type 255) which is commonly described in DNS implementations
using the keyword "ANY" (the keyword "ANY" is also mentioned in
{{!RFC6895}}.  This is described in {{!RFC1034}} section 3.7.1 as
"matches all RR types". Despite the superficial simplicity of this
direction, there are significant corner cases for which the
specification is ambiguous, especially where a query's QNAME
corresponds to a delegation (a case which DNS Security Extensions
{{?RFC9364}} makes more complicated, with authoritative data now
available both above and below the zone cut).

{{!RFC8482}} recognises that server operators have reasons to provide
minimal responses to queries with QTYPE=ANY ("ANY queries"), and
provides a number of allowable strategies that can be used. Mechanisms
described in {{!RFC8482}} have been broadly implemented with no
known negative consequences for end users. A consequence of that
experience is that it is now even harder to predict the type of
response any particular server night provide to an ANY query, and
consequently more difficult to interpret the response.

It is known that some useful and constructive uses of ANY queries
exist, despite their limitations. For example, ANY queries are
sometimes used to troubleshoot DNS problems, together with other
techniques and tools.

It is also possible that software exists that sends ANY queries and
expects a particular type of response, e.g. a response with a
particular RCODE. Such scenarios do not seem outlandish, and being
able to adapt to such situations when they come to light unexpectedly
while remaining consistent with Internet standards is important.

ANY queries were a nice idea. However, the idea turns out to have
been under-specified. There is a great deal of variation in their
implementation, and it is difficult to imagine new protocols
incorporating ANY queries since their treatment by servers is
extremely inconsistent.

This document reimagines {{!RFC8482}} as a first step on a path
towards deprecation of ANY queries, and provides a second step in
the same direction. The journey will continue.


# Terminology

{::boilerplate bcp14-tagged}

This document assumes familiarity with DNS-specific terminology as described
in {{!RFC9499}}.


# Updated Guidance on the Use of ANY Queries

## Do not send ANY queries without good reason

New protocols developed at the IETF SHOULD NOT incorporate ANY
queries unless the operational consequences of ambiguous behaviour
and inconsistent implementation are thoroughly understood and
documented.

DNS clients SHOULD NOT send ANY queries without a thorough understanding
of their limitations. DNS clients SHOULD NOT rely upon any particular
interpretation of a response to an ANY query unless the specific
behaviour of the system sending the response is known and predictable.
DNS clients MUST NOT assume that this is the situation when sending
queries to arbitrary destinations on the Internet.

DNS clients MAY send ANY queries for the purposes of troubleshooting
or gathering diagnostic information.

## Do not respond to ANY queries without good reason

DNS servers SHOULD respond to ANY queries with RCODE = 4 (NOTIMPL)
unless they have a specific local reason to respond differently. In
such circumstances, DNS servers MAY respond following their
interpretation of {{!RFC1034}} and {{!RFC1035}} or with a minimal
response as described in {{!RFC8482}}.

# Security Considerations

ANY queries are known to have been used to provide amplification
of source-spoofed DNS queries using UDP transport, as described in
{{?RFC5358}}. However, there are many other QTYPEs that provide
amplification potential when coupled with predictable QNAMEs, and
a reduction in support for ANY queries will not eliminate this
problem.

ANY queries can be used to inspect the cache of a recursive server,
and this might provide insight into query patterns for users of
that recursive server; this ability might present privacy concerns
that could be mitigated by not supporting ANY queries at all.

# IANA Considerations

The IANA is directed to update the "Resource Record (RR) TYPEs"
subregistry of the "Domain Name System (DNS) Parameters" registry
entry for type 255 to append this document to the list of references,
and to update the TYPE column to reflect the direction in {{!RFC6895}}.

| TYPE  | Value  | Meaning  | Reference  |
| ----  | -----  | -----    | -----      |
| * (ANY)  | 255    | A request for some or all records the server has available | {{!RFC1034}}{{!RFC6895}}{{!RFC8482}}[this document]  |


--- back

# Acknowledgments
{:numbered="false"}

Your name here, etc.

