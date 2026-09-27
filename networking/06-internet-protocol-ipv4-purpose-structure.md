# The Internet Protocol: Purpose and Structure of an IPv4 Address

## Goal
Understand why every device needs an IPv4 address, and how the hierarchical (network + host) structure of an IPv4 address actually works in practice.

## Problem
Needed to understand:
- What role an IPv4 address actually plays for a host, both locally and on the wider internet.
- What "hierarchical addressing" means concretely, using a real address + subnet mask example.
- Why routers don't need to track every individual host to move traffic around.

## Fix

**Purpose of the IPv4 address**
An IPv4 address is a logical network address that identifies a particular host. It must be:
- Properly configured and **unique within the LAN**, for local communication.
- Properly configured and **unique in the world**, for remote communication.

The address is assigned to a host's network interface (usually a NIC). Every packet sent across the internet carries both a source and destination IPv4 address — this information is what lets networking devices deliver the packet to its destination and route any reply back to the original source.

**The IPv4 address structure — hierarchical addressing**
An IPv4 address is 32 bits, and it's split into two parts: the **network** portion and the **host** portion.

Worked example: host `192.168.5.11`, subnet mask `255.255.255.0`.
- First three octets (`192.168.5`) → the network portion.
- Last octet (`11`) → the host portion, identifying this specific device on that network.

This is called **hierarchical addressing** because the network portion tells you *which network* a host lives on, and the host portion identifies the specific device *within* that network — similar in spirit to a postal address (region, then a specific building).

**Why this matters for routing**
Because addressing is hierarchical, routers only need to know how to reach entire *networks*, not track the location of every individual host. This is what makes IPv4 routing scalable. It also means multiple logical networks can coexist on the same physical network, as long as each has a different network portion in its addressing.

## What I Learned
- This directly explains *why* subnetting works the way it does (covered in the Module 9 write-up): the network/host split described here is exactly the boundary that a subnet mask marks, and subnetting is just moving that boundary to create smaller networks.
- "Routers only need to know how to reach networks, not individual hosts" is the practical, real-world reason hierarchical addressing exists — it's what keeps routing tables manageable at internet scale.
- This module is the conceptual foundation Module 9's subnetting math builds directly on top of.
