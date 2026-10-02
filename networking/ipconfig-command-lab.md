# Using the `ipconfig` Command — Packet Tracer Lab

> A completed Cisco Networking Academy activity using `ipconfig` to inspect a PC's network configuration in Packet Tracer.

## Lab overview

I completed the **Use the ipconfig Command** activity in Cisco Packet Tracer. The activity focuses on using the PC command prompt to view the network settings assigned to a device.

## Command used

At the Packet Tracer PC command prompt, `ipconfig` is entered as one word, with no hyphen:

```text
C:\> ipconfig
```

The command displays the PC’s current IP configuration. Depending on the interface and activity state, the output can include:

| Field | What it tells you |
|---|---|
| IPv4 address | The device’s address on an IPv4 network |
| Subnet mask | Which addresses are local to the subnet and which require routing |
| Default gateway | The router used to reach destinations outside the local subnet |
| DNS information | The name-resolution configuration, when provided |
| IPv6 addresses | IPv6 addressing information, when configured |

## Why this command is useful

`ipconfig` is a quick first check when troubleshooting connectivity. It helps determine whether a PC has a usable address, whether its subnet mask is appropriate, and whether a default gateway is available for reaching other networks.

A self-assigned IPv4 address in the `169.254.x.x` range can indicate that the PC did not receive a DHCP lease. A missing or incorrect default gateway can prevent communication with devices on remote networks even when the PC can communicate locally.

## Key takeaways

- The command is **`ipconfig`**—one word, with no hyphen.
- The basic command is entered at the PC command prompt, for example `C:\> ipconfig`.
- Read the IP address, subnet mask, and default gateway together to understand the PC’s network configuration.
- Use options only when the activity asks for them; on Windows-style command prompts, options are commonly introduced with a forward slash, as in `ipconfig /all`.

## Result

**Completed:** I finished the Cisco Packet Tracer activity on using the `ipconfig` command.
