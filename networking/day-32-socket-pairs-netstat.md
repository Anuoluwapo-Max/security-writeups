# Day 32: Socket Pairs, netstat, and Following a Packet in Packet Tracer

**Course:** Cisco Networking Academy, Networking Basics
**Module:** 15, TCP and UDP (15.2 Port Numbers, 15.3 Summary and Quiz)
**Tools:** Windows Command Prompt, Cisco Packet Tracer

## Goal

Understand how the transport layer uses port numbers to deliver data to the right application, then check it on my own machine instead of only reading about it.

## Concepts

**Socket:** an IP address plus a port number, written `IP:port`.

**Socket pair:** the client socket plus the server socket. It uniquely identifies one connection.

```
192.168.1.5:1099  <->  192.168.1.7:80
```

The lesson example had one client talking to one server over two connections at once (FTP and web). Both had the same IP addresses, so the IPs alone couldn't tell the traffic apart. The ports did:

| Connection | Client socket | Server socket |
|------------|---------------|---------------|
| FTP | 192.168.1.5:1305 | 192.168.1.7:21 |
| Web | 192.168.1.5:1099 | 192.168.1.7:80 |

- The client picks a **dynamic source port** for each session.
- The server listens on a **well-known destination port**.
- The source port works as a return address, so the transport layer knows which application should receive the reply.

**Short version:** the IP address gets data to the right computer, and the port number gets it to the right application.

## Part 1: netstat on my own machine

With a browser open, I ran:

```
netstat -an
netstat -ano
```

Flags: `-a` shows all connections and listening ports, `-n` shows numeric addresses and ports, `-o` adds the owning process ID (PID).

### What I saw

- Dozens of `ESTABLISHED` TCP connections from my machine to remote servers, mostly on port **443** (HTTPS), each from a different high source port (49xxx to 65xxx).
- A few connections to port **80** (plain HTTP), mostly short-lived and in `TIME_WAIT`.
- Some non-standard ports (for example 5228, used by Google services), a reminder that not everything uses 80 or 443.
- `LISTENING` entries for Windows services (135 RPC, 445 SMB, 139 NetBIOS) and for virtual adapter services.
- UDP entries with no state and `*:*` as the foreign address, including many on 5353 (mDNS) and 1900 (SSDP).

### Connection states

| State | Meaning |
|-------|---------|
| `ESTABLISHED` | Active connection, data can flow |
| `LISTENING` | Waiting for incoming connections on that port |
| `TIME_WAIT` | Connection just closed, OS holds the entry briefly to catch stray packets |
| `CLOSE_WAIT` | Remote side closed, but the local app hasn't closed its end yet |

### Local address matters

- `0.0.0.0:port` means listening on **all interfaces**, so other devices on the network can reach it.
- `127.0.0.1:port` is **loopback**, reachable only from the machine itself.

### Mapping ports to programs

The PID column ties each socket to a running process. To look one up:

```
tasklist | find "<PID>"
```

Or use Task Manager, Details tab, sorted by PID.

## Part 2: Packet Tracer, "The Client Interaction"

I followed a web request from DNS lookup to webpage in simulation mode and opened the PDU details at each device.

| Step | Transport | Ports |
|------|-----------|-------|
| DNS lookup | UDP | destination 53 |
| Web request | TCP | destination 80 |

In the inbound DNS reply, the PDU showed:

- Layer 2: Ethernet II header with the MAC addresses
- Layer 3: source and destination IP addresses
- Layer 4: UDP, **source port 53, destination port 1025**
- Layer 7: DNS

The reply goes back to port 1025, the dynamic source port the PC used when it sent the request. That is how the PC knows which application is waiting for the answer.

DNS uses UDP because lookups are small, quick queries where setting up a connection would be wasted overhead. The web request uses TCP because a page needs reliable, ordered delivery.

## Security takeaway

`LISTENING` ports are a machine's **attack surface**. This is what a port scanner like Nmap probes on a target. Running `netstat` on my own machine showed what that looks like from the inside, and why it's good practice to be able to name the program behind every listening port.

## Quiz note

One question asked which transport layer header information identifies a target application. The answer is the **port number**, not the IP address (Layer 3, identifies the host), the sequence number (TCP ordering), or the MAC address (Layer 2).

## What I'd do next

- Match more `LISTENING` PIDs to programs with `tasklist` and decide whether each one is expected.
- Finish the HTTP half of the Packet Tracer lab and compare the UDP 53 and TCP 80 PDUs side by side.
- Move on to Module 16, Application Layer Services.
