# GGMP - Generic Gameserver Messaging Protocol

GGMP is a UDP-based, application layer messaging protocol for standard, efficient communication between game servers and
clients. It's designed from the ground up to be: 

* Lightweight: Exactly as much data as needed and no more.
* Self-contained: Transmit as much information about an Action in one Message as possible
* Efficient: Small packet size, in pure bytes for ease of server and client-side processing.
* Resilient: A lost or malformed packet will be handled by the server, and is not breaking.
* Flexible: Usable for anything from a simple command-line game to a full-blown RTS.

In addition to this document, which defines and specifies the protocol, the GGMP team also maintains and plans 
development of open-source libraries in various languages:

## Actively Developed Libraries
* Python 3

## Planned Libraries
* C++
* Rust
* C#
* Java

## Project Status

The GGMP team began work on the protocol in late August 2015. Development on both the protocol itself and language 
libraries is underway, but in **pre-alpha** phase.

## Version

GGMP is currently on version 0.2.0

GGMP and its official language libraries (those maintained by the GGMP team) use semantic versioning, with an additional
requirement:

* The major and minor versions of a language library must always match the highest version of the GGMP spec they 
implement. Patch numbers may vary.

## Contributing

With so much being designed at this time, the GGMP team is not currently accepting code. However, discussions and 
comments on the protocol are welcome, and in fact, highly encouraged. Please submit them via GitHub Issues to this 
repository.


