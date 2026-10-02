# Ping Command and DNS Troubleshooting — Packet Tracer Lab

> A completed Cisco Networking Academy lab using `ping` to identify and fix a DNS configuration error on PC2.

## Lab overview

In the **Use the ping Command** activity, I investigated why one PC could not open a website while other PCs on the same Packet Tracer network could. I used ping tests and `ipconfig /all` to separate basic IP connectivity from hostname resolution, then corrected PC2’s DNS server address.

## Topology and key addresses

| Item | Address / result |
|---|---|
| PC1 IPv4 address | `192.168.1.101` |
| PC2 IPv4 address | `192.168.1.102` |
| Subnet mask | `255.255.255.0` (`/24`) |
| Default gateway | `192.168.1.1` |
| Web server | `192.15.2.10` |
| Correct DNS server (PC1) | `192.15.2.5` |
| Incorrect DNS server initially on PC2 | `191.15.2.5` |

## Troubleshooting steps

### 1. Identified the affected PC

I tested access to `www.cisco.pka` from the PCs’ web browsers. PC1 could reach the site; PC2 could not, so I focused troubleshooting on PC2.

### 2. Tested the hostname

From PC2’s command prompt, I first tried the shorter name `cisco.pka`. Packet Tracer could not find that host. The activity’s exact website hostname is `www.cisco.pka`.

This showed why using the exact hostname from the activity matters: DNS lookup depends on the name being queried.

### 3. Tested the web server by IP address

I pinged the server directly from PC2:

```text
C:\> ping 192.15.2.10
```

PC2 received **4 replies out of 4** with **0% packet loss**. This confirmed that PC2 had an IP path to the web server. The problem was not simply that the server was unreachable.

### 4. Compared DNS configuration

I used `ipconfig /all` on PC2 and PC1 to inspect their DNS server settings. The addresses differed by one digit:

```text
PC2 before the fix: 191.15.2.5
PC1:                192.15.2.5
```

PC2 had the wrong DNS server address. A DNS server resolves a hostname such as `www.cisco.pka` to the server’s IP address, `192.15.2.10`. Because PC2 had the incorrect DNS address, it could reach the web server by IP but could not reliably reach it by name.

### 5. Corrected PC2 and verified the website

In **PC2 → Desktop → IP Configuration**, I changed the DNS server to:

```text
192.15.2.5
```

I left PC2’s IPv4 address, subnet mask, and default gateway unchanged. After correcting DNS, I opened the website successfully from PC2.

## Commands and checks used

```text
ipconfig /all
ping 192.15.2.10
```

`ipconfig /all` exposed the DNS server value for comparison. Pinging the server by IP confirmed network reachability independently of DNS name lookup.

## Key takeaways

- A successful ping to an IP address does not prove that DNS is configured correctly.
- If a hostname fails but the corresponding IP address responds, investigate name resolution and DNS settings.
- Compare the exact **DNS Servers** value in `ipconfig /all`; a one-digit typo can break hostname lookups.
- In this lab, the error was `191.15.2.5` instead of `192.15.2.5`.
- Keep unrelated settings—such as the PC’s IP address, subnet mask, and default gateway—unchanged when correcting a DNS-only issue.

## Result

**Completed:** I isolated PC2’s DNS configuration error, changed its DNS server to `192.15.2.5`, and successfully opened `www.cisco.pka`.
