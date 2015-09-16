# GGMP Message Components

GGMP Messages are comprised of *Components*, which describe the Message format itself, as well as Client ID, Actors and
 Actions, and so on. Not every Component is included in every Message; the Header dictates the *Message Type*, which 
further dictates the Components and structure of the Message.

## HEAD: Header

* Size: 1 byte
* Acceptable Values: 0x00 - 0xFF

The Header dictates the Message Type. The following Message Types are currently available in GGMP. For information on 
their structure, see [Message Types](message-types.md)

|Header Value|Type|Description|Size (bytes)|
|---|---|---|---|
|0x00|Action|Long-form Action with Conditions, Message IDs|20|
|0x01|ActionAck|Long-form Action as above, but require ACK|20|
|0x02|ActionShort|Lightweight Action with reduced value ranges|8|
|0x04|ActionExtended|Action with additional data contained in a following message|20|
|0x0E|Data|Data corresponding to a previous message|?|
|0x12|DataEnd|Final Data corresponding to a previous message|?|
|0xFE|Client Assign|Used by server to assign a Client ID to a connected Client|?|
|0xFF|Ack|Acknowledge a previous message which has requested it|?|

An important note: Any even-numbered Message Type in the range 0x00 to 0xEE can require an ACK by increasing the value 
of the Header by 0x01. In the table above, this property is explicitly written for the Action message type, however this
property is implemented for all Message Types in the above range, and is simply implied in much of this documentation.

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
* Optional

The Message ID is an optional Component that can assist with certain features in advanced GGMP implementations. For 
example, any game requiring the use of ACKs must implement Message IDs. Additionally, Message IDs can be used to estimate
packet loss, ensure correct ordering of packets, etc.

Message IDs should be unique per client per message. That is to say: no one Client should send multiple Messages with 
the same ID. However, Messages from *different* clients can share IDs, and in fact this is expected.

Each client should begin counting their Messages at 0x00000000 and increment them by 0x01 with every message sent.

If a game does not choose to implement Message IDs, the byte ranges designated for Message IDs should be cleared to 
 0x00000000.
 
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

If the data associated with an Action would exceed 8 bytes, use an **ActionExt** Message, which sends arbitrary data in
a following **Data** or **DataEnd** Message. 
 
## DAT : Action Data
* Size: User-defined
* Acceptable Values: User-defined

Action Data makes up the primary content of a **Data** Message. Its purpose is to communicate data which is too large to
fit in a standard **Action** Message. 

## SIZ: Data Size
* Size: 1 byte
* Acceptable Values: 0x00 - 0xFF

Data Size specifies the length, in bytes, of Action Data attached to a **Data** or **DataEnd** Message.

## PMSG: Parent Message
* Size: 4 bytes
* Acceptable Values: 0x00000000 - 0xFFFFFFFF

Parent Message is used by **Data**/**DataEnd** or **Ack** Messages to express the Message ID with which they are associated. A 
**Data**/**DataEnd** Message must additionally have a Message ID of its own.  
   
 