# Technical Specifications

_The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL
      NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",  "MAY", and
      "OPTIONAL" in this document are to be interpreted as described in
      [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt)._

## Networking

GGMP MUST use UDP port 12358.

## Components

The following components MUST be decoded as **32-bit unsigned integers.** 

* Client ID (CL)
* Message ID (MID)
* Actor ID (AR)
* Action ID (AN)
* Action Condition 1 (AC1)
* Action Condition 2 (AC2)

The following components MUST be decoded as **8-bit unsigned integers.**
 
* Header (HEAD)
* Size (SIZ)

The following components MUST be provided by language libraries as an **iterable stream of bytes**. Decoding into such
 a format SHOULD use one of that language's built-in types. Decoding them is left to the implementation.

* Data (DAT)