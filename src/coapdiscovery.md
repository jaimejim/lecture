# Finding your CoAP devices

While we have explained how data is transmitted and how it looks like and we do have URLs to share the location of our CoAP devices, we have to figure out **how to find CoAP devices to begin with** and, once we know the URL of a CoAP device, **how do we know what is on that device**.

## Discovery steps

There are quite a few steps needed in order to retrieve that particular temperature measurement we are looking for. At a high level they are:

1. Find a Resource Directory (RD).

```txt
      GET coap://[FF0X::FE]/.well-known/core?rt=core.rd*
```

2. Find the Lookup Interface of the RD.

```txt
      GET coap://rd.jaime.win/.well-known/core?rt=core.rd-lookup-res
```

3. Find an endpoint or a temperature resource (for example) Registered in the RD.

```txt
      GET coap://rd.jaime.win/rd-lookup/res?rt=temperature
```

4. Query that Endpoint for the `/.well-known/core` resource in order to see what's there.

```txt
      GET coap://[2001:db8:3::123]:5683/.well-known/core?rt=temperature
```

5. Query the specific resource you are looking for in order to get the current representation.

```txt
      GET coap://[2001:db8:3::123]:5683/sensors/temp1
```

After this last query you would get back the current value of the temperature. Of course, this series of requests are done only the first time a client connects, when we know nothing about the network or the devices that are in it.

## Discovering Resources

Let's start with the second question first, how do you find the resources that a device has *before* asking it for them? Indeed, if we do not have any idea of what the device is supposed to do, it would be impossible to query for anything as we would not know the `path` part of the URL. To fix that problem, CoAP endpoint can come with a default URI that everyone knows, the ["well-known"](https://tools.ietf.org/html/rfc8428) URI.

