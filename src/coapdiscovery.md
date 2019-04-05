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

In scenarios where direct discovery of resources is not possible due to sleeping nodes, disperse networks or inneficiency of multicast it is possible to store information about resources held on other servers on something called a [Resource Directory (RD)](https://tools.ietf.org/html/draft-ietf-core-resource-directory-20). In adddition to registration it is also possible to do lookups on resources.  

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

To start using the Resource Directory first we need to find it too. There are several options:

1. Multicast request as explained in the next section.
1. Already knowing the IP address. Which means that devices need to be configured with that IP.
1. Using a DNS name for the RD and use DNS to return the IP address of the RD. Which means that devices need to be configured with the domain name (e.g. `www.resource.directory.jaime.win`).
1. It could be configured using DNS Service Discovery ([DNS-SD](https://tools.ietf.org/html/rfc67630))
1. It could be provided by default from the network using [IPv6 Neighbor Discovery](https://tools.ietf.org/html/rfc4861) by carrying information about the address of the RD, there is a Resource Directory Address Option ([RDAO](https://tools.ietf.org/html/draft-ietf-core-resource-directory-20#section-4.1.1)) for it.




Finding a Resource Directory
multicast 224.0.1.187  FF0X::FE
As we saw CoAP allows for Resource Directory

Registration
Lookup

### Convenient Multicast

Although the original lack of TCP, which now [is supported](https://tools.ietf.org/html/rfc8323) too there is an added benefit of using UDP; a CoAP client can use UDP multicast to broadcast a message to every machine on the local network.

In some home automation cases, all devices will be under the same subnet, your thermostat, refrigerator, television, light switches, and other home appliances have cheap embedded processors that communicate over a local low-power network. This lets your appliances coordinate their behavior without direct input from you. When you turn the oven on, the climate control system can notice this event and turn down the heat in the kitchen. You can pull out your mobile phone, get a list of all the lights in your current room, and dim the lights through your phone, without having to go over to the light switch.

CoRE has registered two [IPv4](https://www.iana.org/assignments/multicast-addresses/multicast-addresses.xhtml) and [IPv6](https://www.iana.org/assignments/ipv6-multicast-addresses) addresses for the purpose of CoAP multicast. All CoAP Nodes can be addressed at `224.0.1.187` and at `FF0X::FD`. Nevertheless Multicast must be used with care as it is easy to create complex network problems involving broadcasting. You could do a discovery for all CoAP endpoints with:

```txt
GET coap://FF0X::FD/.well-known/core
```

In a network that supports multicast well, you can discover the RD using a multicast query for `/.well-known/core`. IANA has not yet decided on the multicast address to be reserved but we can assume that all CoRE RDs can be found at the IPv4 `224.0.1.187` and the IPv6 `FF0X::FE`. the request would then be:

```txt
GET coap://FF0X::FE/.well-known/core?rt=core.rd*
```

Notice that in the first example every CoAP endpoint will reply and in the second only those supporting RD.