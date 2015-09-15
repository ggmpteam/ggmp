# Message Types

GGMP provides a number of various *Message Types* for a variety of use cases related to game client and server
 programming. Many of these types are designed to be as flexible as possible. GGMP stands for *Generic* Gameserver 
 Messaging Protocol, after all. It's up to you, the game developer to determine what Actor 45 is, what Actions he can 
 take, and what to do with all that information. Is it critical that the server receive your data? Use an **Ack** variant
 of your Message and your client library will ensure it's received. Don't need support for over 4 billion actors? Use 
 **ActionShort** Messages for your game instead. 
 
## Req-Ack vs. No-Ack

Message Types ranging from 0x00 to 0xEF have both Req-Ack and No-Ack variants. For the sake of conciseness and brevity, only
their No-Ack variants are listed here. However, since Req-Ack vs. No-Ack doesn't change the structure of a message, converting
between them is easy: Simply increment the Header by 0x01, and you have a Req-Ack version of that message. For example, the 
Header value for **Action, No-Ack** is 0x00. The corresponding Header for **Action, Req-Ack** is 0x01. 
  
In this documentation, we will explicitly denote **Req-Ack** when referring to such a variant. If no variant is specified, 
it can safely be inferred that the Message in question is **No-Ack**.

# 0x00 - 0xEF: Game State Message Types

These Message Types make up the core of communication over GGMP. 

## 0x00 - Action
The bread-and-butter message of GGMP, a game Action with Actors and Conditions.

#### Bytes

|19 |18 |17 |16 |15 |14 |13 |12 |11 |10 |9  |8  |7  |6  |5  |4  |3  |2  |1  |0  |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|HEAD|CL|CL |CL |MID|MID|MID|AR |AR |AR |AN |AN |AC1|AC1|AC1|AC1|AC2|AC2|AC2|AC2|

#### Components

* Head 
* Client ID 
* Message ID 
* Actor ID 
* Action ID 
* Action Condition 1 
* Action Condition 2 


 
