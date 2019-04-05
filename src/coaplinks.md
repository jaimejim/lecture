# CoAP Web Linking and Serialization

Web Linking in CoRE is defined by [RFC6690](https://tools.ietf.org/html/rfc6690) in a similar way as HTTP defines it in [RFC5988](https://tools.ietf.org/html/rfc5988) and URI's are defined in [RFC3986](https://tools.ietf.org/html/rfc3986).

## URIs

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

## Media Types

In addition to URIs, it is important to know other concepts used in REST applications and systems. In  particular concepts like media-types, content-types, and content coding. There is a very good [clarification document](https://tools.ietf.org/html/draft-bormann-core-media-content-type-format-00), 

1. **Media-Type** is often abbreviated as "mt", they are stored at the [IANA Registry](https://www.iana.org/assignments/media-types/media-types.xhtml). Originally it was a term that identify the general type like "text" or "audio". Nowadays it is the combination of a `type` and a `subtype` like "audio/ogg", "text/plain" or "text/html". In IoT common media types are used for applications that consume the information like "application/senml+cbor" or "application/link-format".

2. **Content-Type** they are the top `type` Media-Type, optionally associated with parameters (separated from the media type name and from each other by a semicolon).

3. **Content-Coding** are registered in [IANA HTTP parameters](http://www.iana.org/assignments/http-parameters) too and they are essentially numbers that identify a *potential transformation to the representation of a resource*. This might sound confusing cause it is, but think about it as a compact way to indicate that you want to compress/deflate/encrypt some resource value.

4. **Content-Format** is again a number on a registry. This number identifies the combination of a Content-Type and a Content-Coding defined by the [CoAP Content-Formats registry](https://www.iana.org/assignments/core-parameters/core-parameters.xhtml).

5. **Attributes** describe information useful in accessing the target link they can be found on [IANA](http://www.iana.org/assignments/http-parameters) and are defined for CoAP in [RFC6690](https://tools.ietf.org/html/rfc6690). For example `rt` to specify the resource type, `sz` for maximum size estimate or `obs` to indicate that the resource is [observable](https://tools.ietf.org/html/rfc7641). 

Keeing all this in mind, the following sentence will now make more sense:

**The content-format `60` identifies the `application/cbor` media-type, defined by [RFC7049](http://www.iana.org/go/rfc7049).**

At this point we are capable of having applications point to specific locations using URLs, indicating concrete protocols used to get resources and serving those resources in a specific format.

Just like on the web a HTTP Client (e.g. a browser) can understand the common media types for HTML `text/html` and  images `image/jpeg`; a CoAP Client could understand that `application/cbor` implies data will be send in [CBOR](http://www.iana.org/go/rfc7049) format.

## Data Serialization

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