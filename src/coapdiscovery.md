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

Once the client knows that there are two sensors of the type `temperature`, it can decide to follow one of the presented links and query it, for example the first one `/sensors/temp1`. That way it can learn the current value of the resource as we learnt in the previous section.

```txt
REQ: GET coap://coap.me:5683/sensors/temp1
RES: 2.05 Content
   [
     {"n":"urn:dev:ow:10e2073a01080063","u":"Cel","v":23.1}
   ]
```

## Discovering CoAP Endpoints

As we saw CoAP allows for Resource Directory

