# IPv4 Exhaustion, NAT, and IPv6 Transition Strategies

## Goal
Understand why IPv4 is running out of addresses, how NAT has delayed the crisis, and the three main strategies (dual stack, tunneling, translation) used to transition to IPv6.

## Problem
Needed to connect several related facts into one coherent picture: the RIR exhaustion timeline, why NAT isn't a permanent fix, and how networks running different IP versions can actually communicate with each other during the transition period.

## Fix
- **The core shortage**: IPv4's 32-bit address space caps out at ~4.3 billion addresses — not enough for the modern internet (population + multiple devices per person + IoT). Regional Internet Registries (RIRs) allocate blocks of these addresses; APNIC (2011), RIPE NCC (2012), LACNIC (2014), and ARIN (2015) each exhausted their free pools in turn. AFRINIC (Africa's registry) entered its own soft-landing exhaustion phases starting 2017, reaching Phase 2 in January 2020.
- **NAT (Network Address Translation)** let many private-addressed devices share one public IP, extending IPv4's usable life significantly. Downsides: added latency, breaks assumptions some applications make about direct addressing, and severely complicates peer-to-peer communication (e.g., why some video calls fall back to a relay server instead of connecting directly — the STUN/TURN pattern).
- **IPv6** solves the shortage permanently with a 128-bit address space (2^128 ≈ 340 undecillion addresses).
- Three coexistence/transition strategies:
  - **Dual stack**: a device runs both IPv4 and IPv6 stacks simultaneously, picking whichever protocol fits a given destination. No conversion needed; each endpoint is independently capable of both.
  - **Tunneling**: used when both real endpoints are IPv6, but the path between them crosses IPv4-only infrastructure. The IPv6 packet is encapsulated inside an IPv4 packet at a dual-stack boundary router, carried across the IPv4 segment, then de-encapsulated back to IPv6 on the other side.
  - **Translation (NAT64)**: used when one endpoint is genuinely IPv6-only and the other genuinely IPv4-only, with no shared protocol at all. A NAT64 router actively converts the packet between protocols in both directions, rather than just wrapping it.

## What I Learned
- Dual stack is the actual end goal of the transition; tunneling and translation are deliberate stopgaps for reaching that goal without breaking connectivity in the meantime.
- The distinguishing question between tunneling and translation: are both real endpoints already capable of the same protocol (tunneling), or is at least one endpoint genuinely stuck on a different protocol than the other (translation)?
- IPv4 exhaustion is a regionally staggered, real, already-largely-completed event — not a future hypothetical — and it directly affects the ISPs (e.g., MTN, Airtel, Glo, Spectranet) operating under AFRINIC's allocation.
