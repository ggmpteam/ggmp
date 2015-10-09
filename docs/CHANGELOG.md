# CHANGELOG

## Future
* **[PROTOCOL:MESSAGES]** Do **ActionExt** or **ActionShort** have a place anymore?
* **[PROTOCOL:COMMUNICATION]** Suggested "burst fire" mechanism of sending messages.

## v0.3.0
* **[PROTOCOL:COMMUNICATION, LIBRARIES:COMMUNICATION]** Modified Ack requirements, mandating certain behaviors from language libraries.  

## v0.2.0
* **[PROTOCOL:MESSAGES]** Made requiring acknowledgment the default behavior for all message types. 
* **[PROTOCOL:MESSAGES]** Made Message ID use mandatory
* **[PROTOCOL:MESSAGES]** Added new **ActionData** message type, which acts as a combination of **Action** and **DataEnd**
* **[PROTOCOL:MESSAGES]** Reversed byte ordering to a sensible arrangement. The Header should refer to the first byte sent
and received, and so forth.
* **[PROTOCOL:MESSAGES]** Extended the size of all non-head components to 4 bytes for ease of message parsing, except
**ActionShort** messages.

## v0.1.0

* **[PROTOCOL:GENERAL]** Initial versioning, following Semantic Versioning practices.
* **[DOC]** Added changelog.