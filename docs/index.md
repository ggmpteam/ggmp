# Introduction to the GGMP

The Generic Gameserver Messaging Protocol, or GGMP, is a messaging protocol that sits atop UDP. The protocol is designed
from the ground up to send Messages between game servers and clients. Messages are designed to contain easily decodable
information about updates to game state.

Goals for the proposed protocol are:

* Lightweight: Exactly as much data as needed and no more.
* Self-contained: Transmit as much information about an Action in one Message as possible
* Efficient: Small packet size, in pure bytes for ease of server and client-side processing.
* Resilient: A lost or malformed packet will be handled by the server, and is not breaking.
* Flexible: Usable for anything from a simple command-line game to a full-blown RTS.

Non-goals are:

* Data integrity: Following a common design pattern among games, dropped packets are not game breaking.

