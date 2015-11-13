# GGMP Message Components

GGMP Messages are comprised of *Components*, which describe the Message format itself, as well as Client ID, Actors and
 Actions, and so on. Not every Component is included in every Message; the Header dictates the *Message Type*, which 
further dictates the Components and structure of the Message.

## HEAD: Header

* Size: 1 byte
* Acceptable Values: 0x00 - 0xFF

The Header dictates the Message Type. The following Message Types are currently available in GGMP. For information on 
their structure, see [Message Types](message-types.md).

|Header Value|Type|Description|Size (bytes)|
|---|---|---|---|
|0x00|Action|Long-form Action with Conditions, Message IDs|24|
|0x01|ActionNoAck|Long-form Action as above, but don't require ACK|24|
|0x02|ActionShort|Lightweight Action with reduced value ranges|8|
|0x04|ActionExtended|Action with additional data contained in a following message|24|
|0x0E|Data|Data corresponding to a previous message|?|
|0x12|DataEnd|Final Data corresponding to a previous message|?|
|0x14|ActionData|Action with Data and add'l Data to follow|?|
|0x16|ActionDataEnd|Action with arbitrary Data|?|
|0xFE|Client Assign|Used by server to assign a Client ID to a connected Client|?|
|0xFF|Ack|Acknowledge a previous message which has requested it|8|

An important note: Any even-numbered Message Type in the range 0x00 to 0xEE can opt not to 
require an ACK by increasing the value of the Header by 0x01. In the table above, this property is explicitly written 
for the Action message type, however this property is implemented for all Message Types in the above range, and is 
simply implied in much of this documentation.

## CL: Client ID

* Size: 3 bytes 
* Acceptable Values: 0x000001 - 0xFFFFFF for Clients, 0x00 for Server

The Client ID is a value assigned to a Client by the Game Server. It is useful for tracking origin of a Message as it is
processed by the Game Server. Additionally, it provides a way, if desired, to delineate Actors based on Client. A game 
could choose to have all actors 'owned' by a particular client, providing a number of benefits, not the least of which 
is an additional layer of security against message spoofing.

The Client ID 0x00 is reserved for the Game Server itself. Further Client IDs should begin at 0x01 and increment by 0x01
for each new client assigned.
 
## MID: Message ID

* Size: 4 bytes
* Acceptable Values: 0x00000000 - 0xFFFFFFFFF

The Message ID is a Component that can assist with certain features in advanced GGMP implementations. For 
example, **Ack** and **Data** messages use Message IDs to refer to previous messages. 
Additionally, Message IDs can be used to estimate packet loss, ensure correct ordering of packets, etc.

Message IDs should be unique per client per message. That is to say: no one Client should send multiple Messages with 
the same ID. However, Messages from *different* clients can share IDs, and in fact this is expected.

Each client should begin counting their Messages at 0x00000000 and increment them by 0x01 with every message sent.

 
## AR: Actor
* Size: 4 bytes
* Acceptable Values: 0x00000000 - 0xFFFFFFFF

The Actor is any game entity which takes an Action. The assignment, interpretation, and implementation of these actors 
is left entirely to the programmer. Actors can correspond to individual players, units controlled by players, the game 
state itself, etc.

It is recommended, though not required, that Actor 0x00 be reserved for the keeper of the game state.

## AN: Action
* Size: 4 bytes
* Acceptable Values: 0x00000000 - 0xFFFFFFFF

An Action is a game event taken by an Actor. Like the definition of an Actor, Actions are left intentionally vague. They
can communicate game state (though this is discouraged) or game actions. 

## AC1, AC2: Action Condition 1 and 2
* Size: 4 bytes each
* Acceptable Values: 0x00000000 - 0xFFFFFFFF
* Optional

Action Conditions act as parameters for Actions. They are used to attach data an Action. Their use is optional. If an 
Action does not require the use of Conditions, the byte ranges for those conditions should be cleared to 0x00.

If the data associated with an Action would exceed 8 bytes, use a message type that uses the Data component.
 
## DAT : Data
* Size: User-defined
* Acceptable Values: User-defined

Data is used for the transfer of Data which is either too large for an **Action** Message, or which can be sent separately.
Message Types implementing Data include: **ArbData**, **Data**, **DataEnd**, etc. 


## SIZ: Data Size
* Size: 1 byte
* Acceptable Values: 0x00 - 0xFF

Data Size specifies the length, in bytes, of Action Data attached to a **Data** or **DataEnd** Message.

## TYP: Data Type
* Size: 1 byte
* Acceptable Values: 0x00 - 0x06 

Data Type is used in **ArbData** (and, in the future, **Data** and similar Messages) to indicate to the recipient how to decode
the related Data Component. Available types are:

|Value |Type |Detail|
|-|-|-|
|0x00 |UINT32 |Unsigned 32-bit Integer|
|0x01 |INT32  |Signed 32-bit Integer  |
|0x02 |UINT64 |Unsigned 64-bit Integer|
|0x03 |INT64  |Signed 64-bit Integer  |
|0x04 |DOUBLE |IEEE 754 Double-precision floating point |
|0x05 |ASCII  |ASCII String, NOT null-terminated |
|0x06 |UTF8   |UTF-8 String           |

At this time, custom types are not permitted. If an implementation calls for a custom type, those types should begin at 
0xFF, to allow for future expansion of this Component.

## NUM: Data Components Count
* Size: 1 byte
* Acceptable Values: 0x01 - 0x08

Data Components Count is used in **ArbData** to signify how many Data components are included in the message. This allows
for the recipient to more easily process an incoming **ArbData** message.


## PMSG: Parent Message
* Size: 4 bytes
* Acceptable Values: 0x00000000 - 0xFFFFFFFF

Parent Message is used by **Data**/**DataEnd** or **Ack** Messages to express the Message ID with which they are associated. A 
**Data**/**DataEnd** Message must additionally have a Message ID of its own.  
   
 