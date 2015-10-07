# Acknowledgement Behavior

The Ack message is perhaps the most important of the Message Types in GGMP. It is used in situations where a client or 
server must know that the other end has received their message; e.g., any situation in which the game state risks 
becoming desynchronized between the two parties. Consider the following example.

_Note: All discussions in this article assume ReqAck variants when discussing a **MessageType**_. 

```
HEAD 0x05   Action Extended Req Ack
...
AR   0x0001 Player 1 Entity Model
AN   0x003F Move Along Vector
AC1  0x0000 None
...

HEAD 0x13   Data End Req Ack
...
DAT  0xF3.. <Vector Definition>

EAD 0x05   Action Extended Req Ack
...
AR   0x0001 Player 1 Entity Model
AN   0x003F Move Along Vector
AC1  0x0000 None
...

HEAD 0x13   Data End Req Ack
...
DAT  0xF4.. <New Vector Definition>
```

Both message sets require acknowledgment, but to what degree? If the initial **ActionExt** message is never acknowledged, 
does it make sense to send its **DataEnd** message? Additionally, what happens if one message pair gets received before
the other? 

Now consider this example

```
HEAD 0x01   Action Req Ack
...
AR   0x0001 Player 1 Entity Model
AN   0x0030 Move to Position
AC1  0xFD07 <Position Definition>
AC2  0xE9C0 <Position Definition>
...

HEAD 0x01   Action Req Ack
...
AR   0x0001 Player 1 Entity Model
AN   0x0030 Move to Position
AC1  0xFD1F <New Position Definition>
AC2  0xEA06 <New Position Definition>
...

```

In this case, once again, both messages require acknowledgement. However, in this case, what happens if only the first
message is dropped? Only the second?

All of these questions can be handled with knowledge of the implementation, but GGMP is intended to be implementation 
agnostic. Even though the first example is communicating events, there are many valid and appealing reasons to
choose the second example, which communicates state. 
 
In the first example, it is critical that *both* sets of messages get received, and ideally, in order. Even though 
vector addition is commutative, one set of movements may trace the player model through a nonsensical path. In the
second example, it might be critical that only the most recent movement get acknowledged<sup>[1](ack-behavior.md#footnotes)</sup>.

GGMP defines only one rule for acknowledgment of messages.

## Mandatory Ack Behavior

Senders implementing GGMP must respond to every **ReqAck** message with an **Ack** message. Receivers implementing GGMP
must resend unacknowledged messages if they have not received an **Ack** for that message within #TIMEOUT
<sup>[2](ack-behavior.md#footnotes)</sup> ms. If the message remains unacknowledged, this process should be repeated 2 
more times, each time incrementing #TIMEOUT by an additional #TIMEOUT ms. 

```
#TIMEOUT = 30ms
Sender                      Receiver
|                                  |
| --- MID 0x0021, ReqAck --------> |   
| ...30ms...                       |
| --- MID 0x0021, ReqAck --------> |
| ...60ms...                       |
| --- MID 0x0021, ReqAck --------> |
| ...90ms...                       |
| --- MID 0x0021, ReqAck --------> |
|
```

If any message sent is not properly acknowledged by this point, the message is considered **lost**. A lost message
must not be sent with the same Message ID, however it may be re-sent with a new Message ID. 
The Protocol does not explicitly define behavior beyond this point. However, the GGMP Libraries provided by the GGMP 
Contributors will offer some advanced functionality. Possible implementations include automatically re-sending messages, 
raising exceptions, or variable behavior depending on Message Type or Action ID.

## Ack Behavior in the GGMP Language Libraries

One of the strengths in the GGMP is the availability of client and server libraries for a variety of languages that will
make its implementation near effortless. In designing the protocol, we attempt to keep decisions for the protocol separate
from the decisions for the libraries. However, more often than not, one informs the other, and here we'll discuss the 
desired behavior for the official language libraries.

### Handling Lost Messages

Currently, a GGMP language library maintains 4 queues:
 
 * `inbox`, for Messages that have been received and are awaiting processing by the game itself
 * `outbox`, for Messages that have been built and will be sent on the next call of `sendAll()` (or similar)
 * `awaitbox`, for **ReqAck** Messages that have been sent but not Acked
 * `lostbox`, for lost Messages
  
We intend to offer programmers at least the following options regarding lost messages:

* Auto Resend: Automatically rebuild Messages from `lostbox` and resend them
* ManualProcessing: Require the programmer to read from `lostbox` in order to determine how to handle side effects of a dropped message

Additionally, we may implement a "burst fire" mode, where messages are sent in groups of 3 or more between the #TIMEOUT periods, 
 in order to increase chance of receipt.

## Footnotes

1. There are many valid situations where it's also critical that all state-based movement messages get received as well.
2. \#TIMEOUT has yet to be defined. 

