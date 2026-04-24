# Networking Exercises

---

## Exercise 1 — RJ-45 Cable

### What is an RJ-45 Cable?

An **RJ-45** (Registered Jack 45) is the standard 8-pin connector used in Ethernet networking. It is the plug and socket found at the end of most network cables, used to connect computers, switches, routers, and other network devices. The cable contains **8 copper wires** arranged in 4 twisted pairs, and the connector has 8 positions (pins) — which is why it is also called an **8P8C** connector.

---

### What is the Difference Between Straight-Through and Crossover RJ-45 Cables?

The difference lies in how the 8 wires are mapped between the two ends of the cable.

| Type | Wire Mapping | Typical Use |
|---|---|---|
| **Straight-Through** | Both ends use the same wiring standard (T568B ↔ T568B) | Connecting **different** device types: PC → Switch, Switch → Router |
| **Crossover** | Ends use opposite standards (T568A ↔ T568B) | Connecting **same** device types: PC → PC, Switch → Switch |

> **Note:** Most modern devices support **Auto-MDI/MDIX**, which detects cable type automatically and eliminates the need for crossover cables.

---

### How Are the IP Addresses Calculated?

Each PC in the topology is assigned an IP address within the appropriate subnet. Looking at the Packet Tracer diagram:

**Pair 1 — Straight-Through (PC0 ↔ PC1):**
- PC0 is given: `192.168.1.3/24`
- Subnet: `192.168.1.0`, Mask: `255.255.255.0`
- PC1 must be in the same `/24` subnet → any address from `192.168.1.1` to `192.168.1.254` (excluding `.3` already used)

**Pair 2 — Crossover (PC2 ↔ PC3):**
- PC3 is given: `192.168.13.82/29`
- Block size for `/29` = 8 → Network: `192.168.13.80`, Broadcast: `192.168.13.87`
- Valid hosts: `192.168.13.81` – `192.168.13.86`
- PC2 must use one of those addresses (e.g., `192.168.13.81`)

**Pair 3 — Crossover (PC4 ↔ PC5):**
- PC4 is given: `192.168.13.254/29`
- Block size for `/29` = 8 → Network: `192.168.13.248`, Broadcast: `192.168.13.255`
- Valid hosts: `192.168.13.249` – `192.168.13.254`
- PC5 must use one of those addresses (e.g., `192.168.13.249`)

---

## Exercise 2 — Hub vs. Switch

### What is a Switch and How Does it Operate?

A **switch** is a smart **Layer 2 (Data Link)** device that intelligently forwards data only to the intended recipient.

**How it works:**
1. When a device sends a frame, the switch reads its **source MAC address** and records which port it came from.
2. This builds a **CAM table** (Content Addressable Memory) — a mapping of MAC addresses to ports.
3. When a frame arrives destined for a known MAC address, the switch sends it **only** to that specific port.
4. If the destination MAC is unknown, it temporarily floods the frame to all ports until it learns the destination.

**Role in networking:** Switches create efficient, segmented networks where each port operates as its own collision domain, maximizing bandwidth and reducing unnecessary traffic.

```
PC1 sends to PC2:

PC1 → Switch → PC2 ✅  (receives it)
             → PC3 ❌  (does NOT receive it)
             → PC4 ❌  (does NOT receive it)
```

---

### What is a Hub and How Does it Operate?

A **hub** is a simple **Layer 1 (Physical)** device that repeats incoming signals out of every other port without any intelligence.

**How it works:**
1. A signal arrives on one port.
2. The hub regenerates (repeats) that signal.
3. It sends the signal out of **every other port**, regardless of the intended destination.

**Role in networking:** Hubs were used to connect multiple devices in early networks, but because all devices share the same bandwidth and collision domain, they are largely obsolete today.

```
PC1 sends to PC2:

PC1 → Hub → PC2 ✅  (receives it)
          → PC3 ✅  (receives it — unintended)
          → PC4 ✅  (receives it — unintended)
```

