
# IoT Playground

[TOC]

## Purpose

The idea is to have a simple development environment for CoAP endpoints.

## 1. Set Up

Host machine: macbook pro
Container: Ubuntu 20

### 1.1 Install Multipass and run ubuntu

If you don’t have it already, [install Brew](https://brew.sh/). Then, to install Multipass simply execute:

```shell
brew install --cask multipass
```

Launch a container

```shell
multipass launch --name buntu --mem 8G -disk 30G --cpus 2 
```

Get a shell

```shell
multipass shell buntu
```

### 1.2 Update VM and install dependencies

Inside the container run
```shell
sudo apt-get update
sudo apt-get install net-tools python3-pip autoconf
```

Upgrade pip

```shell
pip3 install --upgrade pip
```

### 1.3 Set up visual studio over ssh

In the host machine, get its public key

```shell
$ cat .ssh/id_rsa.pub

ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAC ...
```

Copy that and add it inside the container

```shell
$ vim .ssh/authorized_keys
```

Back in the host machine edit the ssh configuration in `.ssh/config` by appending:

```
Host buntu
 HostName 192.168.64.5
 User ubuntu
```

Now on Visual Studio select `open a remote window` and  `Connect to Host` to select the ubuntu server.

![](http://notes.nomadiclab.com/uploads/upload_18de2b0758113bda16b6de8b5c8710f7.png)

You should be able to open any repo, for example aiocoap.


### 1.4 Install aiocoap

If you want to play with aiocoap’s internals or consider contributing to the project, the suggested way of operation is getting a Git checkout of the project:

```shell
$ git clone https://github.com/chrysn/aiocoap
$ cd aiocoap
```

You can then use the project from that location, or install it with

```shell
$ pip3 install --upgrade ".[all,docs]"
```

### 1.5 Install remote desktop (optional)

You may want to have a UX interface, which is set as follows.

In the ubuntu machine install an RDP server:
```
sudo apt-get install ubuntu-desktop xrdp -y
```

On your host run some remote desktop visualizer like microsot remote desktop by connecting to the IP of your ubuntu machine.

## 2. Experimenting with aiocoap

Start a coap server in the container ubuntu machine
```shell
$ ./server.py 
```
Do a get on a resource in that machine

```shell
$ ./clientGET.py
```

The shell should return:
```
Result: 2.05 Content
b'2022-07-25 15:17'
```
The client code can be very simple, for example, as follows:

```python
import logging
import asyncio

from aiocoap import *

logging.basicConfig(level=logging.INFO)

async def main():
    protocol = await Context.create_client_context()

    request = Message(code=GET, uri='coap://192.168.64.5:5683/time')

    try:
        response = await protocol.request(request).response
    except Exception as e:
        print('Failed to fetch resource:')
        print(e)
    else:
        print('Result: %s\n%r'%(response.code, response.payload))

if __name__ == "__main__":
    asyncio.get_event_loop().run_until_complete(main())
```
