# Message Types

GGMP provides a number of various *Message Types* for a variety of use cases related to game client and server
 programming. Many of these types are designed to be as flexible as possible. GGMP stands for *Generic* Gameserver 
 Messaging Protocol, after all. It's up to you, the game developer to determine what Actor 45 is, what Actions he can 
 take, and what to do with all that information. Is it critical that the server receive your data? Use an **Ack** variant
 of your Message and your client library will ensure it's received. Don't need support for over 4 billion actors? Use 
 **ActionShort** Messages for your game instead. 
 
## Req-Ack vs. No-Ack

Message Types ranging from 0x00 to 0xEF have both Req-Ack and No-Ack variants. For the sake of conciseness and brevity, only
their Req-Ack variants are listed here. However, since Req-Ack vs. No-Ack doesn't change the structure of a message, converting
between them is easy: Simply increment the Header by 0x01, and you have the No-Ack version of that message. For example, the 
Header value for **Action, Req-Ack** is 0x00. The corresponding Header for **Action, No-Ack** is 0x01. 
  
In this documentation, we will explicitly denote **No-Ack** when referring to such a variant. If no variant is specified, 
it can safely be inferred that the Message in question is **Req-Ack**.

# 0x00 - 0xEF: Game State Message Types

These Message Types make up the core of communication over GGMP. 



## 0x00 - Action
The bread-and-butter message of GGMP, a game Action with Actors and Conditions.

#### Bytes

|0  |1  |2  |3  |4  |5  |6  |7  |8  |9  |10 |11 |12 |13 |14 |15 |16 |17 |18 |19 |20 |21 |22 |23 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|HEAD|CL|CL |CL |MID|MID|MID|MID|AR |AR |AR |AR |AN |AN |AN |AN |AC1|AC1|AC1|AC1|AC2|AC2|AC2|AC2|

#### Components

* Head 
* Client ID 
* Message ID 
* Actor ID 
* Action ID 
* Action Condition 1 
* Action Condition 2 



## 0x02 - ActionShort
A micro-sized version of an Action. Ideal for demos, tutorials, and ultra-lightweight games.

#### Bytes

|0  |1  |2  |3  |4  |5  |6  |7  |
|---|---|---|---|---|---|---|---|
|HEAD|CL|MID|MID|AR |AN |AC1|AC2|

#### Components

* Head 
* Client ID 
* Message ID 
* Actor ID 
* Action ID 
* Action Condition 1 
* Action Condition 2 



## 0x04 - ActionExtended
An Action which will be followed by additional **Data** or **DataEnd** messages. Identical in structure to `0x00`.

If an ActionExtended Message requires only one additional Data Message, that Message should be `0x12` **DataEnd**. `0x12`
allows the transmission of data, but also indicates to the receiver that no more data attached to this message should be
expected. 

#### Bytes

|0  |1  |2  |3  |4  |5  |6  |7  |8  |9  |10 |11 |12 |13 |14 |15 |16 |17 |18 |19 |20 |21 |22 |23 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|HEAD|CL|CL |CL |MID|MID|MID|MID|AR |AR |AR |AR |AN |AN |AN |AN |AC1|AC1|AC1|AC1|AC2|AC2|AC2|AC2|

#### Components

* Head 
* Client ID 
* Message ID 
* Actor ID 
* Action ID 
* Action Condition 1 
* Action Condition 2  



## 0x0E - Data
Data attached to an **ActionExtended** Message. Data Messages should not be sent without a preceding ActionExtended 
Message. If the transfer of such 'orphaned' data is required, use `0x10` - **Raw Data**.

The Data message is *n + 11* bytes long, where *n* = the size of the data attached. Attached data is limited to 255 bytes.

#### Bytes

|0  |1  |2  |3  |4  |5  |6  |7  |8   |9   |10  |11  |12 |13 |...|n  |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|HEAD|CL|CL |CL |MID|MID|MID|MID|PMSG|PMSG|PMSG|PMSG|SIZ|DAT|DAT|DAT|

#### Components

* Head
* Client ID
* Message ID
* Parent Message
* Size
* Data



## 0x12 - DataEnd
Identical in structure to `0x0E` - **Data**. However, this Message Type indicates that this is the final Data Message
attached to its associated Parent Message. Therefore, every `0x04` **ActionExtended** Message should be followed by any 
number of `0x0E` **Data** Messages, and exactly one `0x12` **DataEnd** Message.

#### Bytes

|0  |1  |2  |3  |4  |5  |6  |7  |8   |9   |10  |11  |12 |13 |...|n  |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|HEAD|CL|CL |CL |MID|MID|MID|MID|PMSG|PMSG|PMSG|PMSG|SIZ|DAT|DAT|DAT|

#### Components

* Head
* Client ID
* Message ID
* Parent Message
* Size
* Data


## 0x14 - ActionData
An Action with a self-contained Data Message. 

This message acts as the combination of an **ActionExt** Message and a **Data** Message. It contains an initial stream of 
data, and more importantly, signifies that the receiver should expect additional **Data** and **DataEnd** messages.

#### Bytes

|0  |1  |2  |3  |4  |5  |6  |7  |8  |9  |10 |11 |12 |13 |14 |15 |16 |17 |18 |19 |20 |21 |22 |23 |24 |25 |...|n  |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|HEAD|CL|CL |CL |MID|MID|MID|MID|AR |AR |AR |AR |AN |AN |AN |AN |AC1|AC1|AC1|AC1|AC2|AC2|AC2|AC2|SIZ|DAT|DAT|DAT|

#### Components

* Head 
* Client ID 
* Message ID 
* Actor ID 
* Action ID 
* Action Condition 1 
* Action Condition 2  


## 0x16 - ActionDataEnd
An Action with a self-contained Data Message. 

This message acts as the combination of an **ActionExt** Message and a **DataEnd** Message. Unlike **ActionData**, this 
message is entirely self-contained, and does not imply additional attached **Data** or **DataEnd** messages.

#### Bytes

|0  |1  |2  |3  |4  |5  |6  |7  |8  |9  |10 |11 |12 |13 |14 |15 |16 |17 |18 |19 |20 |21 |22 |23 |24 |25 |...|n  |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|HEAD|CL|CL |CL |MID|MID|MID|MID|AR |AR |AR |AR |AN |AN |AN |AN |AC1|AC1|AC1|AC1|AC2|AC2|AC2|AC2|SIZ|DAT|DAT|DAT|

#### Components

* Head 
* Client ID 
* Message ID 
* Actor ID 
* Action ID 
* Action Condition 1 
* Action Condition 2  


# 0xF0 - 0xFF: Protocol State Message Types
 
 These Messages communicate information about the state of the client, server, and connection.
 
## 0xFF - Ack
 Acknowledges the receipt of any Message which requires it. PMSG is the Message ID of the Message requiring Ack. Ack 
 messages never require further Acks. See [Ack Behavior](ack-behavior) for details on GGMP's Ack behavior.
  
#### Bytes
|0  |1  |2  |3  |4  |5  |6  |7  |
|---|---|---|---|---|---|---|---|
|HEAD|CL|CL|CL|PMSG|PMSG|PMSG|PMSG|

#### Components
* Head
* Client ID
* Parent Message