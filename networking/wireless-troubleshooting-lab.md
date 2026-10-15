# Packet Tracer: Troubleshoot a Wireless Connection

**Course:** Cisco Networking Academy, Endpoint Security (Module 5.3, Secure WLANs)
**Tool:** Cisco Packet Tracer
**Challenge day:** 46

## Scenario

A library patron could not reach the internet from one of the laptops. Every computer was configured with DHCP except Laptop1, whose user reported that www.cisco.pka could not be reached. The task was to identify the fault, fix it, and verify the fix.

## Objectives

- Identify faults in a wireless network
- Correct misconfigured devices in a wireless network

## What I did

### 1. Found the faulty device
I tried to reach www.cisco.pka from each end device. Laptop1 was the only one that failed.

### 2. Checked why Laptop1 was failing
Opening Laptop1's wireless monitor showed a prompt: **WPA2-Personal needed for connection**, asking for a pre-shared key. Laptop1 was never connected to the wireless network, which meant it had no IP address and no access to the website.

### 3. Compared it with a working device
The router's settings were locked on the network diagram, so I checked Laptop2 (which worked). Its Link Information showed:

- SSID: Academy
- Security: WPA2-Personal

This told me the correct network and security mode, but it did not reveal the key.

### 4. Logged into the router
The lab said to log into the router from one of the connected PCs. I ran `ipconfig` on PC3 and read the default gateway (192.168.1.1), which is the wireless router's address. I entered that address in PC3's web browser, logged in with the router's credentials, and opened **Wireless > Wireless Security**. It showed:

- Security mode: WPA2 Personal
- Encryption: AES
- Passphrase: (shown on the page, case-sensitive)

### 5. Fixed Laptop1
On Laptop1, I selected the **Academy** network, chose WPA2-Personal, and entered the passphrase exactly as shown.

### 6. Verified
Laptop1 connected, received an IP address, and could reach www.cisco.pka. Both lab tasks were marked complete.

## Reflection questions

**What is the difference between ping and tracert?**
Ping checks whether a destination is reachable and how long the round trip takes. Tracert shows each hop along the path, so it shows where the connection fails. I prefer ping for a quick first check, and tracert when ping fails, to locate the problem.

**Why change the default username and password on a wireless router?**
Default credentials are publicly known, so attackers try them first. Anyone who gets in can change the router's settings, including its wireless security, and lock out the administrator.

## What I learned

- A failed ping only says something is wrong. It does not say what.
- Comparing a broken device with a working one is a fast way to find the difference.
- The default gateway on a connected device is the router's address, which is how I reached its admin page.
- WPA2-Personal uses one shared passphrase for everyone. This lab is a good contrast with WPA2-Enterprise, where each user logs in individually through a RADIUS server using 802.1X and EAP, and the access point only passes the login along.

## Notes

- Screenshots to add: Laptop1's key prompt, Laptop2's Link Information, the router's Wireless Security page (blur or crop the passphrase), and the completed lab at 100%.
