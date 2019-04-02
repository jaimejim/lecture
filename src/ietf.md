# The Internet Engineering Task Force (IETF)

In 1998 the Internet had about 50 million users, supported by approximately 25 million servers (Web and e-mail hosting sites, for example, but not desktops or laptops). Today the estimation is that some 3.4 billion people are regular users of the Internet, and there are some 20 billion devices connected to it. We have achieved this using some 3 billion unique IPv4 addresses. Looking ahead, there are estimations of several other billions of devices to be soon connected to this network. Noone thought that this feat could be achievable and yet it has happened with almost no fanfare.

A large portion of this success has been done by the work of engineers at organizations that are more or less unknown to the public. Being the Internet Engineering Task Force (IETF) the most prominent.

## What has the IETF done for us anyways?

The mission of the IETF is [to make the Internet work better](https://tools.ietf.org/html/rfc3935), and it tries to do so by creating high quality, relevant technical documents that influence the way people design, use, and manage the Internet. Those formal documents have behind them consensus and several implementations before they are assigned the usual Request for Comments (RFC) number.  Many of those RFCs have already been implemented and run on every connected machine today:

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

While most RFCs are just proposals, miscellaneous protocol descriptions, or opinion/information documents, there are true *Internet Standards* among the RFCs and are maintained by the [RFC Editor](http://www.rfc-editor.org/standards).

For the reader's pleasure I also copied two fantastic summaries of the changes that have happened to the Internet in a couple of decades:

- [A decade of Internet Evolution (2008)](./10years.md)
- [Another 10 years (2018)](./another10years.md)

## IETF Organization

As explained at the [IETF website](https://www.ietf.org/about/who/), the technical work of the IETF is done in its [working groups](https://www.ietf.org/how/wgs/), which are organized by topic into several areas (e.g., routing, transport, security, etc.). The IETF working groups are grouped into areas, and managed by Area Directors, or ADs. The ADs are members of the Internet Engineering Steering Group ([IESG](https://www.ietf.org/glossary.html#IESG)).

Much of the work in the IETF is handled via [mailing lists](https://www.ietf.org/list/). The IETF holds [meetings](https://www.ietf.org/how/meetings/) three times per year. [IETF Hackathons](https://www.ietf.org/how/runningcode/hackathons/) encourage collaboration on developing utilities, ideas, sample code, and solutions that show practical implementations of IETF standards. More recently GitHub has become a prevalent way to track the work and interact with developers more smoothly; for example [CoRE](https://github.com/core-wg), [HTTP](https://github.com/httpwg) or [TEEP](https://github.com/ietf-teep) use it regularly.

The Internet Architecture Board, ([IAB](https://www.ietf.org/glossary.html#IAB)) is responsible for defining the overall architecture of the Internet, providing guidance and broad direction to the IETF. The IAB also serves as the technology advisory group to the Internet Society [ISOC](https://www.ietf.org/glossary.html#ISOC)).

There are very detailed guideline documents like [RFC2418](https://tools.ietf.org/html/rfc2418) that explain how WGs are created and what the roles of each individual are, but at a high level the IETF relies on **"rough consensus and running code"**.

## The life of a draft

It all starts with an author of set of authors that want to propose some solution for a specific problem they are facing. Usually the problem requires some interoperability and thus an Internet draft seems like the right approach.

1. An Internet draft is first publish as an *individual submission* by one or more authors. Literally anyone can write a draft and the requirements on that submission are very low. 

2. Once the submission gets the attention of the Working Group, by discussing it on the mailing list, presenting it, doing implementations or evaluation... then the group can *adopt it* by doing a *Working Group Adoption (WGA)* call. Once agreed the document becomes a *working group internet draft*.

3. The draft will then go through **many** iterations and revisions, interop events, discussions, errata, and so on before the group thinks it is ready for *Working Group Last Call* which is the moment in which the group decides that the document is ready for publication.

4. After that the document is sent to the [Internet Engineering Steering Group (IESG)](https://www.ietf.org/about/groups/iesg/) for final review, [IANA](https://www.iana.org/) will due its due process and other and other editing will be done by/with the RFC Editor. After some time the document gets assigned an RFC number and is published. 

5. After several years, providing there is industry adoption and the document is on the Standards Track i.e. it is not informational, the document may be come an Internet Standard. This whole process takes a long time and it can feel a bit slow, but at least the out put has guarantees of *some* consensus and *some* running code.

![IETF Publication Process](./img/ietf-process.png)

## Contributing to IETF


Best is to start by reading [the Tao](https://www.ietf.org/about/participate/tao/)
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