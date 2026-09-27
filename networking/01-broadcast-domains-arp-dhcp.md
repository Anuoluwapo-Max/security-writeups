# Broadcast Domains, ARP, and DHCP

## Goal
Understand why routers don't forward broadcast traffic, and how ARP and DHCP rely on broadcasting to function within a local network.

## Problem
Given a network diagram split into two subnets (192.168.1.0/24 and 192.168.2.0/24) connected by a router, I needed to understand:
- Why a host broadcasting an ARP request ("Who has 192.168.1.10?") only reaches devices on its own subnet.
- Why a host with no IP address (relying on DHCP Discover) can't reach a DHCP server on a different subnet.
- What specifically stops a router from forwarding these broadcasts across.

## Fix
Traced through the mechanics of both protocols:

- **ARP (Address Resolution Protocol)**: a host that knows a target's IP but not its MAC address broadcasts a request to the entire local broadcast domain. Only the device matching that IP replies with its MAC address. This is how devices on the same LAN locate each other at Layer 2.
- **DHCP (Dynamic Host Configuration Protocol)**: a host with no IP address broadcasts a DHCP Discover message ("Any DHCP servers out there?") since it has no way to address a specific server yet. Every device on the local subnet receives this broadcast; only the DHCP server responds.
- **Routers do not forward broadcast traffic by default.** This is the core reason broadcast domains stay contained to a single subnet. A broadcast on 192.168.1.0/24 never reaches 192.168.2.0/24, and vice versa.
- Extended this to the general concept of **service discovery** on Ethernet LANs — devices can broadcast for "anyone offering a specific service" (DHCP, DNS, printing) rather than needing to know a specific device's identity in advance.

## What I Learned
- Switches forward broadcasts within a broadcast domain; routers block them between domains. This single distinction explains a recurring exam/quiz pattern: "which device will not forward an IPv4 broadcast by default?" → router.
- ARP and DHCP are two concrete, everyday examples of the same underlying mechanic: broadcast locally, let the relevant device respond.
- This is foundational for later offensive techniques — ARP spoofing/poisoning and rogue DHCP server attacks both rely on understanding legitimate ARP/DHCP behavior first.
