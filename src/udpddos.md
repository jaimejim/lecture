# On Internet Attacks

Distributed Denial of Service (DDoS) attacks are a common way to prevent Internet Services from running.  employing reflected UDP amplification are regularly used to disrupt networks and systems. The amplification allows one rented server to generate significant volumes of data, while the reflection hides the identity of the attacker. Consequently this is an attractive, low risk, strategy for criminals bent on vandalism and extortion. To measure the uptake of this strategy we analyse the results of running a network of honeypot UDP reflectors (median size 65 nodes) from July 2014 onwards. We explore the life cycle of attacks that use our reflectors, from the scanning phase used to detect our honeypot machines, through to their use in attacks. We see a median of 1 450 malicious scanners per day across all UDP protocols, and have recorded details of 5.18 million subsequent attacks involving in excess of 3.31 trillion packets. Using a capture-recapture statistical technique, we estimate that our reflectors can see between 85.1% and 96.6% of UDP reflection attacks over our measurement period.






[Another 10 years](./another10years.md)

The Evolution of Cyber Attacks
The Internet of Billions of Tragically Stupid Things

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


## Overview of Network Attacks on the Internet

(Refer to another10years for users and for attacks)
(Refer to another10years for iotattacks)

Here are some of my notes about the event, what are the causes and how can it be prevented.

## UDP-based Attacks

### Reflection Attack

asdasdasd

### Amplification Attack

asdasdasd

### Past attacks using UDP

NTP, SNMP, DNS