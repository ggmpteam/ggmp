# Acknowledgement Behavior

The Ack message is perhaps the most important of the Message Types in GGMP. It is used in situations where a client or 
server must know that the other end has received their message; e.g., any situation in which the game state risks 
becoming desynchronized between the two parties. Consider the following example.

*Note: All discussions in this article assume ReqAck variants when discussing a* **MessageType**. 

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






