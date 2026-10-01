# Day 32 (continued): Packet Tracer, The Client Interaction

Continuation of [Day 32: Socket Pairs, netstat, and Following a Packet in Packet Tracer](day-32-socket-pairs-netstat.md).

The first writeup listed finishing the HTTP half of this lab as a next step. That's now done: I watched the whole communication from the first DNS query to the delivered webpage.

**Course:** Cisco Networking Academy, Networking Basics
**Tool:** Cisco Packet Tracer, simulation mode
**Lab:** Packet Tracer - The Client Interaction

## Objective

Observe the client interaction between a PC and a server: how a web page is requested, how the name is resolved to an IP address, and how the page is delivered.

## The full communication

In the lab I requested `www.example.com` from the PC's simulated web browser, with the Event List Filters set to show only DNS and HTTP events. The exchange played out in this order:

| Step | What happens | Transport | Server port |
|------|--------------|-----------|-------------|
| 1 | PC sends a DNS query to resolve the URL to an IP address | UDP | 53 |
| 2 | Server sends the DNS reply with the IP address | UDP | 53 (source) |
| 3 | PC sends the HTTP request for the web page | TCP | 80 |
| 4 | Server sends the web page back in **two segments** | TCP | 80 (source) |
| 5 | PC **acknowledges** the web page | TCP | 80 |

The PC doesn't know the web server's IP address up front, so DNS always comes first. Only after the name is resolved can it send the HTTP request.

Steps 4 and 5 show TCP at work: the page didn't arrive in one piece, and the PC confirmed receipt. That acknowledgment is part of the reliable delivery that UDP doesn't provide.

## Seeing it in the PDU details

Opening the PDU at the PC shows each layer of the frame. For the inbound DNS reply:

- **Layer 2:** Ethernet II header with the two MAC addresses
- **Layer 3:** source and destination IP addresses of the server and the PC
- **Layer 4:** UDP, source port **53**, destination port **1025**
- **Layer 7:** DNS

Port 53 is the DNS server's well-known port. Port 1025 is the dynamic source port the PC chose when it sent the query, which is why the reply comes back to it.

## UDP vs TCP in the same lab

The two halves of the interaction use different transport protocols on purpose:

- **DNS uses UDP.** A lookup is a small request and a small reply, so the overhead of setting up a connection isn't worth it. If a reply is lost, the client can just ask again.
- **HTTP uses TCP.** A web page needs reliable, ordered delivery, so TCP breaks it into segments, tracks them with sequence numbers, and waits for the receiver to acknowledge them. In the lab, the page arrived in two segments and the PC acknowledged it.

The same pattern shows up in both: a fixed, well-known port on the server side and a dynamic port on the client side.

## Connecting it to netstat

Earlier today I ran `netstat -an` on my own machine and saw this pattern in real traffic: many connections from one IP, each with its own high source port, going to well-known ports like 443 and 80. Packet Tracer showed the same idea one step at a time, with each layer's header visible.

| | netstat | Packet Tracer |
|---|---------|---------------|
| What it shows | Live sockets on my real machine | One simulated request, step by step |
| Strength | Real-world scale and states | Layer-by-layer header detail |

## Key takeaways

1. A web request is really **two conversations**: a DNS lookup first, then the HTTP request.
2. The server side uses a **well-known port** (53 for DNS, 80 for HTTP), and the client side uses a **dynamic port**.
3. The **source port is the return address**, which is how replies reach the right application.
4. Protocol choice is a trade-off: UDP for quick, small exchanges, TCP when reliable delivery matters.

## Next

Module 16, Application Layer Services.
