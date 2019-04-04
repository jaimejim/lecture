# A CoAP Message

As explained in [RFC7252](https://tools.ietf.org/html/rfc7252) CoAP messages are very compact and by default transported over UDP, there is TCP support too for NATed environments, but UDP was the intended original transport. CoAP messages are encoded in a simple binary format with a very compact header of 4 bytes. The `version` indicates the CoAP version, the message `type` can be (`CON`, `NON`, `ACK` and `RST`). This is followed by a variable-length `Token` value, which can be between 0 and 8 bytes long. Then it has the response or method `codes` (e.g. `0.01` (GET) , `0.02` (POST) , `2.05` (Success) , `4.04` (Not Found)). Then there is the Message ID, which is a 16 bit field to detect message duplication. There are then several Options that allow for extensibility and finally the payload with the actual data.

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

As mentioned before, CoAP was intended for *UDP transmission*, which is unreliable. This means that CoAP request and response messages may arrive out of order, appear duplicated, or go missing without notice. For this reason, CoAP implements a lightweight reliability mechanism using the `type`field, introducing both Confirmable and Non-Confirmable messages. If the messages is CON as in Confirmable, that means that either the request or the response require confirmation, that is an acknowledgemnt of receival or ACK.

To ensure retransmission in case of loss, the CoAP endpoint sending the CON message keeps track of a `timeout` for the message and a `retransmission counter` to keep track of how many times the message was sent.

Below it is shown how a simple CoAP Request would look like. The CoAP Client sends a request message `GET /temperature` that requires requires an acknowledgment message from the server as it uses the type `CON`. The `Message ID` is `0x7d36` which will be used in that acknowledgment message. Without the message ID, a client that makes two GET requests and gets two responses won’t know which response goes with which request. A single CoAP request may trigger several responses, and the same `token` is used in every response, not just the initial acknowledgment. Responses sent after the acknowledgment will have new message IDs, but they’ll be tied to the original request by the token.

Since the first message got lost the client waits for a time until the `timeout` is triggered and the message is sent again.

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

In the response you can see a `Content-Format` field explaining the format of the content. This is a CoAP option that will be explained in the next section.

## Convenient Multicast

Although the original lack of TCP, which now [is supported](https://tools.ietf.org/html/rfc8323) too there is an added benefit of using UDP; a CoAP client can use UDP multicast to broadcast a message to every machine on the local network.

In some home automation cases, all devices will be under the same subnet, your thermostat, refrigerator, television, light switches, and other home appliances have cheap embedded processors that communicate over a local low-power network. This lets your appliances coordinate their behavior without direct input from you. When you turn the oven on, the climate control system can notice this event and turn down the heat in the kitchen. You can pull out your mobile phone, get a list of all the lights in your current room, and dim the lights through your phone, without having to go over to the light switch.

Nevertheless Multicast must be used with care as it is easy to greate other network issues involving broadcast storms.

