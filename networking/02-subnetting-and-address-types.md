# Subnetting and IPv4 Address Types

## Goal
Understand the mechanics of subnetting (why/how a large network is split into smaller broadcast domains), and correctly classify different categories of IPv4 addresses.

## Problem
Given a scenario of a 400-user LAN (172.16.0.0/16) split into two 200-user subnets (172.16.0.0/24 and 172.16.1.0/24), I needed to understand:
- Why large broadcast domains are a problem in the first place.
- The actual bit-level mechanics of how /16 becomes /24.
- How to distinguish private, public, loopback, link-local (APIPA), and multicast addresses — including several deliberately similar-looking distractor addresses (e.g., 192.167.10.10 vs 192.168.x.x; 172.32.5.2 vs 172.16–31.x.x).

## Fix
Worked through the underlying binary structure:

- An IPv4 address is 32 bits, written as 4 octets (8 bits each). The prefix length (e.g., /16, /24) marks the boundary between the network portion and host portion.
- **Subnetting** = borrowing host bits and reassigning them as network bits, shrinking each resulting network's size and broadcast domain. Going from /16 to /24 borrows the entire third octet, creating up to 256 possible subnets (only 2 used in this example).
- Each subnet reserves 2 addresses (network address = all host bits 0; broadcast address = all host bits 1), leaving 254 usable addresses per /24.
- Classified address types using their reserved ranges:
  - **Private**: 10.0.0.0/8, 172.16.0.0/12 (172.16–172.31), 192.168.0.0/16 — not routable on the public internet.
  - **Public**: any unicast address outside the private/reserved ranges — globally routable.
  - **Loopback**: 127.0.0.0/8 — a device referring to itself.
  - **Link-local (APIPA)**: 169.254.0.0/16 — self-assigned when DHCP fails; local-segment only, no gateway/internet access.
  - **Multicast**: 224.0.0.0–239.255.255.255 — one-to-many delivery to a subscribed group only.
  - **Experimental**: 240.0.0.0–255.255.255.254 — reserved, not used in normal deployments.

## What I Learned
- Subnetting doesn't create new address space — it reorganizes existing space into smaller, walled-off broadcast domains, each with its own broadcast address.
- The private ranges have easy-to-miss boundaries that show up deliberately in quiz distractors (e.g., 192.167.x.x looks private but isn't; 172.32.x.x is one number past the actual 172.16–31 private range).
- A `169.254.x.x` address showing up in `ipconfig` is a reliable diagnostic signal that DHCP failed — not a random misconfiguration.
- Public vs. private is a distinction *within* unicast addressing, not a fourth separate category alongside unicast/broadcast/multicast.
