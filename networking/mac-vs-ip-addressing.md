# MAC vs IP Addressing: How Frames Move Between Networks

Notes from Cisco Networking Academy (Networking Basics), sections 13.1.1 and 13.1.2, plus the Packet Tracer lab *Identify MAC and IP Addresses*.

## The core idea

A device on an Ethernet LAN uses two addresses at the same time, and each has a different job.

| Address | Layer | Job |
|---|---|---|
| **MAC address** | Layer 2 | Delivers the frame from one NIC to another NIC on the **same local network**. Only matters for one hop. |
| **IP address** | Layer 3 | Identifies the **final** source and destination of the packet, on the same network or a remote one. |

**One-line summary**

- IP = final destination (never changes)
- MAC = next stop (changes at every hop)

## 13.1.1 Destination on the same network

PC1 (`192.168.10.10`, MAC `aa-aa-aa`) sends to PC2 (`192.168.10.11`, MAC `55-55-55`). Both are in `192.168.10.0/24`, so there is no router involved.

| Dest MAC | Source MAC | Source IPv4 | Dest IPv4 |
|---|---|---|---|
| 55-55-55 | aa-aa-aa | 192.168.10.10 | 192.168.10.11 |

The next hop **is** the final destination, so the destination MAC is PC2's own MAC.

**The problem:** PC1 knows PC2's IP but not its MAC. This is the address resolution problem, solved by **ARP** for IPv4 (and ICMPv6 Neighbor Discovery for IPv6).

## 13.1.2 Destination on a remote network

PC1 (`192.168.10.10`) sends to PC2 (`10.1.1.10`) through routers R1 and R2.

PC1 compares the destination IP to its own network, sees they don't match, and sends the frame to its **default gateway** (R1).

Interface MACs in the diagram:

| Device | Left interface | Right interface |
|---|---|---|
| R1 | bb-bb-bb (G0/0/0) | cc-cc-cc (G0/0/1) |
| R2 | dd-dd-dd (G0/0/1) | ee-ee-ee (G0/0/0) |

Frame at each hop:

| Hop | Dest MAC | Source MAC | Source IPv4 | Dest IPv4 |
|---|---|---|---|---|
| PC1 → R1 | bb-bb-bb | aa-aa-aa | 192.168.10.10 | 10.1.1.10 |
| R1 → R2 | dd-dd-dd | cc-cc-cc | 192.168.10.10 | 10.1.1.10 |
| R2 → PC2 | 55-55-55 | ee-ee-ee | 192.168.10.10 | 10.1.1.10 |

### What a router does

1. Receives the frame on one interface.
2. **De-encapsulates** it (removes the old Layer 2 header).
3. Reads the destination IP and decides the next hop.
4. **Re-encapsulates** the same packet in a new frame for the outgoing interface.

## Common points of confusion

- **"Destination MAC" belongs to the frame, not to a device.** It is whichever device receives the frame on that hop. So bb-bb-bb is the destination MAC on the frame *arriving at* R1, and dd-dd-dd is the destination MAC on the frame R1 *sends out*.
- **Routers don't have one MAC.** Each interface has its own permanent MAC. What changes from hop to hop is which one appears in the frame.
- **The source MAC on each hop** is the interface the frame is leaving from, not the original sender.

## Packet Tracer lab: Identify MAC and IP Addresses

Topology: two laptops (`10.10.10.2`, `10.10.10.3`) behind Switch 1 and an access point, two PCs (`172.16.31.2`, `172.16.31.3`) behind Switch 2, and a router connecting both networks.

- **Part 1 (local communication):** ping between two hosts on the same network, then inspect the PDU in Simulation mode. The destination MAC is the other host's own MAC.
- **Part 2 (remote communication):** send traffic across the router and inspect the frame on each side. The destination MAC changes at the router while the IPs stay the same.

How to inspect a frame: switch to **Simulation** mode, send the ping, step with **Capture/Forward**, click the PDU envelope, then open **Inbound/Outbound PDU Details**.

## Why this matters for security

Understanding that MACs are local and IPs are end-to-end is the foundation for:

- **ARP spoofing**, where an attacker on the same subnet lies about which MAC belongs to an IP.
- **Man-in-the-middle attacks on a LAN**, which work because frames are delivered by MAC, not by IP.
- Why an attacker on the same subnet has capabilities a remote attacker doesn't.

## Key takeaways

1. IP addresses stay the same from source to destination.
2. MAC addresses are rewritten at every router, because each link is its own local network.
3. When the next hop is the final device, the destination MAC is that device's own MAC.
4. Routers work by de-encapsulating the frame, reading the destination IP, and re-encapsulating in a new frame.

## Next up

ARP: how a device discovers the MAC address that goes with an IP address.
