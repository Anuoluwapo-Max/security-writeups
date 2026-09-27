# The Access Layer: Encapsulation, Ethernet Frames, and Switches

## Goal
Understand encapsulation as a networking concept, the structure/purpose of the Ethernet frame, and how the access layer (specifically Ethernet switches) actually moves frames between hosts.

## Problem
Needed to understand:
- What "encapsulation" actually means in the context of network messages.
- What information an Ethernet frame carries and why.
- How a switch decides where to send a frame, and how it builds that knowledge over time.

## Fix

**Encapsulation**
Encapsulation is the process of placing one message format inside another for delivery — the same idea as a letter placed inside an envelope. De-encapsulation is the reverse: the recipient removes the letter from the envelope. Every message sent over a network follows specific format rules so it can be delivered and processed correctly.

**The Ethernet frame**
Ethernet protocol standards define the frame format, size, timing, and encoding for messages on an Ethernet network. A frame specifies:
- Destination and source MAC addresses
- A preamble (for sequencing and timing)
- A start-of-frame delimiter
- Length/type of frame
- A frame check sequence (used to detect transmission errors)

**The access layer**
The access layer is the part of the network where hosts actually connect in — the first line of networking devices linking hosts to the wired Ethernet network. Each host connects to an access layer device via an Ethernet cable.

- Older **hubs** allowed only one message at a time; simultaneous messages caused collisions, and excessive retransmissions clogged the network. Hubs are now obsolete.
- Modern **Ethernet switches** (Layer 2 devices) solve this. When a host sends a frame, the switch reads the destination MAC address and checks it against its **MAC address table** — a list of active ports and the MAC addresses reachable through them.
- If the destination MAC is known, the switch builds a temporary connection (a **circuit**) directly between the source and destination ports, rather than broadcasting to everyone.
- Switches can send and receive over the same cable simultaneously, eliminating collisions entirely and improving performance over the old hub model.
- The MAC address table is built **dynamically**: the switch learns a MAC address (and which port it's connected to) by examining the source address of every frame that passes through it, updating the table each time a new source MAC is observed.

## What I Learned
- Encapsulation is a general pattern (wrap → deliver → unwrap) that shows up throughout networking — the same idea reappears later in IPv6 tunneling (an IPv6 packet encapsulated inside an IPv4 packet), just applied at a different layer.
- A switch's "intelligence" isn't pre-configured — it's built entirely through observation, by learning source MAC addresses as traffic naturally flows through it.
- This connects directly to the ARP write-up: ARP is how a host *finds* another host's MAC address in the first place; the switch's MAC address table is how that MAC address then gets used efficiently to deliver frames, instead of flooding every port like a hub would.
