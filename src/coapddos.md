# DDOS using CoAP

A distributed reflective denial-of-service (DRDoS) is a form of distributed denial-of-service (DDoS) attack that relies on publicly accessible UDP servers and bandwidth amplification factors (BAFs) to overwhelm a victim’s system with UDP traffic.


But, in some ways this is news but isn’t. CoAP is widely enough deployed to be noticeable now. 
Since there is no handshake, little state and smaller messages size, CoAP chose UDP as protocol in order to process messages faster, also shaves of one round trip time for the query and response when using UDP.






## Amplification

[RFC7252 Section 11.3](https://tools.ietf.org/html/rfc7252#section-11.3)

CoAP servers generally reply to a request packet with a response
   packet.  This response packet may be significantly larger than the
   request packet.  An attacker might use CoAP nodes to turn a small
   attack packet into a larger attack packet, an approach known as
   amplification.  There is therefore a danger that CoAP nodes could
   become implicated in denial-of-service (DoS) attacks by using the
   amplifying properties of the protocol: an attacker that is attempting
   to overload a victim but is limited in the amount of traffic it can
   generate can use amplification to generate a larger amount of
   traffic.

This is particularly a problem in nodes that enable NoSec access, are
   accessible from an attacker, and can access potential victims (e.g.,
   on the general Internet), as the UDP protocol provides no way to
   verify the source address given in the request packet.  An attacker
   need only place the IP address of the victim in the source address of
   a suitable request packet to generate a larger packet directed at the
   victim.

   As a mitigating factor, many constrained networks will only be able
   to generate a small amount of traffic, which may make CoAP nodes less
   attractive for this attack.  However, the limited capacity of the
   constrained network makes the network itself a likely victim of an
   amplification attack.

   Therefore, large amplification factors SHOULD NOT be provided in the
   response if the request is not authenticated.  A CoAP server can
   reduce the amount of amplification it provides to an attacker by
   using slicing/blocking modes of CoAP [BLOCK] and offering large
   resource representations only in relatively small slices.  For
   example, for a 1000-byte resource, a 10-byte request might result in
   an 80-byte response (with a 64-byte block) instead of a 1016-byte
   response, considerably reducing the amplification provided.

   CoAP also supports the use of multicast IP addresses in requests, an
   important requirement for M2M.  Multicast CoAP requests may be the
   source of accidental or deliberate DoS attacks, especially over
   constrained networks.  This specification attempts to reduce the
   amplification effects of multicast requests by limiting when a
   response is returned.  To limit the possibility of malicious use,
   CoAP servers SHOULD NOT accept multicast requests that can not be
   authenticated in some way, cryptographically or by some multicast
   boundary limiting the potential sources.  If possible, a CoAP server
   SHOULD limit the support for multicast requests to the specific
   resources where the feature is required.

   On some general-purpose operating systems providing a POSIX-style API
   [IEEE1003.1], it is not straightforward to find out whether a packet
   received was addressed to a multicast address.  While many
   implementations will know whether they have joined a multicast group,
   this creates a problem for packets addressed to multicast addresses
   of the form FF0x::1, which are received by every IPv6 node.
   Implementations SHOULD make use of modern APIs such as
   IPV6_RECVPKTINFO [RFC3542], if available, to make this determination.

## IP Address Spoofing

[RFC7252 Section 11.4](https://tools.ietf.org/html/rfc7252#section-11.4)

Due to the lack of a handshake in UDP, a rogue endpoint that is free
   to read and write messages carried by the constrained network (i.e.,
   NoSec or PreSharedKey deployments with a nodes/key ratio > 1:1), may
   easily attack a single endpoint, a group of endpoints, as well as the
   whole network, e.g., by:

   1.  spoofing a Reset message in response to a Confirmable message or
       Non-confirmable message, thus making an endpoint "deaf"; or

   2.  spoofing an ACK in response to a CON message, thus potentially
       preventing the sender of the CON message from retransmitting, and
       drowning out the actual response; or

   3.  spoofing the entire response with forged payload/options (this
       has different levels of impact: from single-response disruption,
       to much bolder attacks on the supporting infrastructure, e.g.,
       poisoning proxy caches, or tricking validation/lookup interfaces
       in resource directories and, more generally, any component that
       stores global network state and uses CoAP as the messaging
       facility to handle setting or updating state is a potential
       target.); or

   4.  spoofing a multicast request for a target node; this may result
       in network congestion/collapse, a DoS attack on the victim, or
       forced wake-up from sleeping; or

   5.  spoofing observe messages, etc.

   Response spoofing by off-path attackers can be detected and mitigated
   even without transport layer security by choosing a nontrivial,
   randomized token in the request (Section 5.3.1).  [RFC4086] discusses
   randomness requirements for security.

   In principle, other kinds of spoofing can be detected by CoAP only in
   case Confirmable message semantics is used, because of unexpected
   Acknowledgement or Reset messages coming from the deceived endpoint.
   But this imposes keeping track of the used Message IDs, which is not
   always possible, and moreover detection becomes available usually
   after the damage is already done.  This kind of attack can be
   prevented using security modes other than NoSec.

   With or without source address spoofing, a client can attempt to
   overload a server by sending requests, preferably complex ones, to a
   server; address spoofing makes tracing back, and blocking, this
   attack harder.  Given that the cost of a CON request is small, this
   attack can easily be executed.  Under this attack, a constrained node
 with limited total energy available may exhaust that energy much more
   quickly than planned (battery depletion attack).  Also, if the client
   uses a Confirmable message and the server responds with a Confirmable
   separate response to a (possibly spoofed) address that does not
   respond, the server will have to allocate buffer and retransmission
   logic for each response up to the exhaustion of MAX_TRANSMIT_SPAN,
   making it more likely that it runs out of resources for processing
   legitimate traffic.  The latter problem can be mitigated somewhat by
   limiting the rate of responses as discussed in Section 4.7.  An
   attacker could also spoof the address of a legitimate client; this
   might cause the server, if it uses separate responses, to block
   legitimate responses to that client because of NSTART=1.  All these
   attacks can be prevented using a security mode other than NoSec, thus
   leaving only attacks on the security protocol.

## Avoid NoSec

uct at the cheapest possible price point. It may be disconcerting to realise that the web camera you just installed has a security model that can be summarised with the phrase: “no security at all”, and its actually offering a view of your house to the entire Internet. It may be slightly more disconcerting to realise that your electronic wallet is on a device that is using a massive compilation of open source software of largely unknown origin, with a security model that is not completely understood, but appears to be susceptible to be coerced into being a “yes, take all you want”.


Distributed Denial of Service (DDoS) attacks are a common way to prevent Internet Services from running.  employing reflected UDP amplification are regularly used to disrupt networks and systems. The amplification allows one rented server to generate significant volumes of data, while the reflection hides the identity of the attacker. Consequently this is an attractive, low risk, strategy for criminals bent on vandalism and extortion. To measure the uptake of this strategy we analyse the results of running a network of honeypot UDP reflectors (median size 65 nodes) from July 2014 onwards. We explore the life cycle of attacks that use our reflectors, from the scanning phase used to detect our honeypot machines, through to their use in attacks. We see a median of 1 450 malicious scanners per day across all UDP protocols, and have recorded details of 5.18 million subsequent attacks involving in excess of 3.31 trillion packets. Using a capture-recapture statistical technique, we estimate that our reflectors can see between 85.1% and 96.6% of UDP reflection attacks over our measurement period.

---














### Basic Shodan Commands

`shodan count port:5683` is used to get all CoAP endpoints.

`shodan count port:5683 country:CN` to get all CoAP endpoints in China.

`shodan host [IP_ADDRESS_HERE]` to get info on a specific endpoint.

`shodan search --fields ip_str,port,org,hostnames port:5683 country:CN` to get a list of CoAP endpoints, their specific IPs and hostnames.

`shodan stats --limit 20 port:5683` to get some statistics CoAP endpoits out there.

A

s the presentation given at [RVAsec](https://www.youtube.com/watch?v=DX68vb2XjdQ&feature=youtu.be&t=19m28s) states, the CoAP protocol was not yet mainstream back in 2017, but the number of *visible* CoAP endpoints has increased rapidly from about `6500` to `220000` between november 2017 and may 2018, to more than `738041` in October 2018. That number has decreased as devices are being patched and we would find about `436854` running `shodan count port:5683`, out of which `305051` are in China. There is of course orders of magnitude more endpoints that are not openly visible on the Internet.



UDP protocols are particularly vulnerable to attack.

There are many UDP-based protocols that are used today to perform attacks. 

memcached protocols has an amplicifation attack of 51200

The table below is inspired from [Dennis Radd](http://rvasec.com/slides/2018/Rand_Dennis-RVAsec_2018.pdf) presentation contains some common UDP protocols used on DDOS, the **total number** of endpoints available on Shodan (available at April 2019) out of which an unknown (to me) percentage will be open for an attack. The request size and amplification factor is taken from the presentation and from [CISA](https://www.us-cert.gov/ncas/alerts/TA14-017A).

The amplification factor means represents the effort for an attacker, for example in CoAP a request of `21` bytes without the UDP header of size can yield a response of `21*16= 336` bytes. The target for the attacker is to use the minimum effort for the maximum attack factor. An attacker who has access to a 1 Mbps link could on average reach 16 Mbps and up to 97 Mbps *per endpoint*.

| Protocol (port)   | Request size         | Avg/Max amplification            | Numbers                      |
|:------------------|---------------------:|---------------------------------:|-----------------------------:|
| DNS (53)          |                 37 b |                            28/54 |                   13.986.243 |
| NetBIOS (137)     |                 50 b |                            3/229 |                      601.869 |
| SIP (5060)        |                128 b |                             3/19 |                   15.161.110 |
| SNPM (161)        |                 40 b |                           34/553 |                    2.184.406 |
| NTP (123)         |                 12 b |                           22/198 |                    9.483.324 |
| **CoAP (5683)**   |              **21 b**|                        **16/97** |                  **436.854** |
| LDAP (389)        |                 52 b |                            45/55 |                      494.276 |
| Memcached (11211) |                 15 b |                 10000/51000 ([!!](http://www.senki.org/memcached-on-port-11211-udp-tcp-being-exploited/)) |                       39.785 |

```sh
$ shodan host 120.212.XXX.XXX
120.212.XXX.XXX
Hostnames:               XXX
City:                    Nizhniy Novgorod
Country:                 Russian Federation
Organization:            OOO Trivon Networks
Updated:                 2019-04-10T11:37:35.572911
Number of open ports:    2

Ports:
     80/tcp  
   5683/udp
```

```sh
$ shodan host 39.159.XXX.XXX
39.159.XXX.XXX
City:                    Nanchang
Country:                 China
Organization:            China Mobile Guangdong
Updated:                 2019-04-10T11:39:13.342628
Number of open ports:    1

Ports:
   5683/udp 
```

On December 2018 the paper [When Machines can't talk](http://i.blackhat.com/eu-18/Thu-Dec-6/eu-18-Maggi-When-Machines-Cant-Talk-wp.pdf) in there the authors present 



[BCP38](https://tools.ietf.org/html/bcp38)

UDP Attacks are conceptually simple. 
The attacker simply forges the originators IP address in what is called IP Spoofing, and sends a request with the destination IP address of the victim. All devices will send a message ... 




All UDP-based protocols are susceptible to IP spoofing. UDP is a connectionless protocol and, as such, unlike Transmission Control Protocol (TCP), has no handshake phase, in which the two endpoints agree on a sequence number that identifies a connection. This means that if an attacker A sends a UDP packet with a spoofed source IP address B to an endpoint C, C will have no way to verify whether that packet comes from B or A. So, if the autonomous systems (ASs) adopt no specific
countermeasures against spoofing, C will have to trust the UDP packet header and act accordingly.

The risk of amplification was already explained in the [CoAP specification](https://tools.ietf.org/html/rfc7252#section-11.3). 

IP spoofing alone only exploits the lack of authentication in UDP and, per se, is not a big issue nowadays. However, if the application layer implements a request-response protocol, the attacker A could use C as a “reflector.” C will trust the source address B, and send any response packet to B, which will accept
such a packet according to its application layer.

Address spoofing on the internet is harder nowadays than in the past, thanks to various countermeasures. However, as of this writing, about 17 to 22 percent of the IPv4 (Internet Protocol version 4) ASs are still susceptible to spoofing — roughly, this translates to 9 to 14 percent of the IPv4 blocks.

In February 2018, an amplification attack taking advantage of the Memcache service reached almost 1 terabit per second (Tbps) of bandwidth, which could easily take down any large website. 

Services other than Memcache have been used in the past to launch other amplification attacks, the most notable ones being DNS and SSDP. As surveyed by a well-referenced research in 2014,40 there are at least 15 application protocols that can be abused for amplification attacks, and they all have two aspects in
common: They are based on UDP and are request-response-based.

[Mirai Botnet](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/46301.pdf)



Searching CoAP records matching the “password” keyword, we found a uniform group of about 365,000 hosts, using networking devices of the same brand, responding on the CoAP port. These hosts are all mobile hotspots located in the Asia Pacific (APAC) region. Given such authentication information and given the abundance of hotspots in the region, it is easy for an attacker to connect to that network and
abuse the service to disguise their true identity 



As we saw in the section [Finding your CoAP devices](./coapdiscovery.md) CoAP has an in-built discovery mechanism, thus sending a `GET` request message to the `/.well-known/core` resource might show information. Indeed, for example the node in [Novgorod](https://pastebin.com/raw/zhZv67P5) shows the available resources, examples of the expected payloads and the interaction methods. Similarly the node in [Nanchang](https://pastebin.com/raw/CYMCFcG5) exposes network information, interfaces and all resources available, some even related to control information and the expected content-formats.

If we were interested which type of devices are these , it is easy to perform an [nmap scan](https://pastebin.com/raw/rb6Q75K9)
 to find out that several other TCP ports are also open, indicating that at least a portion of the devices are gateways with a dozen other services. this could lead to think that the Gateways use CoAP as signalling protocol.





 ## Solutions


As we other DDOS attacks, some solutions are already at hand, some outlined in [RFC4732](https://tools.ietf.org/html/rfc4732):

1.  That those running networks adopt *source IP address verification* (i.e., that they improve and tighten existing systems).
2. That root server operators—and those running country code
top-level domains—draw up their countermeasure policies,
respond quickly to queries, and act quickly to add servers
back into the system if the owner shows they have improved
their security.
3. ISPs should only accept DNS queries from trusted sources
(i.e., their own customers) rather than allow anyone to use
their servers.


In general, network and server administrators for Internet service providers (ISPs) should use the following best practices to avoid becoming amplifier nodes:

1. Use network flow to detect spoofed packets. (See the Mitigation section below for information on verifying spoofed traffic before blocking that traffic.)
2. Use network flow or other summarized network data to monitor for an unusual number of requests to at-risk UDP services.
3. Use network flow to detect service anomalies (e.g., bytes-per-packet and packets-per-second anomalies).



In order to prevent a malware infection on an IoT device, users and administrators should take following precautions:

Ensure all default passwords are changed to strong passwords. Default usernames and passwords for most devices can easily be found on the Internet, making devices with default passwords extremely vulnerable.
Update IoT devices with security patches as soon as patches become available.
Disable Universal Plug and Play (UPnP) on routers unless absolutely necessary. [12]
Purchase IoT devices from companies with a reputation for providing secure devices.
Consumers should be aware of the capabilities of the devices and appliances installed in their homes and businesses. If a device comes with a default password or an open Wi-Fi connection, consumers should change the password and only allow it to operate on a home network with a secured Wi-Fi router.
Understand the capabilities of any medical devices intended for at-home use. If the device transmits data or can be operated remotely, it has the potential to be infected.
Monitor Internet Protocol (IP) port 2323/TCP and port 23/TCP for attempts to gain unauthorized control over IoT devices using the network terminal (Telnet) protocol.[13]
Look for suspicious traffic on port 48101. Infected devices often attempt to spread malware by using port 48101 to send results to the threat actor.




---

Zdnet has just published the following article: https://www.zdnet.com/article/the-coap-protocol-is-the-next-big-thing-for-ddos-attacks/, it explains how CoAP has been used to create a DDOS attack similar to those experienced last year with Mirai. The attack generated at some point 320Gbps of data. To compare, Mirai’s attack exceeded 600 Gbps in volume, among the largest on record. The article probably gets inspiration a presentation given also today at Black Hat Europe (https://www.blackhat.com/eu-18/briefings/schedule/index.html#when-machines-cant-talk-security-and-privacy-issues-of-machine-to-machine-data-protocols-12722 )



The summary is that this year has seen an increase in CoAP running devices in the open, from about 30K to circa 600K devices, mostly located in China. The number was even higher before but it has steadily been decreasing for a couple of weeks. The CoAP endpoints shouldn’t be visible there, if they are publicly visible that shows already some misconfiguration on the device. These new ones not only run CoAP but also several other services  (e.g., ftp, smtp, http, telnet…), they seem to be wireless broadband q-link routers that do not use any CoAP security at all.

I gave a more detailed presentation to Ericsson's TEA Security Forum about this back in November, since the attack vector has been known for quite a while and I believe it could affect unsecured LWM2M clients. I am in contact with the authors of the research too and with a Q-link developer. We are also aware of this on the CoAP standardisation side at IETF, however this is mostly a deployment issue. 

The CoAP specification already foresees such attacks; both Packet Amplification and IP-Spoofing are attacks that can happen on any UDP protocol if you don’t use any security. Needless to say, it can easily be prevented today by using some security on your devices.

---

The time to 1 Tbit/s amplification from CoAP servers is hard to predict — it depends on how many CoAP servers are really not so constrained and can be enticed to send Mbit/s traffic to a victim.  Now it would be useful to know what the parameters of those attacks were….

---



[Another 10 years](./another10years.md)

The Evolution of Cyber Attacks
The Internet of Billions of Tragically Stupid Things

---

https://tools.ietf.org/html/draft-irtf-t2trg-iot-seccons-16

---

ejajimn@EMB-918HFH01:~$ shodan count port:5683
618662

Lot's of coap endpoints in china

https://www.shodan.io/search?query=port%3A5683

Actually, to be precise

ejajimn@EMB-918HFH01:~$ shodan count port:5683 country:CN
604640

An specific example:


ejajimn@EMB-918HFH01:~$ shodan host 112.54.90.238
112.54.90.238
Country:                 China
Organization:            China Mobile Guangdong
Updated:                 2018-09-28T06:45:17.716725
Number of open ports:    1

Ports:
   5683/udp  

---


Starting Nmap 7.70 ( https://nmap.org ) at 2018-09-28 11:24 EEST
Initiating Parallel DNS resolution of 1 host. at 11:24
Completed Parallel DNS resolution of 1 host. at 11:24, 0.21s elapsed
Initiating SYN Stealth Scan at 11:24
Scanning 39.178.181.144 [1000 ports]
Discovered open port 5080/tcp on 39.178.181.144
Completed SYN Stealth Scan at 11:24, 20.55s elapsed (1000 total ports)
Initiating OS detection (try #1) against 39.178.181.144
Retrying OS detection (try #2) against 39.178.181.144
adjust_timeouts2: packet supposedly had rtt of -111442 microseconds.  Ignoring time.
adjust_timeouts2: packet supposedly had rtt of -111442 microseconds.  Ignoring time.
Nmap scan report for 39.178.181.144
Host is up (0.28s latency).
Not shown: 986 closed ports
PORT      STATE    SERVICE
21/tcp    filtered ftp
23/tcp    filtered telnet
25/tcp    filtered smtp
53/tcp    filtered domain
80/tcp    filtered http
139/tcp   filtered netbios-ssn
445/tcp   filtered microsoft-ds
993/tcp   filtered imaps
5060/tcp  filtered sip
5080/tcp  open     onscreen
5555/tcp  filtered freeciv
8080/tcp  filtered http-proxy
12345/tcp filtered netbus
49152/tcp filtered unknown
Device type: WAP|general purpose|switch|storage-misc|remote management|firewall
Running (JUST GUESSING): AVM embedded (87%), Linksys embedded (87%), Netgear embedded (87%), Linux 2.6.X (85%), Extreme Networks ExtremeXOS 12.X (85%), Western Digital embedded (85%), Supermicro embedded (85%), WatchGuard Fireware 11.X (85%)
OS CPE: cpe:/h:avm:fritz%21box_fon_wlan_7050 cpe:/h:linksys:wag200g cpe:/h:netgear:dg834gt cpe:/o:linux:linux_kernel:2.6.29 cpe:/o:extremenetworks:extremexos:12.5.4 cpe:/o:linux:linux_kernel:2.6.17.14 cpe:/o:watchguard:fireware:11.3
Aggressive OS guesses: AVM FRITZ!Box FON WLAN 7050, Linksys WAG200G, or Netgear DG834GT wireless broadband router (87%), Linux 2.6.29 (85%), Linux 2.6.8 (x86) (85%), Extreme Networks ExtremeXOS 12.5.4 (85%), Western Digital MyBook World Edition 2 NAS device (Linux 2.6.17.14) (85%), Supermicro IPMI remote management (85%), WatchGuard firewall (Fireware 11.3) (85%)
No exact OS matches for host (test conditions non-ideal).
Uptime guess: 0.016 days (since Fri Sep 28 11:01:04 2018)
TCP Sequence Prediction: Difficulty=201 (Good luck!)
IP ID Sequence Generation: All zeros

Read data files from: /usr/local/bin/../share/nmap
OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 27.05 seconds
           Raw packets sent: 1134 (54.936KB) | Rcvd: 1039 (41.900KB)


---

Most likely some router with iot gateway function
---

Jaime Jiménez, [7 Dec 2018 at 12:39:13]:
There was a presentation yesterday at Black Hat Europe on the issue. I contacted the authors before, will do again.

I was thinking of writing a blog post

or something light

what could be the right venue? my blog?

Specially clarifying that UDP+NOSEC is a bad idea

---

Jaime Jiménez, [1 Oct 2018 at 23:16:33]:
i'd love to know of any other tool that has that kind of info. In order to figure out about exiting CoAP endpoints.

600k devices is a low estimate

Carsten Bormann, [1 Oct 2018 at 23:17:42]:
We won't see most tradfri controllers

You'd have to do lots of market research on platforms like Bosch to find the firewalled ones

I've heard industry research outfits are good at that :-)

---

https://www.us-cert.gov/ncas/bulletins/SB19-077
https://nvd.nist.gov/vuln/detail/CVE-2019-9750
https://jira.iotivity.org/browse/IOT-3267
https://nvd.nist.gov/vuln-metrics/cvss/v2-calculator?version=2&name=CVE-2019-9750&vector=(AV:N/AC:L/Au:N/C:N/I:P/A:P)
https://www.wirehive.com/thoughts/growth-amplified-udp-reflection-ddos-attacks/
https://www.secplicity.org/2018/06/14/how-udp-amplification-is-taking-ddos-attacks-to-the-next-level/
iotivity -- iotivity	In IoTivity through 1.3.1, the CoAP server interface can be used for Distributed Denial of Service attacks using source IP address spoofing and UDP-based traffic amplification. The reflected traffic is 6 times bigger than spoofed requests. This occurs because the construction of a "4.01 Unauthorized" response is mishandled. NOTE: the vendor states "While this is an interesting attack, there is no plan for maintainer to fix, as we are migrating to IoTivity Lite."

---

Speaking of network operation, we are seeing some stirrings of change, but it appears to be a rather conservative area, and adoption of new network management tools and practices takes time.

The Internet converged on using the Simple Network Management Protocol (SNMP) a quarter of a century ago, and despite its security weaknesses, its inefficiency, its incredibly irritating use of ASN.1, and its use in sustaining some forms of DDOS attacks, it still enjoys widespread use. But SNMP is only a network monitoring protocol, not a network configuration protocol, as anyone who has attempted to use SNMP write operations can attest.

The more recent Netconf and YANG efforts are attempting to pull this area of configuration management into something a little more usable than expect scripts driving CLI interfaces on switches. At the same time, we are seeing orchestration tools such as Ansible, Chef, NAPALM and SALT enter the network operations space, permitting the orchestration of management tasks over thousands of individual components. These network operations management tools are welcome steps forward to improve the state of automated network management, but it’s still far short of a desirable endpoint.

In the same time period as we appear to have advanced the state of automated control systems to achieve the driverless autonomous car, the task of fully automated network management appears to have fallen way short of the desired endpoint. Surely it must be feasible to feed an adaptive autonomous control system with the network’s infrastructure and available resources, and allow the control system to monitor the network and modify the operating parameters of network components to continuously meet the network’s service level objectives? Where’s the driverless car for driving networks? Maybe the next ten years might get us there.

- The Evolution of Cyber Attacks

At the same time as we have seen announcements of ever increasing network capacity within the Internet we’ve seen a parallel set of announcements that note new records in the aggregate capacity of Denial of Service attacks. The current peak volume is an attack of some 1.7Tbps of malicious traffic.

Attacks are now commonplace. Many of them are brutally simple, relying on a tragically large pool of potential zombie devices that are readily subverted and co-opted to assist in attacks. The attacks are often simple forms of attack, such as UDP reflection attacks where a simple UDP query generates a large response. The source address of the query is forged to be the address of the intended attack victim, and not much more need be done. A small query stream can result in a massive attack. UDP protocols such as SNMP, NTP, the DNS and memcache have been used in the past and doubtless will be used again.

Why can’t we fix this? We’ve been trying for decades, and we just can’t seem to get ahead of the attacks. Advice to network operators to prevent the leakage of packets with forged source addresses, RFC 2827, was published in two decades ago in 1998. Yet massive UDP-based attacks with forged source addresses persist all the way through today. Aged computer systems with known vulnerabilities continued to be connected to the Internet and are readily transformed into attack bots.

The picture of attacks is also becoming more ominous. Previously attributed to ‘hackers’ it was quickly realised that a significant component of these hostile attacks had criminal motivations. The progression from criminal actors to state-based actors is also entirely predictable, and we are seeing an escalation of this cyber warfare arena with the investment in various forms of exploitation of vulnerabilities being seen as part of a set of desirable national capabilities.

It appears that a major problem here is that collectively we are unwilling to make any substantial investment in effective defence or deterrence. The systems that we use on the Internet are overly trusting to the point of irrational credulity. For example, the public key certification system used to secure web-based transactions is repeatedly demonstrated to be entirety untrustworthy, yet that’s all we trust. Personal data is continually breached and leaked, yet all we seem to want to do is increase the number and complexity of regulations rather than actually use better tools that would effectively protect users.

The larger picture of hostile attack is not getting any better. Indeed, it’s getting very much worse. If any enterprise has a business need to maintain a service that is always available for use, then any form of in-house provisioning is just not enough to be able to withstand attack. These days only a handful of platforms are able to offer resilient services, and even then it’s unclear whether they could withstand the most extreme of attacks. There is a constant background level of scanning and probing going on in the network, and any form of visible vulnerability is ruthlessly exploited. One could describe today’s Internet as a toxic wasteland, punctuated with the occasional heavily defended citadel. Those who can afford to locate their services within these citadels enjoy some level of respite from this constant profile of hostile attack, while all others are forced to try and conceal themselves from the worst of this toxic environment, while at the same time aware that they will be completely overwhelmed by any large scale attack.

It’s a sobering through that about one half of the world’s population are now part of this digital environment. A more sobering thought is that many of today’s control systems, such as power generation and distribution, water distribution, and road traffic control systems are exposed to the Internet. Perhaps even more of a worry is the increasing use of the Internet in automated systems that include various life support functions. The consequences of massive failure of these systems in the face of a sustained and damaging attack cannot be easily imagined.

- The Internet of Billions of Tragically Stupid Things

What makes this scenario even more depressing is the portent of the so-called Internet of Things.

In those circles where Internet prognostications abound and policy makers flock to hear grand visions of the future, we often hear about the boundless future represented by this “Internet of Things. This phrase encompasses some decades of the computing industry’s transition from computers as esoteric pieces of engineering affordable only by nations, to mainframes, desktops, laptops, handhelds, and now wrist computers. Where next? In the vision of the Internet of Things we are going to expand the Internet beyond people and press on with using billions of these chattering devices in every aspect of our world.

What do we know about the “things” that are already connected to the Internet?

Some of them are not very good. In fact some of them are just plain stupid. And this stupidity is toxic, in that their sometime inadequate models of operation and security affects others in potentially malicious ways. Doubtless if such devices were constantly inspected and managed we might see evidence of aberrant behaviour and correct it. But these are unmanaged devices that are all but invisible. There are the controller for a web camera, the so-called “smart” thin in a smart television, or what controls anything from a washing machine to a goods locomotive. Nobody is looking after these devices.

When we think of an Internet of Things we think of a world of weather stations, web cams, “smart” cars, personal fitness monitors and similar. But what we tend to forget is that all of these devices are built upon layers of other people’s software that is assembled into a product at the cheapest possible price point. It may be disconcerting to realise that the web camera you just installed has a security model that can be summarised with the phrase: “no security at all”, and its actually offering a view of your house to the entire Internet. It may be slightly more disconcerting to realise that your electronic wallet is on a device that is using a massive compilation of open source software of largely unknown origin, with a security model that is not completely understood, but appears to be susceptible to be coerced into being a “yes, take all you want”.

It would be nice to think that we’ve stopped making mistakes in code, and from now on our software in our things will be perfect. But that’s hopelessly idealistic. It’s just not going to happen. Software will not be perfect. It will continue to have vulnerabilities. It would be nice to think that this Internet of Things is shaping up as a market where quality matters, and consumers will select a more expensive product even though its functional behaviour is identical to a cheaper product that has not been robustly tested for basic security flaws. But that too is hopelessly naive.

The Internet of Things will continue to be a market place where the compromises between price and quality will continue to push us on to the side of cheap rather than secure. What’s going to stop us from further polluting our environment with a huge and diverse collection of programmed unmanaged devices with inbuilt vulnerabilities that will be all too readily exploited? What can we do to make this world of these stupid cheap toxic things less stupid and less toxic? Workable answers to this question have not been found so far.
