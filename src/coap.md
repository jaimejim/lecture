# The Constrained Application Protocol (CoAP)

In this one slider you have essentially the main RFCs that have been produced over the years in a somewhat chronological order.

Connectivity and routing
6LoWPAN completed its work on running IP on 802.15.4 networks. 
LPWAN looking into making the internet work on networks like sigfox and lora, with very low throughput. 
ROLL which focused on routing in such networks. 
6Lo was looking into the many radios are capable of doing IoT traffic (802.15.4, zwave, ble, nfc... and what not). LWIG giving implementation guidance for developers.

Application
CoRE, which created an REST application layer protocol for constrained devices, similar to HTTP, together with its extensions (i.e. resource discovery very important in IoT, application layer security, etc..) being a reference on many other SDOs.
CBOR for payload compression where JSON is too expensive, and I like to add
T2TRG there since they are very good at providing the longer term perspective I mentioned before.

Security
DICE which was task to improve DTLS.
COSE specified how to create and process signatures and encryption using CBOR for serialization.
ACE Now the focus is on making sure that the devices are accessed by the right parties with focus on authentication and authorization (ACE). TEEP and SUIT, SUIT basically defining the metadata needed for firmware upgrades, so that devices' bootloader can verify that the binary comes from the right source.

![CoAP 1](./img/coap1.png)

---

CoAP is the Constrained Application Protocol by the IETF for constrained devices and networks.  CoAP is the equivalent of HTTP but for constrained devices. From HTTP it takes multiple characteristics:
It is also client/server type, although endpoints will often be both, so it’d be more of a P2P scenario.
It also uses a smaller subset of methods to operate on resources.
Same key concepts from the web like media types, URLs, URN, etc.
Compact 4 byte header
It was intended to run over UDP, providing reliability at the application layer. This was  done with one of the message types (CON NON ACK RST), basically marking a message as something to be confirmed back.
Nowadays, due mainly to NATting issues in IoT, where you might not be able to have the device initiating the communication always, or the NAT mght be too strict, misconfigured, etc.. We also have TCP support. This was a requirement form OMA.
RD used to register and lookup for devices.

In this example we see a client getting a resource hosted at a coap server.

![CoAP 2](./img/coap3.png)

---

CoAP Core_Link RFC6690 expands the definitions on the web linking RFC and expands it for IoT. 
Adds query string parameters and attributes. These attributes describe information useful in accessing the target link of the relation and provide useful semantic information like in this example that the resource we are accessing is part of the oma organization and it is of type temperature. It also tells us that the content is formatted in plaintext as indicated by the value 0. we could request it in senml-json and then we would be using ct=110 
We also can observe parameters by using CoAP observe option, we can get notifications of state changes in the resources hosted by the coap server. We can even add query parameters like “less than” lt=0, if we want the value to be lower than 0 (-37 in this example).
RFC6690 defines the serialization of links in a formal way as well as defining specific M2M content types.
It also defines a well-known resource which is the well-known/core which is globally reserved. Also can be used with HTTP since it is also reserved there. 
This well-known resource can accept optional query string parameters. So we could use this new content types and resources types to find resources on the device based on attribute and relation. You can see that we are requesting an IPSO temperature resource, with content type 0 (plaintext) and that it has to be observable.
In order to find them and see the links, we can use GET  message with content type and the representation that we want (for example JSON)

This example is the same as before, but now we use OMA’s resource uri (more on that letter) which is the same as asking for the temperature value, we also use a resource type and a content type in which we want the information returned.

![CoAP 3](./img/coap3.png)
