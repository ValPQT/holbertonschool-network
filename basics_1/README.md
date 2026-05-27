# Networking Basics 2 — Learning Objectives

## 1. What is `localhost` / `127.0.0.1`

**`localhost`** is the hostname that refers to **the current machine itself**. When a program connects to `localhost`, the connection never leaves the device — it loops back internally.

It resolves to the **loopback IP address**:
- IPv4: `127.0.0.1`
- IPv6: `::1`

The entire `127.0.0.0/8` block is reserved for loopback, but `127.0.0.1` is the standard address used in practice.

### Common use cases

```bash
# Test a web server running locally
curl http://localhost:3000
curl http://127.0.0.1:3000

# Ping yourself to verify the TCP/IP stack is working
ping localhost
ping 127.0.0.1

# Connect to a local database
psql -h localhost -U postgres
mysql -h 127.0.0.1 -u root -p
```

> Traffic sent to `127.0.0.1` **never reaches the network card** — it is handled entirely within the OS kernel. This makes it useful for development and testing without any external network.

---

## 2. What is `0.0.0.0`

`0.0.0.0` is a **non-routable meta-address** that has different meanings depending on context:

### As a server binding address — "listen on all interfaces"

When a server binds to `0.0.0.0`, it accepts connections on **every available network interface** (localhost, LAN, Wi-Fi, etc.).

```bash
# A server listening on 0.0.0.0:80 accepts:
# - http://127.0.0.1:80       (loopback)
# - http://192.168.1.10:80    (local network)
# - http://203.0.113.5:80     (public IP, if exposed)
```

Compared to binding on `127.0.0.1`, which only accepts local connections:

| Binding address | Who can connect |
|---|---|
| `127.0.0.1` | Only the local machine |
| `192.168.1.10` | Only via that specific interface |
| `0.0.0.0` | Anyone, on any interface |

### In routing tables — "default route"

In a routing table, `0.0.0.0/0` means **"match any destination"** — it is the default gateway entry.

```bash
route -n
# Destination    Gateway        Genmask   ...
# 0.0.0.0        192.168.1.1    0.0.0.0   ...  ← default route
```

### As a source address

A device uses `0.0.0.0` as its source address **before it has been assigned an IP** (e.g., during DHCP discovery).

---

## 3. What is `/etc/hosts`

`/etc/hosts` is a **plain text file** on Unix/Linux systems that maps **hostnames to IP addresses** locally, without querying a DNS server.

It is checked **before DNS** in most systems, making it a powerful tool for overriding name resolution.

### File format

```
IP_ADDRESS    HOSTNAME    [ALIAS...]
```

### Default content on most Linux systems

```
127.0.0.1       localhost
127.0.1.1       myhostname
::1             localhost ip6-localhost ip6-loopback
```

### Common use cases

**1. Define custom local hostnames**
```
127.0.0.1   myapp.local
127.0.0.1   api.myapp.local
```
Now `curl http://myapp.local` works without a DNS server.

**2. Block unwanted domains (redirect to nowhere)**
```
0.0.0.0   ads.example.com
0.0.0.0   tracker.analytics.com
```

**3. Override DNS for testing (point a domain to a different server)**
```
192.168.1.50   production.example.com
```
Useful to test a new server before updating public DNS.

### Viewing and editing

```bash
cat /etc/hosts          # view the file
sudo nano /etc/hosts    # edit the file (requires root)
sudo vim /etc/hosts
```

> Changes take effect **immediately** — no restart needed. However, some applications cache DNS and may need to be restarted.

---

## 4. How to display your machine's active network interfaces

A **network interface** is a point of connection between a device and a network (physical like `eth0`, wireless like `wlan0`, or virtual like `lo`).

### Using `ifconfig` (traditional)

```bash
ifconfig
```

```
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
      inet 192.168.1.10  netmask 255.255.255.0  broadcast 192.168.1.255
      inet6 fe80::1  prefixlen 64
      ether 00:1a:2b:3c:4d:5e  txqueuelen 1000

lo:   flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
      inet 127.0.0.1  netmask 255.0.0.0
      inet6 ::1  prefixlen 128
```

Show a specific interface:
```bash
ifconfig eth0
```

> `ifconfig` may not be installed by default on modern systems. Install with: `sudo apt install net-tools`

### Using `ip` (modern — recommended)

```bash
ip addr          # show all interfaces and their addresses
ip addr show     # same
ip a             # shorthand
```

Show a specific interface:
```bash
ip addr show eth0
```

Show only active (UP) interfaces:
```bash
ip link show up
```

### Common interface names

| Name | Type |
|---|---|
| `lo` | Loopback (127.0.0.1) |
| `eth0` / `ens3` | Wired Ethernet |
| `wlan0` / `wlp2s0` | Wi-Fi |
| `docker0` | Docker virtual bridge |
| `tun0` / `vpn0` | VPN tunnel |

### Quick reference

```bash
ifconfig                  # all interfaces (net-tools)
ifconfig -a               # including inactive interfaces
ip a                      # all interfaces (iproute2)
ip link show up           # only active interfaces
hostname -I               # just the IP addresses, no details
```