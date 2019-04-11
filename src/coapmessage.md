# A CoAP Message

As specified in [RFC7252](https://tools.ietf.org/html/rfc7252), CoAP messages are very compact and by default transported over UDP. There is TCP support too (e.g., for NATed environments), but UDP is the intended original transport. CoAP messages are encoded in a simple binary format with a very compact header of 4 bytes. The version field indicates the CoAP version; the message type can be [ `CON`, `NON`, `ACK` and `RST` ]. This is followed by a method or response code [ `0.00` (Empty) , `0.01` (GET) , `0.02` (POST) , `2.05` (Success) , `4.04` (Not Found) ] and a message ID, which is a 16 bit field to detect message duplication. After the header, there is a variable-length `token`, which can be between 0 and 8 bytes long and is used to correlate requests and responses. Then there several options that allow for extensibility and finally the payload with the actual data. The `11111111` (255 in binary) is a marker to indicate the end of the options and beginning of the payload.



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

As mentioned before, CoAP was intended for *UDP transmission*, which is unreliable. This means that CoAP request and response messages may arrive out of order, appear duplicated, or go missing without notice. For this reason, CoAP implements a lightweight reliability mechanism using four different message types, including "confirmable" and "non-confirmable" messages. If the messages is confirmable (`CON`), that means that either the request or the response require an acknowledgement (`ACK`).

To ensure retransmission in case of loss, the CoAP endpoint sending the CON message keeps track of a timeout for the message and a retransmission counter to keep track of how many times the message was sent.

The figure below shows how a simple CoAP Request would look like. The CoAP Client sends a confirmable request `GET /temperature` that requires an acknowledgment message from the server. The message ID is `0x7d36`, which will be returned in the acknowledgment. Without the message ID, a client that makes two confirmable requests and gets two acknowledgements won’t know which acknowledgement goes with which request. A single CoAP request also triggers one or more responses, and the same token is used in every response. In the figure, the first message gets lost due to the unreliable nature of UDP, so the client needs to retransmit the message after waiting for an acknowledgement until some timeout.

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

The `Content-Format` option in the response explains the format of the content. This CoAP option will be further explained in [CoAP Web Linking and Serialization](./coaplinks.md).

## Observing Resources

So far we have seen how CoAP can be used to access resources on a sensor running a tiny server. However, basic CoAP requires the client interested in the state of a resource to initiate a request to the server every time in order to get the latest representation. If a client wants to have the current representation over a period of time this model does not work. In HTTP, this has been solved using repeated polling or HTTP long polling [RFC6202](https://tools.ietf.org/html/rfc6202) but the added complexity makes it hard in the constrained space.

In CoAP, this has been solved by using the [Observe option](https://tools.ietf.org/html/rfc7641). As we saw, CoAP allows to define new options to extend the protocol. This one is added to the `GET` method to request the server to add/remove a client from a list of observers of a resource. The value of the Observe option is either `0` for *register* or `1` for *deregister*.

If the servers returns a successful response (2.xx) with the Observe option as well, then that means the server has added the client to the list of observers of the target resource and the client will be notified of changes to the resource. Links to resources that are observable may include the attribute `obs` to indicate that. This is an attribute of a link, which will be explained in [CoAP Web Linking and Serialization](./coaplinks.md).

Another important option is Max-Age, which indicates how long the data can be served from a cache in seconds. For example, a CoAP client could request for a temperature resource and use the Observe option with value `0` to get subsequent notifications. The CoAP server will send a notification every time the resource value changes and use the Max-Age option to indicate that the data will be *stale* after a certain time period. This is shown in the following example.

```txt
REQ: GET coap://coap.me:5683/sensors/temp1
     observe:0  | token: 0x4a

RES: 2.05 Content
     observe:9  | token: 0x4a | Max-Age: 15
     [{"u":"C","v":25.2}]

RES: 2.05 Content
     observe:16 | token: 0x4a | Max-Age: 15
     [{"u":"C","v":27.1}]
```

After these two notifications were sent, it might happen that third message is lost. Since the `Max-Age` of the data is 15 seconds, the Client will notice that the data is *stale*, at which point it might choose to register again or to wait for the next notification.

```txt
RES (LOST MESSAGE): 2.05 Content
     observe:25  | token: 0x4a | Max-Age: 15
     [{"u":"C","v":19.7}]

--- 15 seconds pass ---

REQ: GET coap://coap.me:5683/sensors/temp1
     observe:0  | token: 0xb2

RES: 2.05 Content
     observe:44 | token: 0xb2 | Max-Age: 15
     [{"u":"C","v":18.4}]
```

It can be important to have reliability for some resources therefore some requests can use confirmable (`CON`) messages. A server might also use a confirmable response every Nth message to make sure that the client is still interested in the information, but use non-confirmable responses in most cases. The example below uses `Max-Age = 15` and a first `timeout` of usually from 2 seconds (note that the timeout has an algorithm to calculate the right value). In this case there is a retransmission *before* the freshness of the data expires.

```txt
REQ: GET (T=CON) coap://coap.me:5683/sensors/temp1  
     observe:0  | token: 0x4a

RES: (LOST MESSAGE) 2.05 Content
     observe:25  | token: 0x4a | Max-Age: 15
     [{"u":"C","v":19.7}]

--- 2 seconds pass and retransmission timeout triggers ---

RES: 2.05 Content
     observe:25  | token: 0x4a | Max-Age: 15
     [{"u":"C","v":19.7}]
```