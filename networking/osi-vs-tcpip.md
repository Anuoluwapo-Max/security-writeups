# OSI Model vs TCP/IP Model

## Context
Notes from Cisco Networking Academy — Networking Basics, Module 5 
(Communication Principles).

## The Core Difference
- **OSI Model**: A 7-layer *reference* model. Describes the functions 
  that must happen for network communication to work, without dictating 
  exactly how. Used mainly as a teaching and troubleshooting tool.
- **TCP/IP Model**: A 4-layer *protocol* model. This is what's actually 
  implemented — it's what the internet runs on.

## Layer Mapping

| OSI Layer | TCP/IP Layer |
|---|---|
| 7 - Application, 6 - Presentation, 5 - Session | Application |
| 4 - Transport | Transport |
| 3 - Network | Internet |
| 2 - Data Link, 1 - Physical | Network Access |

## Key Takeaways
- OSI Layer 3 (Network) and TCP/IP's Internet layer both handle 
  addressing and routing.
- OSI Layer 4 (Transport) and TCP/IP's Transport layer both handle 
  reliable, ordered delivery.
- OSI's top three layers all collapse into TCP/IP's single Application 
  layer, since in practice these functions are handled together by 
  end-user applications.
- Why it matters: OSI gives precise vocabulary for troubleshooting — 
  being able to say "the issue is at Layer 3" narrows down a problem 
  fast, even though TCP/IP is what's actually running.

## Result
Passed Module 5 (Communication Principles) quiz — 100%.
