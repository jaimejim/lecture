# Finding your CoAP devices

While we have explained how data is transmitted and how it looks like and we do have URL to share the location of our CoAP devices, we have to figure out **how to find CoAP devices to begin with?** and, once we know the URL of a CoAP device, **how do we know what is on that device?**

## Discovering Resources

Let's start with the second question first, how do you find the resources that a device has *before* asking it for them? Indeed, if we do not have any idea of what the device is supposed to do, it would be impossible to query for anything as we would not know the `path` part of the URL.

To fix that problem every CoAP endpoint comes with a default URI that they all know, the ["well-known"](https://tools.ietf.org/html/rfc8428) URI. If a server supports representations in [CoRE Link Format](https://tools.ietf.org/html/rfc6690) it must always support too the URI called `/.well-known/core`. That way any CoAP client can always send a `GET` request to a CoAP Server on `/.well-known/core` and will get in return a list of hypermedia links to other resources hosted in that server. Moreover, it can also filter the output to limit the ammount of responses with query filetering `?`. For example we could query a CoAP server for all resources of the type `rt` = temperature.

```txt
REQ: GET coap://coap.me:5683/.well-known/core?rt=temperature
RES: 2.05 Content
   </sensors/temp1>;rt="temperature",
   </sensors/temp2>;rt="temperature",
```

Once the client knows that there are two sensors of the type `temperature`, it can decide to follow one of the presented links and query it, for example the first one `/sensors/temp1`. That way it can find the current value of the resource as we learnt in the previous section.

```txt
REQ: GET coap://coap.me:5683/sensors/temp1
RES: 2.05 Content
   [{"n":"urn:dev:ow:10e2073a01080063","u":"Cel","v":23.1}]
```

## Discovering CoAP Endpoints

We have explained how to discover the resources on a coap endpoint but we have not mentioned how endpoints can be found to begin with.

Networks are and will continue to be heterogeneous, some scenarios foresee the use of multicast, while others have a master/slave approach. Some scenarios will have NATs and firewalls while other - more ideal - will simply have globally addressable IPv6 addresses. Some devices will be asleep while others will be permanently connected. 

In many IoT applications, direct discovery of resources is not
   practical due to sleeping nodes, disperse networks, or networks where
   multicast traffic is inefficient.  These problems can be solved by
   employing an entity called a Resource Directory (RD), which contains
   information about resources held on other servers, allowing lookups
   to be performed for those resources.  The input to an RD is composed
   of links and the output is composed of links constructed from the
   information stored in the RD.  This document specifies the web
   interfaces that a Resource Directory supports for web servers to
   discover the RD and to register, maintain, lookup and remove
   information on resources.  Furthermore, new target attributes useful
   in conjunction with an RD are defined.

To palliate these problems at CoRE there is a function used only to register and lookup for CoAP endpoints and their resources called [Resource Directory (RD)](https://tools.ietf.org/html/draft-ietf-core-resource-directory-20). An RD is an entity whose input and oputput are links and that only stores links; like a link repository.

```txt
                Registration         Lookup
                 Interface         Interface
     +----+          |                 |
     | EP |----      |                 |
     +----+    ----  |                 |
                   --|-    +------+    |
     +----+          | ----|      |    |     +--------+
     | EP | ---------|-----|  RD  |----|-----| Client |
     +----+          | ----|      |    |     +--------+
                   --|-    +------+    |
     +----+    ----  |                 |
     | CT |----      |                 |
     +----+
```



Finding a Resource Directory
multicast 224.0.1.187  FF0X::FE
As we saw CoAP allows for Resource Directory

Registration
Lookup

```txt
                Registration         Lookup
                 Interface         Interface
     +----+          |                 |
     | EP |----      |                 |
     +----+    ----  |                 |
                   --|-    +------+    |
     +----+          | ----|      |    |     +--------+
     | EP | ---------|-----|  RD  |----|-----| Client |
     +----+          | ----|      |    |     +--------+
                   --|-    +------+    |
     +----+    ----  |                 |
     | CT |----      |                 |
     +----+


```

