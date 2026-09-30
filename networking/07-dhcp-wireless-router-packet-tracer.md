# Configure DHCP on a Wireless Router (Packet Tracer Lab)

## Goal
Get hosts on a small network to receive their IP configuration automatically from a DHCP-enabled router, then verify connectivity between the router and the PCs.

## Problem
Three PCs (PC0, PC1, PC2) connect to a single DHCP-enabled router. Without DHCP, each PC would need a manually configured address, mask, and gateway. The lab needed to confirm that:
- Each PC actually receives a valid configuration from the router.
- All three PCs can reach the router (their default gateway) and each other.

## Fix
Set up the router as the DHCP server for the LAN and let the PCs obtain addresses automatically. [Fill in: the pool/subnet you configured, e.g. network 192.168.5.0, mask, gateway 192.168.5.1, and any reserved or excluded addresses.]

Verified from PC2's command prompt using `ping`:
- `ping 192.168.5.1` (the router / default gateway): 4 sent, 4 received, 0% loss.
- `ping 192.168.5.126` and `ping 192.168.5.127`: 4 sent, 4 received, 0% loss on both. [Fill in: confirm which PCs these addresses belong to, using `ipconfig` on PC0 and PC1.]

Details in the ping output:
- The router replied with **TTL=255** and the PCs with **TTL=128**. Different operating systems start packets with different default TTLs (Cisco devices 255, Windows 128, Linux/macOS commonly 64), so the TTL of a reply gives a rough hint about what kind of device answered.
- Each successful reply is an ICMP echo reply, the same mechanism covered in the ICMP notes.

[Fill in: paste the FastEthernet section of `ipconfig` from PC2 showing the DHCP-assigned IPv4 address, subnet mask, and default gateway. The `0.0.0.0` block visible at the top of the output is a separate adapter (likely Bluetooth), not the network connection.]

## What I Learned
- DHCP is the practical version of the DHCP Discover broadcast covered earlier: the PC broadcasts because it has no address yet, and the router answers with a full configuration (address, mask, gateway).
- Everything worked without any manual addressing on the PCs, which is the whole point of DHCP on a LAN.
- A quick ping to the gateway and to neighbouring hosts is a fast way to confirm both the DHCP result and same-subnet connectivity.
- If DHCP had failed, the PCs would have fallen back to a `169.254.x.x` link-local address, so seeing a proper address from the expected range is itself a sign DHCP worked.
- TTL values in ping replies can hint at the type of device responding, which is useful later for basic recon.
