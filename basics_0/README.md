# Networking Basics — Learning Objectives

## 1. OSI Model

### What it is

The **OSI Model** (Open Systems Interconnection) is a conceptual framework that standardizes how different network systems communicate. It divides network communication into **7 distinct layers**, each with a specific role, so that hardware and software from different vendors can interoperate.

### How many layers it has

The OSI model has **7 layers**.

### How it is organized

| Layer | Name | Role | Example protocols |
|---|---|---|---|
| 7 | **Application** | Interface for end-user applications | HTTP, FTP, DNS, SMTP |
| 6 | **Presentation** | Data formatting, encryption, compression | SSL/TLS, JPEG, ASCII |
| 5 | **Session** | Opens, manages and closes sessions | NetBIOS, RPC |
| 4 | **Transport** | End-to-end data transfer, error control | TCP, UDP |
| 3 | **Network** | Logical addressing and routing | IP, ICMP, ARP |
| 2 | **Data Link** | Physical addressing (MAC), error detection | Ethernet, Wi-Fi (802.11) |
| 1 | **Physical** | Raw bit transmission over a medium | Cables, hubs, radio waves |

> **Mnemonic (top → bottom):** *All People Seem To Need Data Processing*

Data flows **down** the layers on the sender's side, travels through the network, and flows **up** the layers on the receiver's side.

---

## 2. LAN — Local Area Network

### What it is

A **LAN** is a network that connects devices within a **limited geographical area** such as a home, office, school, or building.

### Typical usage

- Connecting computers, printers, and servers within an office
- Sharing files and resources between employees
- Local gaming networks
- Home Wi-Fi networks connecting phones, laptops, and smart TVs

### Typical geographical size

- A **single room** to a **building** or **campus**
- Range: a few meters up to ~1 km
- Typically owned and managed by a single organization or individual

---

## 3. WAN — Wide Area Network

### What it is

A **WAN** connects multiple LANs or devices over **large geographical distances**, often using public infrastructure (phone lines, fiber optic cables, satellites).

### Typical usage

- Connecting branch offices of a company across different cities or countries
- Providing internet access to homes and businesses
- Corporate VPNs linking remote workers to a central office

### Typical geographical size

- Spans **cities, countries, or continents**
- Range: hundreds to thousands of kilometers
- The **Internet** is the largest WAN in existence

---

## 4. The Internet

### What it is

The **Internet** is a global network of interconnected networks (LANs, WANs, and other networks) that communicate using the **TCP/IP protocol suite**. It is the largest WAN in the world.

---

### What is an IP address

An **IP address** (Internet Protocol address) is a **unique numerical label** assigned to every device connected to a network. It serves two purposes:
- **Identification** — who the device is
- **Location addressing** — where the device is on the network

Example: `192.168.1.10` (IPv4) or `2001:0db8:85a3::8a2e:0370:7334` (IPv6)

---

### The 2 types of IP address

| Type | Description | Example |
|---|---|---|
| **IPv4** | 32-bit address, written as 4 decimal octets separated by dots | `192.168.1.1` |
| **IPv6** | 128-bit address, written as 8 groups of 4 hex digits separated by colons | `2001:db8::1` |

---

### What is `localhost`

`localhost` is the **hostname that refers to the current device itself**. It resolves to the loopback IP address:
- IPv4: `127.0.0.1`
- IPv6: `::1`

It is used to test network applications on the same machine without going through an external network.

```bash
ping localhost       # pings 127.0.0.1
curl http://localhost:3000  # accesses a local server
```

---

### What is a subnet

A **subnet** (subnetwork) is a **logical subdivision of an IP network**. Subnetting divides a large network into smaller, more manageable pieces.

A subnet is defined by:
- An **IP address**: e.g. `192.168.1.0`
- A **subnet mask**: e.g. `255.255.255.0` (or `/24` in CIDR notation)

```
192.168.1.0/24 → addresses from 192.168.1.0 to 192.168.1.255 (256 addresses)
192.168.1.0/28 → addresses from 192.168.1.0 to 192.168.1.15  (16 addresses)
```

Subnetting improves **security**, **performance**, and **IP address management**.

---

### Why IPv6 was created

IPv4 uses 32-bit addresses, allowing for ~**4.3 billion** unique addresses. With the explosive growth of internet-connected devices (computers, phones, IoT), **IPv4 addresses ran out**.

IPv6 was created to solve this by using **128-bit addresses**, providing ~**340 undecillion** (3.4 × 10³⁸) unique addresses — effectively unlimited.

Additional improvements in IPv6:
- Simpler packet headers (faster routing)
- Built-in IPSec support (better security)
- Better auto-configuration (no need for DHCP in many cases)
- No need for NAT (Network Address Translation)

---

## 5. TCP / UDP

### The 2 main data transfer protocols for IP

At the **Transport layer (Layer 4)** of the OSI model, the two main protocols are:

- **TCP** — Transmission Control Protocol
- **UDP** — User Datagram Protocol

---

### Main difference between TCP and UDP

| Feature | TCP | UDP |
|---|---|---|
| **Connection** | Connection-oriented (handshake required) | Connectionless (no handshake) |
| **Reliability** | Guaranteed delivery, retransmits lost packets | No guarantee, packets may be lost |
| **Order** | Packets arrive in order | No ordering guarantee |
| **Speed** | Slower (overhead for reliability) | Faster (minimal overhead) |
| **Error checking** | Yes — acknowledgement system | Minimal (checksum only) |
| **Use cases** | Web (HTTP/S), email, file transfer | Streaming, gaming, DNS, VoIP |

**TCP** → use when **accuracy matters** (you need all the data, in order).  
**UDP** → use when **speed matters** (losing a few packets is acceptable).

---

### What is a port

A **port** is a **logical communication endpoint** that identifies a specific process or service on a device. It allows a single IP address to run multiple services simultaneously.

- Ports range from **0 to 65535**
- **0–1023**: Well-known ports (reserved for standard services)
- **1024–49151**: Registered ports (used by applications)
- **49152–65535**: Dynamic/private ports

A full network address is `IP:port`, for example: `192.168.1.10:80`

---

### Memorize: SSH, HTTP, HTTPS port numbers

| Protocol | Port | Description |
|---|---|---|
| **SSH** | `22` | Secure Shell — encrypted remote terminal access |
| **HTTP** | `80` | HyperText Transfer Protocol — unencrypted web traffic |
| **HTTPS** | `443` | HTTP Secure — encrypted web traffic (TLS) |

---

### Tool used to check if a device is connected to a network

The **`ping`** command uses the **ICMP** (Internet Control Message Protocol) to test connectivity between two devices.

It sends an **echo request** to a target and waits for an **echo reply**.

```bash
ping google.com
# PING google.com (142.250.74.46): 56 data bytes
# 64 bytes from 142.250.74.46: icmp_seq=0 ttl=116 time=12.3 ms

ping 192.168.1.1     # ping a local device
ping localhost       # ping yourself (loopback test)
ping -c 4 8.8.8.8   # send exactly 4 packets
```

If you receive replies → the device is reachable.  
If you get `Request timeout` → the device is unreachable or blocking ICMP.