---

### What Are the Differences Between a Hub and a Switch?

| Feature | Hub | Switch |
|---|---|---|
| **OSI Layer** | Layer 1 (Physical) | Layer 2 (Data Link) |
| **Intelligence** | None — dumb repeater | Smart — learns MAC addresses |
| **Traffic delivery** | Broadcasts to ALL ports | Sends only to the destination port |
| **Collision domain** | One shared domain for all ports | One domain per port |
| **Bandwidth** | Shared among all devices | Dedicated per connection |
| **Security** | Low — all traffic visible to everyone | Higher — traffic isolated per port |
| **Speed** | Half-duplex only | Full-duplex capable |

---

### OSI Model Layers for Hub and Switch

| Device | OSI Layer | Reason |
|---|---|---|
| **Hub** | **Layer 1 — Physical** | Operates only with electrical signals; has no awareness of addresses or data content |
| **Switch** | **Layer 2 — Data Link** | Uses MAC addresses (a Layer 2 concept) to make intelligent forwarding decisions |

---

## Exercise 3 — Network Services

### What is a Server and What is its Purpose?

A **server** is a device (or software application) that provides resources, data, or services to other devices — called **clients** — over a network. Servers run continuously, listening for and responding to client requests.

Common examples: web server, file server, email server, DNS server, DHCP server.

---

### How Does DHCP Work?

**DHCP** (Dynamic Host Configuration Protocol) automatically assigns IP configuration to devices on a network so they do not need to be manually configured.

- **OSI Layer:** 7 (Application)
- **Port:** 67 (server), 68 (client)
- **Transport:** UDP

**The DORA Process:**

| Step | Who | Action |
|---|---|---|
| **Discover** | Client | Broadcasts "I need an IP address" to the network |
| **Offer** | Server | Replies with an available IP address offer |
| **Request** | Client | Formally requests the offered IP |
| **Acknowledge** | Server | Confirms and finalizes the assignment |

The DHCP server also provides the subnet mask, default gateway, and DNS server address.

---

### What is DNS and What Role Does it Play?

**DNS** (Domain Name System) translates human-readable domain names into IP addresses that computers can use to communicate.

- **OSI Layer:** 7 (Application)
- **Port:** 53
- **Transport:** UDP (queries), TCP (large responses or zone transfers)

Without DNS, you would need to remember `192.168.1.99` instead of `server.local`.

**Common DNS Record Types:**

| Record | Purpose | Example |
|---|---|---|
| **A** | Maps a domain to an IPv4 address | `server.local → 192.168.1.99` |
| **AAAA** | Maps a domain to an IPv6 address | `server.local → ::1` |
| **CNAME** | Alias pointing to another domain name | `www → server.local` |
| **MX** | Specifies the mail server for a domain | `mail.local` |
| **NS** | Identifies the authoritative name server | `ns1.local` |
| **PTR** | Reverse lookup — IP address to domain name | `192.168.1.99 → server.local` |

---

### What is HTTP and How is it Used?

**HTTP** (HyperText Transfer Protocol) is the protocol used to transfer web pages and content between a web server and a browser.

- **OSI Layer:** 7 (Application)
- **Port:** 80
- **Transport:** TCP

HTTP is **unencrypted** — data travels in plain text and can be intercepted by anyone on the network path.

---

### What is HTTPS and How Does it Differ from HTTP?

**HTTPS** (HTTP Secure) is HTTP with an added **TLS/SSL encryption layer**. All data exchanged between the client and server is encrypted, preventing eavesdropping and tampering.

- **OSI Layer:** 7 (Application)
- **Port:** 443
- **Transport:** TCP

| Feature | HTTP | HTTPS |
|---|---|---|
| Encryption | ❌ None | ✅ TLS/SSL |
| Port | 80 | 443 |
| Data visible to interceptors | Yes | No (encrypted) |
| Use case | Internal/legacy systems | Any site handling sensitive data |

