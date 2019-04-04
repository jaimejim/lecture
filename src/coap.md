# The Constrained Application Protocol (CoAP)

The Constrained Application Protocol (CoAP) is a protocol intended to be used in low-powered devices and networks. That is networks of very low throughput and devices that run on battery. Such devices often have limited memory and CPU too, often as little as 1.5kB of RAM and about 5 kB of ROM.

CoAP is a [REST](https://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm) protocol largely inspired by HTTP. However it brings the Web Server concept to the very constrained space where IoT devices are the ones exposing their resources.

Like HTTP, CoAP also uses request/response communication, being CoAP messages very small, of tens of bytes on a 6LoWPAN network and of the hundreds of bytes on a less constrained one.

CoAP devices are intended to come from multiple manufacturers, much like the World Wide Web enabled anyone to have an HTTP server. While arriving to a common data modeling and representation form as HTML for HTTP has not yet happened for CoAP, the technology is already mature in terms of networking layout.

Devices may be sensors and actuators, exposing resources (i.e. measurable data) or enabling interaction with the environment. They have to find ways to register to services, find each other, and interact without much human control or intervention.

Ideally we would be talking about a decentralized scenario, as on the Web. However, much like the Web is moving back to the mainframe format, most comertial IoT solutions today feature a central control point, manager, broker or orchestrating entity.  

## A CoAP Message

As explained in [RFC7252](https://tools.ietf.org/html/rfc7252) CoAP messages are very compact and by default transported over UDP, there is TCP support too for NATed environments, but UDP was the intended original transport. CoAP messages are encoded in a simple binary format with a very compact header of 4 bytes. The `version` indicates the CoAP version, the message `type` can be (`CON`, `NON`, `ACK` and `RST`). This is followed by a variable-length `Token` value, which can be between 0 and 8 bytes long. Then it has the response or method `codes` (e.g. `0.01` (GET) , `0.02` (POST) , `2.05` (Success) , `4.04` (Not Found)). Then there is the Message ID, which is a 16 bit field to detect message duplication. There are then several Options that allow for extensibility and finally the payload with the actual data.

``` md
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |Ver| T |  TKL  |      Code     |          Message ID           |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |   Token (if any, TKL bytes) ...
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |   Options (if any) ...
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |1 1 1 1 1 1 1 1|    Payload (if any) ...
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

## Unreliable Transmission

As mentioned before, CoAP was intended for *UDP transmission*, which is unreliable. This means that CoAP request and response messages may arrive out of order, appear duplicated, or go missing without notice. For this reason, CoAP implements a lightweight reliability mechanism using the `type`field, introducing both Confirmable and Non-Confirmable messages. If the messages is CON as in Confirmable, that means that either the request or the response require confirmation, that is an acknowledgemnt of receival or ACK.

To ensure retransmission in case of loss, the CoAP endpoint sending the CON message keeps track of a `timeout` for the message and a `retransmission counter` to keep track of how many times the message was sent.

Below it is shown how a simple CoAP Request would look like. The CoAP Client sends a request message `GET /temperature` that requires requires an acknowledgment message from the server as it uses the type `CON`. The `Message ID` is `0x7d36` which will be used in that acknowledgment message. Without the message ID, a client that makes two GET requests and gets two responses won’t know which response goes with which request. A single CoAP request may trigger several responses, and the same `token` is used in every response, not just the initial acknowledgment. Responses sent after the acknowledgment will have new message IDs, but they’ll be tied to the original request by the token.

Since the first message got lost the client waits for a time until the `timeout` is triggered and the message is sent again.

```md
   Client  Server  (hosting a resource at "/temperature" with 
      |      |       value 22.3 degrees centigrade)
      |      |
      +----X |   Header: GET (T=CON, Code=0.01, MID=0x7d36)
      | GET  |   Token: 0x31
      |      |   Uri-Path: "temperature"
   TIMEOUT   |
      |      |
      +----->|   Header: GET (T=CON, Code=0.01, MID=0x7d36)
      | GET  |   Token: 0x31
      |      |   Uri-Path: "temperature"
      |      |
      |      |
      |<-----+   Header: 2.05 Content (T=ACK, Code=2.05, MID=0x7d36)
      | 2.05 |   Token: 0x31
      |      |   Content-Format: text/plain;charset=utf-8
      |      |   Payload: "22.3 C"
```

In the response you can see a `Content-Format` field explaining the format of the content. This is a CoAP option that will be explained in the next section.

### Convenient Multicast

Although the original lack of TCP, which now [is supported](https://tools.ietf.org/html/rfc8323) too there is an added benefit of using UDP; a CoAP client can use UDP multicast to broadcast a message to every machine on the local network.

In some home automation cases, all devices will be under the same subnet, your thermostat, refrigerator, television, light switches, and other home appliances have cheap embedded processors that communicate over a local low-power network. This lets your appliances coordinate their behavior without direct input from you. When you turn the oven on, the climate control system can notice this event and turn down the heat in the kitchen. You can pull out your mobile phone, get a list of all the lights in your current room, and dim the lights through your phone, without having to go over to the light switch.

Nevertheless Multicast must be used with care as it is easy to greate other network issues involving broadcast storms.

## Revisiting web linking

Web Linking in CoRE is defined by [RFC6690](https://tools.ietf.org/html/rfc6690) in a similar way as HTTP defines it in [RFC5988](https://tools.ietf.org/html/rfc5988) and URI's are defined in [RFC3986](https://tools.ietf.org/html/rfc3986).

### URIs

We will briefly have to explain what a URI is as it is probably very intuitive since it is part of our daily HTTP browsing. Everyone can recognize the following syntax of the URI scheme.

```md
   foo://example.com:8042/over/there?name=ferret#nose
   \_/   \_____________/\________/\________/ \___/
    |           |            |            |        |
   scheme     authority       path        query   fragment
```

A URI can be classified as a locator, a name, or both. Uniform Resource Locator (URL) refers to URIs that allow for identification *and location* of a resource by describing how to access it on a network. Uniform Resource Name (URN) are used as *globally unique* identifiers. Universally Unique IDentifier ([UUID](https://tools.ietf.org/html/rfc4122)) are unique and persistent URNs that *do not require a central registration authority*.

The URI format is used on pretty much any application protocol in existance, from FTP, HTTP or telnet to CoAP. Some with little variations in the syntax, like that of the email [`mailto`](https://tools.ietf.org/html/rfc6068) or the telephone [`tel`](https://tools.ietf.org/html/rfc3966), as you can see below.

```md
   tel     :                        +34-690-555-1212
   mailto  :   infobot@example.com                      ?subject=issue
   urn:uuid:                        f81d4fa...a0c91e6bf6
   ftp     :// 196.4.160.12        /rfc/rfc1808.txt
   http    :// www.ietf.org        /rfc/rfc2396.txt
   \______/   \________________/ \_________________/\___________/
     |              |                 |                         |
   scheme        authority           path                     query
```

The CoAP URI is similar: `"coap:" "//" host [ ":" port ] path-abempty [ "?" query ]`, the examples below show several usages of it. You can try some yourself at [coap.me](http://coap.me).

```md
   coap://sensor.iot.org:5683 /lamp             ?rt=light-lux#rec=3
   coap://[2001:db8:::1]:5683 /temperature      ?ct=60
   coap://coap.me:5683        /.well-known/core
   \__/  \_________________/\______________/\__________/\___/
     |           |                 |                |           |
   scheme     authority           path            query     fragment
```

### Media Types

In addition to URIs, it is important to know other concepts used in REST applications and systems. In  particular concepts like media-types, content-types, and content coding. There is a very good [clarification document](https://tools.ietf.org/html/draft-bormann-core-media-content-type-format-00), 

1. **Media-Type** is often abbreviated as "mt", they are stored at the [IANA Registry](https://www.iana.org/assignments/media-types/media-types.xhtml). Originally it was a term that identify the general type like "text" or "audio". Nowadays it is the combination of a `type` and a `subtype` like "audio/ogg", "text/plain" or "text/html". In IoT common media types are used for applications that consume the information like "application/senml+cbor" or "application/link-format".

2. **Content-Type** they are the top `type` Media-Type, optionally associated with parameters (separated from the media type name and from each other by a semicolon).

3. **Content-Coding** are registered in [IANA](http://www.iana.org/assignments/http-parameters) too and they are essentially numbers that identify a *potential transformation to the representation of a resource*. This might sound confusing cause it is, but think about it as a compact way to indicate that you want to compress/deflate/encrypt some resource value.

4. **Content-Format** is again a number on a registry. This number identifies the combination of a Content-Type and a Content-Coding defined by the [CoAP Content-Formats registry](https://www.iana.org/assignments/core-parameters/core-parameters.xhtml).

Keeing all this in mind, the following sentence will now make more sense:

**The content-format `60` identifies the `application/cbor` media-type, defined by [RFC7049](http://www.iana.org/go/rfc7049).**

At this point we are capable of having applications point to specific locations using URLs, indicating concrete protocols used to get resources and serving those resources in a specific format.

Just like on the web a HTTP Client (e.g. a browser) can understand the common media types for HTML `text/html` and  images `image/jpeg`; a CoAP Client could understand that `application/cbor` implies data will be send in [CBOR](http://www.iana.org/go/rfc7049) format.

### Data Serialization

We have explained how resources exposed by a CoAP Server can be addressed using URIs and how the client can tell the server how content is to be presented and which format to use. We have even mention how that content can be *serialized*, that is how it is sent on the wire, by using CBOR.
While CBOR is binary and hard to read without translation tools for humans, there is a serialization format called [SenML](https://tools.ietf.org/html/rfc8428) that is much readable.

SenML can be represented in various formats with their respective media types which are: `application/senml-exi` , `application/senml+cbor` , `application/senml+json` , `application/senml+xml`. We will use JSON as it is one of the most common and it is easily readable - and writeable - in this document. To illustrate with an example, we have the following exchange between two CoAP endpoints.

The CoAP client will **request** the resources available under the path `/device`. The request asks for the content format identifier of `110` which means `application/senml+json` format. Other CoAP fields like `token` or `version` have been omitted for simplicity.

```md
REQ: GET coap://coap.me:5683/device?ct=110
```

The **response** below uses the CoAP `code` of 2.05, which means that the operation was successful The payload contains a times series of measurements with the base name `bn`indicating the URN of the device, the base time `bt`when the time series started, the units `bu` and the version used `ver` of `2`.

After the base values there is the individual measurements containing the field name `n` either current or voltage, the time it was measured `t` , which is negative for historical data before the base measurement. Finally 

```json
RES: 2.05 Content
    [   {"bn":"urn:dev:mac:0024befffe804ff1/","bt":1276020076,"bu":"A","ver":2},
        {"n":"voltage","u":"V","v":0},
        {"n":"current","t":-4,"v":1.3},
        {"n":"current","t":-3,"v":1,"s":33.44},
        {"n":"current","v":1.7}
    ]
```

The output looks like JSON and is easily understandable by humans. However, given that we have constrains in bandwidth, it would be very useful to have a data format that, among other features, is much smaller in message size. For that purpose the Concise Binary Object Representation ([CBOR](https://cbor.io)) was created. Below you can see the same payload in CBOR in only 147 Bytes.

```md
00000000: 85a4 2178 1d75 726e 3a64 6576 3a6d 6163  ..!x.urn:dev:mac
00000010: 3a30 3032 3462 6566 6666 6538 3034 6666  :0024befffe804ff
00000020: 312f 221a 4c0e 856c 2361 4163 7665 7202  1/".L..l#aAcver.
00000030: a300 6776 6f6c 7461 6765 0161 5602 00a3  ..gvoltage.aV...
00000040: 0067 6375 7272 656e 7406 2302 fb3f f4cc  .gcurrent.#..?..
00000050: cccc cccc cda4 0067 6375 7272 656e 7406  .......gcurrent.
00000060: 2202 0105 fb40 40b8 51eb 851e b8a2 0067  "....@@.Q......g
00000070: 6375 7272 656e 7402 fb3f fb33 3333 3333  current..?.33333
00000080: 33                                       3
```

## Finding your Things

While we have explained how data is transmitted and how it looks like and we do have URL to share the location of our CoAP devices, we have to figure out **how to find CoAP devices to begin with?** and, once we know the URL of a CoAP device, **how do we know what is on that device?**

### Discovering Resources

Let's start with the second question first, how do you find the resources that a device has *before* asking it for them? Indeed, if we do not have any idea of what the device is supposed to do, it would be impossible to query for anything as we would not know the `path` part of the URL.

To fix that problem every CoAP endpoint comes with a default URI that they all know, the ["well-known"](https://tools.ietf.org/html/rfc8428) URI. If a server supports representations in [CoRE Link Format](https://tools.ietf.org/html/rfc6690) it must always support too the URI called `/.well-known/core`. That way any CoAP client can always send a `GET` request to a CoAP Server on `/.well-known/core` and will get in return a list of hypermedia links to other resources hosted in that server. Moreover, it can also filter the output to limit the ammount of responses with query filetering `?`. For example we could query a CoAP server for all resources of the type `rt` = temperature.

```txt
REQ: GET coap://coap.me:5683/.well-known/core?rt=temperature
RES: 2.05 Content
   </sensors/temp1>;rt="temperature",
   </sensors/temp2>;rt="temperature",
```

Once the client knows that there are two sensors of the type `temperature`, it can decide to follow one of the presented links and query it, for example the first one `/sensors/temp1`. That way it can learn the current value of the resource as we learnt in the previous section.

```txt
REQ: GET coap://coap.me:5683/sensors/temp1
RES: 2.05 Content
   [
     {"n":"urn:dev:ow:10e2073a01080063","u":"Cel","v":23.1}
   ]
```

### Discovering CoAP Endpoints

As we saw CoAP allows for Resource Directory