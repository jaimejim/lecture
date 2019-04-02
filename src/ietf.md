# The Internet Engineering Task Force (IETF)

In 1998 the Internet had about 50 million users, supported by approximately 25 million servers (Web and e-mail hosting sites, for example, but not desktops or laptops). Today the estimation is that some 3.4 billion people are regular users of the Internet, and there are some 20 billion devices connected to it. We have achieved this using some 3 billion unique IPv4 addresses. Nobody thought that we could achieve this astonishing feat, yet it has happened with almost no fanfare.

A lot of this success has been done by the work of engineers at organizations that are more or less unknown to the public. Being the Internet Engineering Task Force (IETF) the most prominent.

## What has the IETF done for us anyways?

The IETF is a large open international community of network designers, operators, vendors, and researchers concerned with the evolution of the Internet architecture and the smooth operation of the Internet. The mission of the IETF is to make the Internet work better by producing high quality, relevant technical documents that influence the way people design, use, and manage the Internet. [RFC 3935](https://tools.ietf.org/html/rfc3935). Many of those RFCs have already been implemented on every connected machine today:

- [RFC791](https://tools.ietf.org/html/rfc791) The Internet Protocol.
- [RFC792](https://tools.ietf.org/html/rfc792) The Internet Control Message Protocol.
- [RFC821](https://tools.ietf.org/html/rfc821) The Simple Mail Transfer Protocol.
- [RFC768](https://tools.ietf.org/html/rfc768) User Datagram Protocol.
- [RFC959](https://tools.ietf.org/html/rfc959) The File Transfer Protocol.
- [RFC793](https://tools.ietf.org/html/rfc793) The Transmission Control Protocol.
- [RFC854](https://tools.ietf.org/html/rfc854) Telnet Specification.
- [RFC1119](https://tools.ietf.org/html/rfc1119) Network Time Protocol.
- [RFC1157](https://tools.ietf.org/html/rfc1157) A Simple Network Management Protocol.
- [RFC1035](https://tools.ietf.org/html/rfc1035) Domain names - implementation and specification.
- [RFC1945](https://tools.ietf.org/html/rfc1945) Hypertext Transfer Protocol.
- [RFC1964](https://tools.ietf.org/html/rfc1964) The Kerberos Version 5 GSS-API Mechanism.
- [RFC2131](https://tools.ietf.org/html/rfc2131) Dynamic Host Configuration Protocol.
- [RFC2246](https://tools.ietf.org/html/rfc2246) The TLS Protocol Version.
- [RFC2328](https://tools.ietf.org/html/rfc2328) The Open Shortest Path First routing protocol.
- [RFC6455](https://tools.ietf.org/html/rfc6455) The WebSocket Protocol.
- [RFC5321](https://tools.ietf.org/html/rfc5321) Simple Mail Transfer Protocol.
- [RFC7540](https://tools.ietf.org/html/rfc7540) Hypertext Transfer Protocol Version 2.
- [RFC6749](https://tools.ietf.org/html/rfc6749) The OAuth 2.0 Authorization Framework.
- [RFC4271](https://tools.ietf.org/html/rfc4271) The Border Gateway Protocol.
- [RFC4287](https://tools.ietf.org/html/rfc4287) The Atom Syndication Format.
- [RFC4251](https://tools.ietf.org/html/rfc4251) The Secure Shell (SSH) Protocol Architecture.
- [RFC8200](https://tools.ietf.org/html/rfc8200) Internet Protocol, Version 6 (IPv6) Sepcification.

While most RFCs are just proposals, miscellaneous protocol descriptions, or opinion/information documents, there are true *“Internet standards”* among the RFCs and are maintained by the [RFC Editor](http://www.rfc-editor.org/standards).

## IETF Organization

As explained at the [IETF website](https://www.ietf.org/about/who/), the technical work of the IETF is done in its [working groups](https://www.ietf.org/how/wgs/), which are organized by topic into several areas (e.g., routing, transport, security, etc.). The IETF working groups are grouped into areas, and managed by Area Directors, or ADs. The ADs are members of the Internet Engineering Steering Group ([IESG](https://www.ietf.org/glossary.html#IESG)).

Much of the work in the IETF is handled via [mailing lists](https://www.ietf.org/list/). The IETF holds [meetings](https://www.ietf.org/how/meetings/) three times per year. [IETF Hackathons](https://www.ietf.org/how/runningcode/hackathons/) encourage collaboration on developing utilities, ideas, sample code, and solutions that show practical implementations of IETF standards. More recently GitHub has become a prevalent way to track the work and interact with developers more smoothly; for example [CoRE](https://github.com/core-wg), [HTTP](https://github.com/httpwg) or [TEEP](https://github.com/ietf-teep) use it regularly.

The Internet Architecture Board, ([IAB](https://www.ietf.org/glossary.html#IAB)). The IAB also adjudicates appeals when someone complains that the IESG has failed. The IAB and IESG are chartered by the Internet Society ([ISOC](https://www.ietf.org/glossary.html#ISOC)) for these purposes. The General Area Director also serves as the chair of the IESG and of the IETF, and is an ex-officio member of the IAB.

There are very detailed guideline documents like [RFC2418](https://tools.ietf.org/html/rfc2418) that explain how WGs are created and what the roles of each individual are, but at a high level the IETF relies on **"rough consensus and running code"**.




The technical work of the IETF is done in Working Groups, which are organized by topic into several Areas. Much of the work is handled via mailing lists. The IETF holds meetings three times per year. 

The IETF working groups are grouped into areas, and managed by Area Directors, or ADs. The ADs are members of the Internet Engineering Steering Group (IESG). Providing architectural oversight is the Internet Architecture Board, (IAB). The IAB also adjudicates appeals when someone complains that the IESG has failed. The IAB and IESG are chartered by the Internet Society (ISOC) for these purposes. The General Area Director also serves as the chair of the IESG and of the IETF, and is an ex-officio member of the IAB. The Internet Assigned Numbers Authority (IANA) is the central coordinator for the assignment of unique parameter values for Internet protocols.

More information about the IETF standards process is available here and in RFC2026.

New participants in the IETF might find it helpful to read Getting Started in the IETF and The Tao of the IETF, (also available as RFC4677). You can learn more via tutorials or mentoring.


**IETF Working Groups (WGs)** are the primary mechanism for the development of IETF specifications and guidelines, many of which are intended to be standards or recommendations.

**IRTF Research Group (RG)** explore and work on research-related topics with a more longer-term approach when compared to a WG. However, its organizational nature remains similar.

## The life of a draft

**Internet-Drafts (I-Ds)** are working documents of the IETF, its areas, and its Working Groups. Note that other groups may also distribute working documents as I-Ds. I-Ds can be proposed by individual parties, normally within the scope a specific WG. In order to become WG I-Ds they need to go through an adoption process within the WG itself.

**Request for Comment (RFC)** is a technical and organizational publication, which describes mechanisms, implementations guidelines, or innovations applicable on topics related to Internet protocols, Internet-connected systems, applications, architecture and technology. In order to become RFC there needs to be several interoperable implementations, which often are Open Sourced.

RFCs can follow multiple tracks; standard, informational or experimental. Standard track RFCs that reach an adequate level of maturity become firstly *Proposed Standard*. Once that the proposed standard reaches a high degree of maturity and adoption can become *Internet Standard*.

## Contributing to IETF

Initially, the IETF met quarterly, but from 1991, it has been meeting three times a year. The initial meetings were very small, with fewer than 35 people in attendance at each of the first five meetings. The maximum attendance during the first 13 meetings was only 120 attendees. This occurred at the 12th meeting held during January 1989. These meetings have grown in both participation and scope a great deal since the early 1990s; it had a maximum attendance of 2,810 at the December 2000 IETF held in San Diego, California. Attendance declined with industry restructuring during the early 2000s, and is currently around 1,200.

### Communication tools

**mailing lists**
**jabber**
**interims**
**github**
**face to face**

## References
- [IETF Newcomer's presentation](https://www.youtube.com/playlist?list=PLC86T-6ZTP5hXPJ-n4mwJbZ0BHaNlhTMA)
- [Newcomer's Overview](https://www.ietf.org/about/participate/tutorials/newcomers/overview/)
- [Another 10 years](./another10years.md)