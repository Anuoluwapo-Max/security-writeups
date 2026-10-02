# Telnet and SSH Remote Access Lab

> A Cisco Packet Tracer lab comparing an insecure Telnet connection with a secure SSH connection to a router.

## Lab overview

I used PC1 to verify network connectivity to the HQ router, then tested remote command-line access with Telnet and SSH. Telnet was rejected by the router, as expected for this activity; SSH provided the secure remote session.

| Device | Interface / address | Details |
|---|---|---|
| PC1 | FastEthernet0, DHCP | Received `192.168.1.11/24`; default gateway `192.168.1.1` |
| HQ router | G0/0/1, `64.100.1.1/16` | Remote device used in the activity |
| SSH account | `admin` | Course-provided password used; password omitted here |

The client and router addresses are on different subnets. PC1 reaches the router through its default gateway, which routes traffic onward. The activity uses a simulated network, so the addressing is part of the lab topology.

## What I did

### 1. Checked PC1’s IP configuration

At first, PC1 displayed a self-assigned `169.254.x.x` IPv4 address with no usable gateway. Later, DHCP assigned it an address in the `192.168.1.0/24` network and gateway `192.168.1.1`.

I checked the configuration from **PC1 → Desktop → Command Prompt** with:

```text
C:\> ipconfig
```

The DHCP-assigned address and gateway showed that PC1 was ready to communicate beyond its local subnet.

### 2. Verified connectivity to HQ

I pinged the HQ router interface:

```text
C:\> ping 64.100.1.1
```

The first request timed out, but the following three received replies. The ping summary showed **3 of 4 replies**. This confirmed that PC1 could reach the router, although the first packet was lost while the lab network was initializing or resolving the path.

### 3. Tested Telnet

I attempted to open a Telnet session to HQ:

```text
C:\> telnet 64.100.1.1
```

The connection opened and then closed with a message indicating it was closed by the remote host. That was expected: the router is configured not to allow insecure Telnet access.

### 4. Connected with SSH

I used SSH with the `admin` account:

```text
C:\> ssh -l admin 64.100.1.1
```

After entering the course-provided password, the router displayed the privileged EXEC prompt:

```text
HQ#
```

`HQ#` confirms that the SSH session reached the router’s privileged EXEC level. The activity asks for the prompt after successful SSH access; the answer is **`HQ#`**.

### 5. Viewed the router’s file listing

At `HQ#`, I used:

```text
HQ# dir
```

The router listed files in `flash:/`. I also accidentally tried `more 486899872`; that failed because `486899872` was the file size, not a filename. The listed `.bin` file is a router image, which does not need to be opened for this activity.

I exited the remote session with:

```text
HQ# exit
```

## Telnet vs. SSH

| Protocol | Purpose | Result in this activity |
|---|---|---|
| Telnet | Remote command-line access without encryption | Router closed the connection because insecure Telnet access is disabled |
| SSH | Encrypted remote command-line access | Login succeeded and reached the HQ router prompt |

SSH is preferred for real network administration because it encrypts the session, including login credentials and commands. Telnet does not provide that protection.

## Key takeaways

- A client and router do **not** have to be on the same subnet if routing and a default gateway provide a path between them.
- A `169.254.x.x` address is a self-assigned fallback; the later `192.168.1.11/24` address and `192.168.1.1` gateway showed that DHCP configuration had arrived.
- A failed Telnet login can be intentional when the router is configured to require SSH.
- `HQ>` is the router’s user EXEC prompt; `HQ#` is its privileged EXEC prompt. This SSH login landed directly at `HQ#`.
- The `dir` command listed flash files. The number `486899872` was a file size, not a file name.

## Result

**Completed:** I verified PC1’s addressing, pinged the HQ router, observed Telnet being rejected, signed in over SSH, reached the `HQ#` prompt, and exited the remote session.