---

### What is FTP and How Does it Operate?

**FTP** (File Transfer Protocol) is used to transfer files between a client and a server over a network.

- **OSI Layer:** 7 (Application)
- **Ports:** 21 (control/commands), 20 (data transfer)
- **Transport:** TCP

FTP supports uploading, downloading, directory listing, renaming, and deleting files on a remote server. Like HTTP, standard FTP is unencrypted; **FTPS** or **SFTP** are secure alternatives.

---

### What are TCP and UDP and What Distinguishes Them?

Both operate at **Layer 4 (Transport)** of the OSI model.

| Feature | TCP | UDP |
|---|---|---|
| **Full name** | Transmission Control Protocol | User Datagram Protocol |
| **Connection** | Connection-oriented (3-way handshake) | Connectionless |
| **Reliability** | Guaranteed delivery, ordering, error checking | No guarantee — "fire and forget" |
| **Speed** | Slower (reliability overhead) | Faster (minimal overhead) |
| **Use cases** | HTTP/S, FTP, email, SSH | DNS queries, DHCP, video streaming, VoIP |

---

### What is a Port and What is its Function?

A **port** is a logical number (0–65535) that identifies a specific service or process on a device. While an IP address routes traffic to the right machine, the port number routes it to the right application on that machine.

Think of it like an apartment building: the IP address is the building address, and the port is the apartment number.

---

### Port and OSI Layer Reference for Each Protocol

| Protocol | OSI Layer | Port(s) | Transport |
|---|---|---|---|
| **FTP** (control) | 7 — Application | 21 | TCP |
| **FTP** (data) | 7 — Application | 20 | TCP |
| **SSH** | 7 — Application | 22 | TCP |
| **DNS** | 7 — Application | 53 | UDP / TCP |
| **DHCP** (server) | 7 — Application | 67 | UDP |
| **DHCP** (client) | 7 — Application | 68 | UDP |
| **HTTP** | 7 — Application | 80 | TCP |
| **HTTPS** | 7 — Application | 443 | TCP |
| **TCP / UDP** | 4 — Transport | — | — |

---

## Exercise 4 — Routers & Routing

### What is a Router and What is its Role?

A **router** is a **Layer 3 (Network)** device that forwards data packets between different networks using **IP addresses**. Its primary role is to determine the best path for data to travel from one network to another, connecting separate subnets and enabling communication across the internet.

---

### How Does a Switch Differ from a Router?

| Feature | Switch | Router |
|---|---|---|
| **OSI Layer** | Layer 2 (Data Link) | Layer 3 (Network) |
| **Addressing** | MAC addresses | IP addresses |
| **Scope** | Within a single network (LAN) | Between different networks |
| **Function** | Forwards frames within one subnet | Routes packets across subnets |
| **Connects** | Devices in the same network | Separate networks |

---

### At Which OSI Layer Does a Router Operate?

A router operates at **Layer 3 — the Network layer** of the OSI model. This is the layer responsible for logical addressing (IP addresses) and path determination.

---

### What is a Default Gateway?

The **default gateway** is the IP address of the router interface that connects a local network to external networks. When a device needs to communicate with an address **outside its own subnet**, it sends the traffic to the default gateway, which then routes it toward the destination.

**Example:** A PC with IP `192.168.1.2/24` wants to reach `192.168.2.5`. Since `192.168.2.5` is in a different subnet, the PC forwards the packet to its default gateway (e.g., `192.168.1.1`), and the router handles the rest.

---

## Exercise 6 — Routing Table

> 💬 **Your Turn**
>
> In your own words, explain:
>
> - **What is a routing table?**
> - **What role does it play in routing network traffic?**
>
> Think about: What information does a routing table contain? How does a router use it when a packet arrives? What happens if no matching route is found?
