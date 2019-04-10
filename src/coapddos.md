# DDOS using CoAP

That CoAP can be used in malicious DDOS attacks is news but at the same time it isn't. Already when the CoAP RFC was publish it came with a [Security Section](https://tools.ietf.org/html/rfc7252#section-11) with that caveat in mind, being UDP a choice made to process message faster and scale better. Today, CoAP is now deployed widely enough to be more than noticeable by malicious attackers as shown in [1](http://i.blackhat.com/eu-18/Thu-Dec-6/eu-18-Maggi-When-Machines-Cant-Talk-wp.pdf) , [2](http://rvasec.com/slides/2018/Rand_Dennis-RVAsec_2018.pdf) , [3](https://nvd.nist.gov/vuln/detail/CVE-2019-9750) and  [4](https://www.zdnet.com/article/the-coap-protocol-is-the-next-big-thing-for-ddos-attacks).

As CoAP becomes more mainstream, it is likely that there will be more attacks taking advantage of misconfigured endpoints. In fact CoAP has already been *"occasionally used as a basis of DDOS attacks, with increasing frequency, reaching [55Gbps on average](https://www.zdnet.com/article/the-coap-protocol-is-the-next-big-thing-for-ddos-attacks/), and with the largest one clocking at 320Gbps"*. Apparently the attacks are short-lived, lasting on average *"just over [90 seconds](https://www.securityweek.com/attackers-use-coap-ddos-amplification)"* and feature around 100 packets per second.

An attack of 320Gbps is already huge, since as we saw [On DDOS Attacks](./udpddos.md) Mirai clocked at 600Gbps. It is thus likely that a 1Tbps attack using CoAP will occur sonn, maybe even larger. The current peak volume is an attack of some 1.7Tbps of malicious traffic.

