# Client Features

While most of the GGMP Specification dictates the protocol, this small document outlines some expected features of the
GGMP language libraries.

### DDOS Dropping
All incoming messages will be checked upon being read from the socket. If the message is not from a trusted client, it 
receives no further processing.

### Cheat Prevention 
A message will not be processed if its client ID does not match the origin IP address it expects. Each Client can be
assigned a "whitelist" of Action IDs it may execute. 

### Action Callbacks
Functions can be assigned to a class (MessageActionHandler) which can call methods automatically depending on which 
Action is found. It will include the data already decoded.

