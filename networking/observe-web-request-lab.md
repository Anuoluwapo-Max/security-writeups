# Observe Web Request — Packet Tracer Lab

> A completed Cisco Networking Academy lab (Networking Basics, Module 16): trace one web request from a client to a web server and watch the TCP and HTTP traffic in Simulation mode.

## Lab overview

I used **External Client** to request a web page from `ciscolearn.web.com`, compared the page in the browser with the HTML file on the server, then used Simulation mode to see the packets behind a single web request.

| Item | Lab value |
|---|---|
| Client | External Client, `192.168.1.10` |
| Web server | `ciscolearn.web.com`, `172.16.15.200` |
| Server port | `80` (HTTP) |
| Client source port | `1000` (set manually in the Complex PDU) |
| Simulation filters | TCP and HTTP only |

## What I did

### 1. Verified connectivity to the web server

From **External Client → Desktop → Command Prompt**, I used `ping` with the server's name:

```text
C:\> ping ciscolearn.web.com
```

Pinging by name needs DNS first: the PC asks the DNS server for the IP address, then sends the ping to that address.

### 2. Connected to the web server

From **Desktop → Web Browser**, I entered `ciscolearn.web.com`. The page loaded, so DNS had resolved the name and the server had answered the HTTP request on port 80.

### 3. Viewed the HTML code

On the server, under **Services → HTTP**, I opened `index.html` and compared it with the page in the browser. The server stores plain text with tags, and the browser turns the tags into the formatted page:

| In the file | What the browser shows |
|---|---|
| `<b>bolding</b>` | Bold text |
| `<i>italics</i>` | Italic text |
| `<u>underlining</u>` | Underlined text |
| `<font color='red'>` | Red text |
| `<H1>`, `<H2>`, `<H3>` | Headers of different sizes |

### 4. Observed the traffic in Simulation mode

I switched to **Simulation** mode and checked in **Edit Filters** that only **TCP** and **HTTP** were selected. Then I created a **Complex PDU** with these settings:

| Setting | Value |
|---|---|
| Source | External Client |
| Application | HTTP |
| Destination IP | `172.16.15.200` (filled in when I clicked the server) |
| Starting source port | `1000` |
| Destination port | `80` |
| Simulation setting | Periodic, interval `120` seconds |

After I clicked **Create PDU** and played the simulation, the PDU list showed a TCP PDU with status **Successful**. A **Buffer Full** message appeared and I closed it with **View Previous Events**.

## What I saw in the event list

Every event I looked at was labeled **TCP**. HTTP travels inside TCP segments, so the filtered list showed the TCP exchange carrying it. Each packet shows up as several rows because every device it passes through (switches and routers) is its own event.

I opened individual events and read the **PDU Information** window:

- **SYN+ACK from the server:** the text said the client received a TCP SYN+ACK on the connection to `172.16.15.200` on port 80, the TCP connection was successful, and the connection state was set to **ESTABLISHED**. This is step 2 of the three-way handshake.
- **FIN+ACK from the client:** the client closed the TCP connection to the server on port 80 and set its state to `FIN_WAIT_1`. This is connection teardown.
- In both, the **Layer 4** TCP header was filled in (`Src Port: 1000, Dst Port: 80` from the client) and **Layers 5 to 7 were empty**. These packets carried no web page content, only connection management.

## The TCP exchange behind one web request

```text
Client → Server   SYN         "I want to connect; my starting number is X"
Server → Client   SYN+ACK     "Got it; my starting number is Y"
Client → Server   ACK         "Got that too"        → connection ESTABLISHED
        ... HTTP request and response carried inside TCP ...
Client → Server   FIN+ACK     "I'm done"            → connection closed
```

## Key takeaways

- A browser request starts with **DNS**, which turns the name into an IP address, then **TCP** sets up a connection, then **HTTP** carries the page.
- **HTTP runs on top of TCP**. That's why the lab says HTTP is a TCP protocol and generates "considerable overhead": connection setup, acknowledgements and teardown all add packets.
- The **three-way handshake** (SYN → SYN+ACK → ACK) gives both sides a confirmed starting sequence number before any data moves.
- The server sends **plain text with HTML tags**; the browser decides how to display it. HTTP sends it unencrypted, which is why HTTPS exists.
- Every device on the path appears as its own event, so one packet becomes several rows in the list.
- Fixing the source port at `1000` makes the client's traffic easy to identify; normally the client picks a random high port.

## Result

**Completed:** I verified connectivity to `ciscolearn.web.com`, loaded the page, compared the server's `index.html` with the browser output, created a Complex PDU in Simulation mode, and examined the TCP handshake and teardown events behind a single web request.