As a danish researcher at [RVAsec](https://www.youtube.com/watch?v=DX68vb2XjdQ&feature=youtu.be&t=19m28s) points out, *the CoAP protocol was not yet mainstream back in 2017, but the number of visible CoAP endpoints has increased rapidly from about `6500` to `220000` between november 2017 and may 2018, to more than `738041` in October 2018*.

That number has decreased as devices are being patched and today we would find about `436854` running `shodan count port:5683`, out of which `305051` are in China. There are of course orders of magnitude more endpoints that are not openly visible on the Internet.

These attacks are based on the two vulnerabilities of UDP-based protocols mentioned in the [previous section](./udpddos.md).

## IP Address Spoofing

Due to the lack of handshake in UDP, [RFC7252 Section 11.4](https://tools.ietf.org/html/rfc7252#section-11.4) warns that there are some message `types` that can be spoofed on **unsecured** CoAP networks. They can range from simple, like a `RST` or an `ACK`in response to a `CON` message which would cause an error in the communication, to disruptive when spoofing a **multicast request** (cause CoAP does support multicast) for a target node. This may result in a DOS to a victim or in the congestion/collapse of the network.

Moreover under this attack, a constrained node with limited total energy available may exhaust that energy much more quickly than planned (battery depletion attack).

## Amplification

As it is explained on [RFC7252 Section 11.3](https://tools.ietf.org/html/rfc7252#section-11.3) CoAP responses might be - and usually are - larger than their request. Think for example `GET /.well-known/core` that returns links to all resources, or a query to a complex sensor that returns a large JSON payload. I can imagine that if the Observe option is `obs = 0` then notifications to the victim would be sent automatically, increasing the length of the attack. Also if the crafted request asks for text-based `ct` the response would be larger than binary ones.

The draft then states: *"This is particularly a problem in nodes that enable `NoSec` access, are accessible from an attacker, and can access potential victims (e.g., on the general Internet), as the UDP protocol provides no way to verify the source address given in the request packet".*

### CoAP Amplification Factor

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
| Memcached (11211) |                 15 b |                 73/51000 ([!!](http://www.senki.org/memcached-on-port-11211-udp-tcp-being-exploited/)) |                       39.785 |

## Exploring Vulnerable Endpoints

[Shodan](https://www.shodan.io/) and [nmap](https://nmap.org/) are the preferred tools to find out about existing vulnerable CoAP endpoints, however the usability of the first one is limited without a paid account. Some of shodan's most basic commands are:

`shodan host [IP_ADDRESS_HERE]` to get info on a specific endpoint.

`shodan count port:5683 country:CN` to get all CoAP endpoints in China.

`shodan stats --limit 20 port:5683` to get some statistics CoAP endpoits out there.

`shodan search --fields ip_str,port,org,hostnames port:5683 country:CN` to get a list of CoAP endpoints, their specific IPs and hostname.

There is also a web interface that allows for searching, for example [CoAP Endpoints](https://www.shodan.io/search?query=port%3A5683). Looking a bit more on a couple of aleatory searched hosts shows two CoAP endpoints that potentially are discoverable over the Internet and vulnerable to UDP-based attacks.

```sh
$ shodan host 120.212.XXX.XXX
120.212.XXX.XXX
Hostnames:               XXX
City:                    Nizhniy Novgorod
Updated:                 2019-04-10T11:37:35.572911

Ports:
     80/tcp  
   5683/udp
```

```sh
$ shodan host 39.159.XXX.XXX
39.159.XXX.XXX
City:                    Nanchang
Updated:                 2019-04-10T11:39:13.342628

Ports:
   5683/udp
```

As we saw in the section [Finding your CoAP devices](./coapdiscovery.md) CoAP has an in-built discovery mechanism, thus sending a `GET` request message to the `/.well-known/core` resource might show information. Indeed, for example:

- The node in [Novgorod](https://pastebin.com/raw/zhZv67P5) shows the available resources, examples of the expected payloads and the interaction methods.

- Similarly the node in [Nanchang](https://pastebin.com/raw/CYMCFcG5) exposes network information, interfaces and all resources available, some even related to control information and the expected content-formats or passwords.

If we were interested which type of devices are these , it is easy to perform an [nmap scan](https://pastebin.com/raw/rb6Q75K9) to find out that several other TCP ports are also open, indicating that at least a portion of the devices are gateways with a dozen other services.

## Solutions

As we other DDOS attacks, some solutions are already at hand, some outlined in [RFC4732](https://tools.ietf.org/html/rfc4732), others on [RFC7252](https://tools.ietf.org/html/rfc7252#section-11) and on [iot-seccons](https://tools.ietf.org/html/draft-irtf-t2trg-iot-seccons-16). The guidelines to be followed when implementing and deploying CoAP networks that help prevent and palliate DOS attacks would be:

- Avoiding the use of `NoSec` mode, using instead any of the other [secure modes](https://tools.ietf.org/html/rfc7252#section-9).
- Avoid exposing CoAP endpoints to the wider Internet if they don't need to.
- Using a randomized `token` value.
- When using `CON` messages we can detect unexpected `ACK` `RST` from the deceived endpoint. However it is costly and not always feasible.
- Verify every now and then that observations are still valid by sending a `CON` from the observed CoAP resource.
- Limiting the response rate can also palliate the problem.
- Routers and middleboxes have high bandwidth, which is a scarce resource in the case of constrained networks. That will be a mitigating factor, making CoAP nodes less attractive for this attack.
- Large amplification factors should not be provided if the request is not authenticated.
- CoAP servers should not accept multicast requests that can not be authenticated in some way
- Ensure all default passwords are changed to strong passwords.
- Update IoT devices with security patches as soon as patches become available.
- Network operators could prevent the leakage of packets with forged source addresses by following the guidelines of [RFC2827](https://tools.ietf.org/html/rfc2827).

Nevertheless, as it is pointed out in [Another 10 years](./another10years.md):
> The Internet of Things will continue to be a market place where the compromises between price and quality will continue to push us on to the side of cheap rather than secure.