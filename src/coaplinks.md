# CoAP Web Linking and Serialization

Web Linking is a fundamental part of any REST protocol, and it is the feature that makes it more versatile and flexible when compared with other alternative technologies. In CoRE, linking is defined by [RFC6690](https://tools.ietf.org/html/rfc6690) in a similar way as HTTP defines it in [RFC5988](https://tools.ietf.org/html/rfc5988). URI's are defined in [RFC3986](https://tools.ietf.org/html/rfc3986). We will see some of the basic concepts in this chapter.

## URIs

We will briefly have to explain what a URI is as it is probably very intuitive since it is part of our daily HTTP browsing. Everyone can recognize the following syntax of the URI scheme.

```md
   foo://example.com:8042/over/there?name=ferret#nose
   \_/   \_______________/\________/\__________/\___/
    |           |              |          |       |
   scheme     authority       path      query   fragment
```

A URI can be classified as a locator, a name, or both. Uniform Resource Locator (URL) refers to URIs that allow for identification *and location* of a resource by describing how to access it on a network. Uniform Resource Name (URN) are used as *globally unique* identifiers. Universally Unique IDentifier ([UUID](https://tools.ietf.org/html/rfc4122)) are unique and persistent URNs that *do not require a central registration authority*.

The URI format is used on pretty much any application protocol in existence, from FTP, HTTP or telnet to CoAP. Some with little variations in the syntax, like that of the email [`mailto`](https://tools.ietf.org/html/rfc6068) or the telephone [`tel`](https://tools.ietf.org/html/rfc3966), as you can see below.

```md
   tel     :                        +34-690-555-1212
   mailto  :   infobot@example.com                      ?subject=issue
   urn:uuid:                        f81d4fa...a0c91e6bf6
   ftp     :// 196.4.160.12        /rfc/rfc1808.txt
   http    :// www.ietf.org        /rfc/rfc2396.txt
   \______/   \__________________/ \___________________/\___________/
     |              |                       |                  |
   scheme        authority                 path              query
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

## Media Types

In addition to URIs, it is important to know other concepts used in REST applications and systems. In  particular concepts like media-types, content-types, and content coding. There is a very good [clarification document](https://tools.ietf.org/html/draft-bormann-core-media-content-type-format-00), 

1. **Media Type:** Media types are registered in the [IANA Media Types registry](https://www.iana.org/assignments/media-types/media-types.xhtml). They identify the general `type` like "audio" or "text" combined with a `subtype` like "ogg", "plain", or "html", resulting in strings like "audio/ogg", "text/plain", and "text/html". In IoT, common media types are used for applications that consume the information like "application/senml+cbor" or "application/link-format". Media-Types are registered in IANA.

2. **Content Type:** Content types are used in HTTP to specify a media type, optionally associated with additional, media type-specific parameters (separated from the media type and each other by semicolons). 

3. **Content Coding:** Content codings are registered in the [IANA HTTP Parameters registry](http://www.iana.org/assignments/http-parameters) and identify an optional *transformation to the representation of a resource*, such as the compression or encryption of some data.

4. **Content Format:** Content formats are used in CoAP to identify the combination of a content type and a content coding. They are registered in the [IANA CoAP Content-Formats registry](https://www.iana.org/assignments/core-parameters/core-parameters.xhtml).

For example, the content-format `11060` identifies the content type `application/cbor` (defined by [RFC7049](http://www.iana.org/go/rfc7049)) combined with the content coding `deflate` (defined by [RFC7049](http://www.iana.org/go/rfc1951)).

At this point, we are capable of having applications point to specific locations using URLs, indicating concrete protocols used to get resources, and serving those resources in a specific format.

Just like a web browser understands HTTP and the common media types for HTML `text/html` and  images `image/jpeg`, an IoT client could understand CoAP and common media types like `application/cbor`, which implies data will be send in [CBOR](http://www.iana.org/go/rfc7049) format.

## Link Attributes

A commonly confused term is link or target **attributes**, which are defined in [RFC6690](https://tools.ietf.org/html/rfc6690) and [RFC5988](https://tools.ietf.org/html/rfc5988) and describe information useful to be added to a target link. They are hints indicating information about the link. In HTTP "media" for example indicates the destination medium, "rel" specifies the relation with another link, and so on. 

In CoAP link attributes can define some properties of a device, for example the Open Interconnect Consortium (OIC) [defines](https://www.iana.org/assignments/core-parameters/core-parameters.xhtml#rt-link-target-att-value) resource type "rt" link attributes that help understand which type of device is behind the link, like "oic.d.fan" or "oic.d.light". They use a urn-like type of format. Another link attribute that is used today is interface description "if" which lets the client know that the link points to an interface of some kind. 

## Data Serialization

We have explained how resources exposed by a CoAP Server can be addressed using URIs and how the client can tell the server how content is to be presented and which format to use. We have even mention how that content can be *serialized*, that is how it is sent on the wire, by using CBOR.
While CBOR is binary and hard to read without translation tools for humans, there is a serialization format called [SenML](https://tools.ietf.org/html/rfc8428) that is much readable.

SenML can be represented in various formats with their respective media types which are: `application/senml-exi` , `application/senml+cbor` , `application/senml+json` , `application/senml+xml`. We will use JSON as it is one of the most common and it is easily readable - and writable - in this document. To illustrate with an example, we have the following exchange between two CoAP endpoints.

The CoAP client will **request** the resources available under the path `/device`. The request asks for the content format identifier of `110`, which means `application/senml+json` without any additional content coding.

```md
REQ: GET coap://coap.me:5683/device?ct=110
```

The **response** below uses the CoAP `code` of 2.05, which means that the operation was successful The payload contains a times series of measurements with the base name `bn` indicating the URN of the device, the base time `bt` when the time series started, the units `bu` and the version used `ver` of `2`.

After the base values there is the individual measurements containing the field name `n` either current or voltage, the time it was measured `t` , which is negative for historical data before the base measurement. The SenML output would look like the one below:

```json
RES: 2.05 Content
    [   {"bn":"urn:dev:mac:0024befffe804ff1/","bt":1276020076,"bu":"A","ver":2},
        {"n":"voltage","u":"V","v":0},
        {"n":"current","t":-4,"v":1.3},
        {"n":"current","t":-3,"v":1,"s":33.44},
        {"n":"current","v":1.7}
    ]
```

At first glance, it looks like JSON and is easily understandable by humans. However, given that we have constrains in bandwidth, it would be very useful to have a data format that, among other features, is much smaller in message size. For that purpose the Concise Binary Object Representation ([CBOR](https://cbor.io)) was created. Below you can see the same payload in CBOR in only 147 Bytes.

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
