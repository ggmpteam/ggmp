# Acknowledgement Behavior

The Ack message is perhaps the most important of the Message Types in GGMP. It is used in situations where a client or 
server must know that the other end has received their message; e.g., any situation in which the game state risks 
becoming desynchronized between the two parties. Consider the following example.

*Note: All discussions in this article assume ReqAck variants when discussing a **MessageType**. 

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

The Protocol does not explicitly define behavior beyond this point. However, the GGMP Libraries provided by the GGMP 
Contributors will offer some advanced functionality.

## Footnotes

1. There are many valid situations where it's also critical that all state-based movement messages get received as well.
2. \#TIMEOUT has yet to be defined. 

