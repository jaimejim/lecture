# RIOT OS

This part is largely a shameless copy from colleagues at the [RIOT](https://github.com/iothon/RIOT) community. The tutorial provides an overview of RIOT and an introduction to using CoAP with well-known cloud services.

**Table of contents**

- [Getting started with RIOT OS](#Getting-started-with-RIOT-OS)
  - [Get your tools](#Get-your-tools)
  - [First example: Hello World](#First-example-Hello-World)
  - [Sensors and actuators](#Sensors-and-Actuators)
  - [CoAP Introduction](#CoAP-Introduction)
  - [Resource Discovery and Web Semantics](#Resource-Discovery-and-Web-Semantics)
- [Getting started with Node-RED](#Getting-started-with-Node-RED)
  - [Installation](#Installation)
  - [First example](#First-example)
  - [CoAP request example](#CoAP-request-example)
- [Getting started with Maker AllThingsTalk cloud](#Getting-started-with-Maker-AllThingsTalk-cloud)

# Getting started with RIOT OS

[RIOT](http://riot-os.org) is a free, open source operating system developed by a grassroots community gathering companies, academia, and hobbyists, distributed all around the world.

RIOT supports most low-power IoT devices and microcontroller architectures (32-bit, 16-bit, 8-bit). RIOT aims to implement all relevant open standards supporting an Internet of Things that is connected, secure, durable & privacy-friendly.
## Get your tools
### Quick Setup using a Virtual Machine (recommended for this challenge)

* Install and set up [git](https://help.github.com/articles/set-up-git/)
* Install latest [VirtualBox](https://www.virtualbox.org/wiki/Downloads) & [VirtualBox Extension Pack](https://www.virtualbox.org/wiki/Downloads) for **your system**
* Install [Vagrant](https://www.vagrantup.com/downloads.html)
* **Only on Windows**, before cloning set 

```Shell
git config --global core.autocrlf input
```

* Clone the RIOT repository and navigate to the root directory

```Shell
git clone https://github.com/iothon/RIOT
cd RIOT
git checkout iothon_2019
```

* Run `vagrant up`. This may take some time as it downloads an ubuntu image.
* Run `vagrant ssh` afterwards to connect to the virtual machine. 

*(See the [Vagrant RIOT Setup](https://github.com/RIOT-OS/RIOT/blob/master/dist/tools/vagrant/README.md) for a more general explanation*)

### Regular Setup without using a VM (recommended for RIOT development)

* Install and set up [git](https://help.github.com/articles/set-up-git/)
* Install the build-essential packet (make, gcc etc.). This varies based on the operating system in use.
* Install [Native dependencies](https://github.com/RIOT-OS/RIOT/wiki/Family:-native#dependencies)
* Install [OpenOCD](https://github.com/RIOT-OS/RIOT/wiki/OpenOCD)
* Install [GCC Arm Embedded Toolchain](https://launchpad.net/gcc-arm-embedded)
* On OS X: install [Tuntap for OS X](http://tuntaposx.sourceforge.net/)

### Troubleshooting

#### If you get the following error after running `vagrant up`

> The guest machine entered an invalid state while waiting for it to boot. Valid states are 'starting, running'. The machine is in the 'poweroff' state.

Make sure you have the [Extension Pack](https://www.virtualbox.org/wiki/Downloads) installed.

#### If you cannot flash a connected board (`/dev/ttyXXXX` does not exist)

Make sure your user is a member of the usergroup `vboxusers`. On Linux you can add the current user with

`usermod -a -G vboxusers $USER`.

You can check the groups of your user with

`groups`.

Note that you need to log out once to reload a Linux user's group assignments

## First example: Hello World
After a successful installation of the tools, try to build and flash *hello world* for two different platforms.

* Navigate into the *hello-world* directory
```sh
cd examples/hello-world
```

### Run your first application as Linux process
* Compile and run on `native` platform:
```sh
make all term
```
* If everything went well, you should see the serial output of your RIOT program after compilation has finished:
```sh
RIOT native interrupts/signals initialized.
LED_RED_OFF
LED_GREEN_ON
RIOT native board initialized.
RIOT native hardware initialization complete.

main(): This is RIOT! (Version: xxx)
Hello World!
You are running RIOT on a(n) native board.
This board features a(n) native MCU.
```

### Run your first application on real hardware
* To compile an application for a specific board, we can make use of the `BOARD` environment variable. In case you are running on an Atmel board, type:
```sh
BOARD=samr21-xpro make all flash term
```
* If everything went well, you should see the serial output of your RIOT program after compilation and flashing has finished:
```sh
INFO # main(): This is RIOT! (Version: xxx)
INFO # Hello World!
INFO # You are running RIOT on a(n) samr21-xpro board.
INFO # This board features a(n) samd21 MCU.
```

## Sensors and Actuators
In order to interact with the physical world IoT nodes need sensors and/or actuators. This allows them to measure and modify physical magnitudes (e.g. determine pressure or change the orientation of an element).

### RIOT SAUL ([S]ensor [A]ctuator [U]ber [L]ayer)
The way to use sensors and actuators is generally not homogeneous, and depends on the vendor and the type of sensor. Most of times the way to interact with this hardware is through a driver. RIOT has many drivers for a wide range of sensors and actuators. But again, the API for the drivers depend on the person who implements it, the usage and the device.

To solve this issue RIOT has [**SAUL**](http://doc.riot-os.org/group__drivers__saul.html). SAUL is a generic interface in RIOT, its purpose is to enable unified interaction with sensors and actuators through a set of defined access functions and a common data structure (it gives a **common API** to interact with them).

SAUL also takes care that any driver needed gets initialized on startup.

#### SAUL Registry
The available devices present on the hardware that can be accessed through the SAUL interface can be discovered using the [SAUL Registry](http://doc.riot-os.org/group__sys__saul__reg.html). Each entry in the registry represents a device (either a sensor or an actuator), which has a type and a name.

#### Usage
To see the usage of SAUL and the SAUL registry, take a look at the test application in the RIOT source code (`tests/saul`). That application traverses the SAUL registry to discover the type of devices available. It will print the name and the type, and it will try to read its value (some actuators like LEDs can be read in order to get its current state. This behavior will depend on the implementation of the SAUL integration of the driver).

## CoAP Introduction
The Constrained Application Protocol (CoAP) is a specialized web transfer protocol for use with constrained nodes and constrained networks in the IoT. Like HTTP, CoAP is based on the REST model: Servers make resources available under a URL and clients access these resources using methods such as GET, PUT, POST and delete.

Obtaining a value from a sensor is not much different from obtaining a value from a Web API.

### Gcoap
The easiest way to use CoAP in a RIOT node is by using the '[gcoap](http://doc.riot-os.org/group__net__gcoap.html)' module. It provides a high-level API to perform requests and responses, as well as an easy way to register resources that the server will expose.

You can find a simple example on how to use gcoap in `examples/gcoap`, in the RIOT code.



## Resource Discovery and Web Semantics

In M2M (Machine to Machine) applications, such as building automation, there is a need for clients and servers to find and interact with each other without human intervention. The mechanism through which the resources hosted by a server are learned is called Resource Discovery.

In order to understand what a given resource is, what it can provide and how to interact with it, the client and the server should agree on some application semantics. That way, a temperature sensor can be exposed as a resource, which has a type `temp`, and also it provides a known way to interact with it, so it has an interface description `sense`. The client should be able to understand these terms, because they belong to a common ontology that is shared between the server and the client (it could be applica)


### Link Format
The discovery of resources hosted by constrained web servers, their attributes and other resource relations is called **CoRE Resource Discovery**. The main goal of this discovery mechanism is to provide URIs to the resources the server hosts, complemented by attributes about those resources.

In constrained nodes this **collection of links** is carried as a payload of one of its resources (the "/.well-known/core"), and the format it uses to express that collection is called **CoRE Link Format** (see [RFC 6690](https://tools.ietf.org/html/rfc6690)).

### Syntax
A single link is expressed like:
```
<target-URI>;paramName1="paramValue1";paramName2;...;paramNameN
```
Where:
- **target-URI**: Is the URI of the resource
- **paramName**: Is the name of the attribute. It can be one of the specified in the RFC or an extension.
- **paramValue**: Is the value of the attribute. Not all attributes have a value (e.g. ['obs'](https://tools.ietf.org/html/rfc7641#section-6))

The target is enclosed in `'<' '>'` characters and the attributes are separated by `';'`.

When more than one link is sent they are separated by `','`:
```
<target-URI1>;paramName1="paramValue1",<target-URI2>;paramName2
```

### Commonly used attributes
In the context of IoT constrained servers commonly expose sensors, actuators, configurations and state variables through resources. In order to provide with semantics to that resources, attributes are used.

#### rt (Resource Type)
It is an opaque string, so it is up to the client to understand the meaning of it. It represents the noun that describes the resource. For instance, a temperature sensor resource could have an application-specific semantic "outdoor-temperature". Multiple Resource Types can be included in this attribute, separated by a space.

#### if (Interface Description)
It is also an opaque string. One can think of this attribute as describing verbs that are usable on a resource. It is meant to describe the generic REST interface to interact with the resource (or group of resources).

With that in mind, if a server exposes multiple sensors all can present the same interface for reading and configuration, by having the attribute `if="sensor"`.

#### Example
In this example a server present two resources. Both have the same interface "sensor":
```
</sensors/temp>;rt="temperature-c";if="sensor",
</sensors/light>;rt="light-lux";if="sensor"
```

### Resource Directory

Components in a resource directory environment ([draft](https://tools.ietf.org/html/draft-ietf-core-resource-directory-20)):

1. Resource Directory (RD) stores information about resources of endpoints and implements the REST interfaces for registration and lookup of those information
2. Endpoint (EP) is a web server that registers resources to the RD using the registration interface
3. Client is a web entity that uses the lookup interface to get information about resources/endpoints registered at the RD. A client can also be an EP at the same time.

Summary of the architecture:
```
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
             | EP |----      |                 |
             +----+
```

#### Resource registration

The registration interface of an RD is a resource on the RD with the resource type (rt) of "core.rd". It can be discovered by sending a GET to ""/.well-known/core?rt=core.rd".

```
 Req: GET coap://rd.example.com/.well-known/core?rt=core.rd

   Res: 2.05 Content
   </rd>;rt="core.rd";ct=40
```
Endpoints can register its resources by sending a POST to this resource with at least a unique name (ep) for the endpoint as query parameter. For other optional parameters (d, lt, base, extra-attr) please refer [section 5](https://tools.ietf.org/html/draft-ietf-core-resource-directory-20#section-5) of the draft. The list of resources to be registered and its attributes is expressed in link format and put in the payload.

```
Req: POST coap://rd.example.com/rd?ep=node1&lt=600
   Content-Format: 40
   Payload:
   </sensors/temp>;ct=41;rt="temperature-c";if="sensor";
         anchor="coap://spurious.example.com:5683",
   </sensors/light>;ct=41;rt="light-lux";if="sensor"

   Res: 2.01 Created
   Location-Path: /rd/4521
```

A successful registration will return the registration resource in the Location-Path option which can later be used to update or delete the registration.

#### Updating a registration

An endpoint can refresh ("keepalive") its registration by sending a POST request with an empty payload to the registration resource returned by the initial registration operation.

The parameters included in the URI during initial registration (lt, base) can be updated by including it again as query parameter with the updated values. Parameters that are not being changed should not be included in an update.

#### Resource lookup

To do a lookup, we must first discover the lookup resources of the RD. The lookup resources have the resource type "core.rd-lookup-res" for resource lookups and "core.rd-lookup-ep" for EP lookups. The interface also accepts "\*" wildcard to match the "rt", so we can make a GET request to "/.well-known/core?rt=core.rd-lookup*" of the RD to find both the resource and endpoint lookup resources of the RD.

Requesters can use filters for the lookup by including it as query parameters. Other than the attributes defined in link format (rt, ct, base, etc.), RDs also accepts "page" and "count" to obtain the lookup results in specified increments using pagination.

Example of a resource lookup. Here the lookup resource is `/rd-lookup/res`:
```
 Req: GET /rd-lookup/res?rt=temperature

   Res: 2.05 Content
   <coap://[2001:db8:3::123]:61616/temp>;rt="temperature";
              anchor="coap://[2001:db8:3::123]:61616"
```

#### Registration and Lookup in RIOT

There is currently no Resource Directory implementation in RIOT however there are APIs for endpoint registration and resource lookup at a RD (see [doxygen](http://api.riot-os.org/group__net__cord.html)) and the [lookup client API](https://github.com/leandrolanzieri/RIOT/tree/iothon_2019/sys/include/net/cord/lc.h) for RIOT nodes. For example on how to use these APIs, please refer the documentation page and the examples applications ([cord_ep example](https://github.com/leandrolanzieri/RIOT/blob/iothon_2019/sys/shell/commands/sc_cord_ep.c), [cord_lc example](TODO))

Local testing using cord_ep, cord_lc and aiocoap is explained in this [guide](https://hackmd.io/s/SJv9Z9VoE)

---

# Getting started with Node-RED
Node-RED is a programming tool that provides a browser-base editor that makes it easy to wire together flows using a wide variety of nodes.

![](https://i.imgur.com/YQOeZIF.png)


### Installation
To run Node-RED locally you will need to install it. For that, you first need Node.js. You can download it from [here](https://nodejs.org/en/).

Check that node is already installed by checking its version, for that on a terminal run:
```
node -v
```

Now, to install Node-RED we will use node package manager npm (`sudo` is only required on Linux of OS X):
```
sudo npm install -g --unsafe-perm node-red
```

In order to send and receive CoAP messages we will need to install the CoAP support node for Node-RED. To do that, run:
```
npm install node-red-contrib-coap
```

If everything went fine, you should be able to start Node-RED by running:
```
node-red
```

Finally, to access the Node-RED editor open a browser tab at [http://localhost:1880](http://localhost:1880).

### First example
On the left side of the editor you can find the node palette, there you can see every node available ordered by category. To add a node tho the workspace just drag it.

Nodes can have an input and/or an output. The information that flows between nodes is called `message`, and it is an object with multiple fields. The default one used to pass information is `message.payload`.

#### Add an Inject node
Inject nodes allow you to inject messages into the flow, either by clicking the button or setting a time interval between injects (e.g. used to start periodic actions). Drag one of these nodes into the workspace.

#### Add a Debug node
This type of nodes cause any messages that arrive the node to be displayed in the 'Debug' side bar. By default it displays the payload of the message, but it can display the entire message object. You can change its behavior in the node configuration. Drag one of these nodes into the workspace.

#### Wire the nodes
To connect nodes together click and drag the output of one node to the input of the other. Multiple nodes can be connected to the same input or output. Do that between the output of the inject node and the input of the debug node.

#### Deploy the flow
Every time a change is introduced in the editor it has to be deployed to the server to take effect. For that, just click the 'Deploy' button.

Select the 'Debug' side bar on the right side, and click the button of the inject node. You should see a timestamp appear on the log.

#### Add a Function node
Function nodes allow you to pass each message through a JavaScript function. Add one of these nodes into the workspace, and place it between the inject and debug nodes (you may delete the wire by selecting it and pressing the delete key). Double-click the node to open up the edit dialog. Enter the following function, it turns the timestamp into a Date string:
```javascript
// Create a Date object from the payload
var date = new Date(msg.payload);
// Change the payload to be a formatted Date string
msg.payload = date.toString();
// Return the message so it can be sent on
return msg;
```

Click Ok and deploy the flow again.

#### Export and Import
Flows can be represented by json files. For example, the previous flow can be imported with:
```json
[{"id":"58ffae9d.a7005","type":"debug","name":"","active":true,"complete":false,"x":640,"y":200,"wires":[]},{"id":"17626462.e89d9c","type":"inject","name":"","topic":"","payload":"","repeat":"","once":false,"x":240,"y":200,"wires":[["2921667d.d6de9a"]]},{"id":"2921667d.d6de9a","type":"function","name":"Format timestamp","func":"// Create a Date object from the payload\nvar date = new Date(msg.payload);\n// Change the payload to be a formatted Date string\nmsg.payload = date.toString();\n// Return the message so it can be sent on\nreturn msg;","outputs":1,"x":440,"y":200,"wires":[["58ffae9d.a7005"]]}]
```

### CoAP request example
The 'CoAP Request' node can be used to perform a request to a CoAP server. Here you can see an example of a simple request:
![](https://i.imgur.com/WShfLVU.png)

To import the flow, use this json:
```json
[{"id":"57b0b5ae.a0874c","type":"tab","label":"Basic CoAP request","disabled":true,"info":""},{"id":"3f5eb024.b4417","type":"coap request","z":"57b0b5ae.a0874c","method":"GET","observe":false,"url":"","content-format":"text/plain","raw-buffer":false,"name":"CoAP Request","x":580,"y":220,"wires":[["bbf9ecda.8b58c"]]},{"id":"2abc4f25.94ea","type":"inject","z":"57b0b5ae.a0874c","name":"","topic":"Trigger","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":170,"y":220,"wires":[["f1bdfdb5.d8d63"]]},{"id":"f1bdfdb5.d8d63","type":"change","z":"57b0b5ae.a0874c","name":"Set URL","rules":[{"t":"set","p":"url","pt":"msg","to":"coap://[fe80::54f4:73ff:fe8e:58fe]%tapbr0/.well-known/core","tot":"str"}],"action":"","property":"","from":"","to":"","reg":false,"x":360,"y":220,"wires":[["3f5eb024.b4417","cd438587.ff5a58"]]},{"id":"bbf9ecda.8b58c","type":"debug","z":"57b0b5ae.a0874c","name":"Debug: print response","active":true,"tosidebar":true,"console":false,"tostatus":false,"complete":"payload","x":800,"y":220,"wires":[]},{"id":"cd438587.ff5a58","type":"debug","z":"57b0b5ae.a0874c","name":"Debug: print URL","active":true,"tosidebar":true,"console":false,"tostatus":false,"complete":"url","x":590,"y":260,"wires":[]}]
```

The 'Set URL' node sets the payload "url" field to the desired URL and the 'CoAP Request' node performs it.

Set the URL to the correct value by double clicking the 'Set URL' node and modifying the 'to' field. Deploy the flow, open the debug console and watch the response to your request.

---

# Getting started with Maker AllThingsTalk cloud
[Maker](https://docs.allthingstalk.com/cloud/general/maker/) is the web interface of the AllThingsTalk cloud. It provides APIs to send and receive data in multiple formats. We will be using the [UDP API](https://docs.allthingstalk.com/developers/api/udp-messaging/) and the [JSON format](https://docs.allthingstalk.com/developers/data-formats/).

1- Create an account: [https://maker.allthingstalk.com/signup](https://maker.allthingstalk.com/signup)

2- Add a New Ground: [https://maker.allthingstalk.com/environment](https://maker.allthingstalk.com/environment)

3- Enter your ground and click on 'Connect a Device'. Choose 'Your own device' and set a name for it.

4- Once your device is created you will find it in the 'Devices' section. Now create a new asset for it. Assets could be sensors and actuators. From the point of view of the application these assets can be spread among multiple physical nodes.
You will have to choose the kind of asset, its name and its profile.

5- To send state updates of a particular asset you need to send UDP messages to the API. The payload needs to have the following format:
```
<device ID>\n<device token>\n{"<asset name>": {"value": <new value>}}
```
Where:
- **device ID** and **device Token** can be obtained under 'Settings > Authentication'.
- **asset name**: is the name of the asset whose state you want to update.
- **new value**: is the update value.

**NOTE** that there is a newline character ('\n') between the device ID, the token and the message.

6- Check if the messages are arriving well formed in the 'DEBUG' tab.