When using [CoRE Link Format](https://tools.ietf.org/html/rfc6690), this URI is called `/.well-known/core`. That way, a CoAP client can send a `GET` request to a CoAP server for `/.well-known/core` and get in return a list of hypermedia links to other resources hosted in that server. Moreover, it can also filter the output to limit the amount of responses with a query string. For example we could query a CoAP server for all resources of the type `temperature`.

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

We have explained how to discover the resources on a CoAP endpoint but we have not mentioned how endpoints can be found to begin with.

Networks are and will continue to be heterogeneous, some scenarios foresee the use of multicast, while others have a master/slave approach. Some scenarios will have NATs and firewalls while other - more ideal - will simply have globally addressable IPv6 addresses. Some devices will be asleep while others will be permanently connected.

In scenarios where direct discovery of resources is not possible due to sleeping nodes, disperse networks or inefficiency of multicast it is possible to store information about resources held on other servers on something called a [Resource Directory (RD)](https://tools.ietf.org/html/draft-ietf-core-resource-directory-20).

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

RD has two interfaces, one for **registration** and another for **lookup**. To start using either fo them we first we need to find the RD. There are several options:

- Already knowing the IP address. Which means that devices need to be configured with that IP, this is the most common setup.
- Using a DNS name for the RD and use DNS to return the IP address of the RD. Which means that devices need to be configured with the domain name (e.g., `rd.jaime.win`).
- Multicast request all RDs in the same multicast group. More on that below.
- It could be configured using DNS Service Discovery ([DNS-SD](https://tools.ietf.org/html/rfc67630))
- It could be provided by default from the network using [IPv6 Neighbor Discovery](https://tools.ietf.org/html/rfc4861) by carrying information about the address of the RD, there is a Resource Directory Address Option ([RDAO](https://tools.ietf.org/html/draft-ietf-core-resource-directory-20#section-4.1.1)) for it.

After performing the discovery you should get the URI of the resource directory like `coap://rd.jaime.win`

### Registration

After discovering the RD a CoAP device can register its resources in it. A minimal registration will contain some endpoint identifier `ep`, the content format identifier which is `40` in this case (i.e., [`application/link-format`](https://www.iana.org/assignments/core-parameters/core-parameters.xhtml)) as well as a series of links resources that the endpoint wants to register.

```md
REQ: POST coap://rd.jaime.win/rd?ep=node1
     ct:40
     </sensors/temp>;ct=41;rt="temperature-c";if="sensor";
     </sensors/light>;ct=41;rt="light-lux";if="sensor"
```

The RD will return `2.01` (Created) response with the location path of the entry in the directory, in this case `/rd/4521`. That location is used in all subsequent operations on that registration.

```md
RES: 2.01 Created
     Location-Path: /rd/4521
```

There are several alternatives, like delegating registration to a commissioning tool, requesting the RD to fetch the links from the endpoint and others that are detailed in the RD specification.

### Lookup

To discover the resources registered with the RD an endpoint can use the lookup interface, which allows lookups for endpoints and resources using known CoRE Link attributes and two additional resource types (`rt`): `core.rd-lookup-res` for resources and `core.rd-lookup-ep` for endpoints.

You will have to ask the RD for its configuration to get which is the path where we can perform lookup, we could ask for all available interfaces by querying `/.well-known/core` with the query `rt=core.rd*`.

```txt
REQ: GET coap://rd.jaime.win/.well-known/core?rt=core.rd*

RES: 2.05 Content
     </rd>;rt="core.rd";ct=40,
     </rd-lookup/ep>;rt="core.rd-lookup-ep";ct=40,
     </rd-lookup/res>;rt="core.rd-lookup-res";ct=40,
```

However, in in this case we just want to find CoAP resources, therefore we query `rt=core.rd-lookup-res`. The RD returns the lookup interface for resources `/rd-lookup/res`.

```txt
REQ: GET coap://rd.jaime.win/.well-known/core?rt=core.rd-lookup-res
RES: </rd-lookup/res>;rt="core.rd-lookup-res";ct=40
```

Once we have the entry point to query the RD we could ask for all links to temperature resources in it with the query parameter `rt=temperature`.

```md
REQ: GET coap://rd.jaime.win/rd-lookup/res?rt=temperature
```

The RD will return a list of links that host that type of resource.

```md
RES: 2.05 Content
     <coap://[2001:db8:3::123]:61616/temp>;rt="temperature";
       anchor="coap://[2001:db8:3::123]:61616"
     <coap://[2001:db8:3::124]/temphome1>;rt="temperature";
       anchor="coap://[2001:db8:3::124]",
     <coap://[2001:db8:3::124]/temphome2>;rt="temperature";
       anchor="coap://[2001:db8:3::124]",
     <coap://[2001:db8:3::124]/temphome3>;rt="temperature";
       anchor="coap://[2001:db8:3::124]"
```

You can perform Observe on the Resource Directory in order to see when new links are registered, a very useful feature.

### Use of Multicast in CoAP

There is an added benefit of using UDP: A CoAP client can use UDP multicast to broadcast a message to every machine on the local network.

In some home automation cases, all devices will be under the same subnet, your thermostat, refrigerator, television, light switches, and other home appliances have cheap embedded processors that communicate over a local low-power network. This lets your appliances coordinate their behavior without direct input from you. When you turn the oven on, the climate control system can notice this event and turn down the heat in the kitchen. You can pull out your mobile phone, get a list of all the lights in your current room, and dim the lights through your phone, without having to go over to the light switch.

CoRE has registered one [IPv4](https://www.iana.org/assignments/multicast-addresses/multicast-addresses.xhtml) and one [IPv6](https://www.iana.org/assignments/ipv6-multicast-addresses) address each for the purpose of CoAP multicast. All CoAP Nodes can be addressed at `224.0.1.187` and at `FF0X::FD`. Nevertheless, multicast must be used with care as it is easy to create complex network problems involving broadcasting. You could do a discovery for all CoAP endpoints with:

```txt
GET coap://[FF0X::FD]/.well-known/core
```

In a network that supports multicast well, you can discover the RD using a multicast query for `/.well-known/core` and the query parameter `?rt=core.rd*`. IANA has not yet decided on the multicast address to be reserved but we can assume that all CoRE RDs can be found at the IPv4 `224.0.1.187` and an additional IPv6 multicast group address `FF0X::FE` will be created in order to limit the burden on other CoAP Endpoints. The request would then be:

```txt
GET coap://[FF0X::FE]/.well-known/core?rt=core.rd*
```

Notice that in the first example every CoAP endpoint will reply and in the second only those supporting RD.