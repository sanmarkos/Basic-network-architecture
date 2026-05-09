# 🖧 Enterprise Network Architecture — Complete Beginner's Guide

> **Project:** Enterprise Network in Cisco Packet Tracer  
> **Topology:** Office Floor (Upper) + Warehouse Floor (Ground) + Internet Connectivity  
> **Tools Used:** Cisco Packet Tracer, Cisco IOS CLI

---

## 📋 Table of Contents

1. [What Did We Build?](#1-what-did-we-build)
2. [Understanding the Big Picture](#2-understanding-the-big-picture)
3. [Key Networking Concepts Explained](#3-key-networking-concepts-explained)
4. [VLANs — Virtual Local Area Networks](#4-vlans--virtual-local-area-networks)
5. [IP Addressing & Subnetting](#5-ip-addressing--subnetting)
6. [Devices in This Network](#6-devices-in-this-network)
7. [Switch Configuration](#7-switch-configuration)
8. [Router Subinterfaces (Router-on-a-Stick)](#8-router-subinterfaces-router-on-a-stick)
9. [Routing — How Packets Find Their Way](#9-routing--how-packets-find-their-way)
10. [DHCP — Automatic IP Assignment](#10-dhcp--automatic-ip-assignment)
11. [DNS — Domain Name System](#11-dns--domain-name-system)
12. [NAT/PAT — Internet Access](#12-natpat--internet-access)
13. [ACLs — Access Control Lists (Security)](#13-acls--access-control-lists-security)
14. [SSH — Secure Remote Management](#14-ssh--secure-remote-management)
15. [DMZ — Demilitarized Zone](#15-dmz--demilitarized-zone)
16. [Services: HTTP, FTP, SYSLOG](#16-services-http-ftp-syslog)
17. [Wireless Access Points (WiFi)](#17-wireless-access-points-wifi)
18. [Additional Networking Concepts You Should Know](#18-additional-networking-concepts-you-should-know)
19. [Complete Command Reference](#19-complete-command-reference)
20. [How to Upload This Project to GitHub](#20-how-to-upload-this-project-to-github)
21. [Summary — What You've Learned](#21-summary--what-youve-learned)

---

## 1. What Did We Build?

We built a **simulated enterprise network** that represents a real company with two floors:

| Floor | What's There |
|-------|-------------|
| **Office Floor (Upper)** | HR, IT, Finance, Printers, Internal Server, DMZ Server, Guest WiFi |
| **Warehouse Floor (Ground)** | Warehouse PCs, Warehouse WiFi, Warehouse Printers |
| **Internet** | ISP Router connecting both floors to the Internet & a public web server |

Think of it like a **company building** where:
- Different departments are **isolated** from each other for security (using VLANs)
- Everyone can still access the **Internet** (using NAT)
- Guests can only browse the Internet, not company files (using ACLs)
- HR can reach printers, but not IT or Finance systems (using ACLs)
- IT admins can **remotely manage** all devices securely (using SSH)
- Warehouse staff can reach the Internet, but not the Office internal network (using ACLs)

---

## 2. Understanding the Big Picture

```
[Office Devices]──[Office-Core-SW]──[Office-Router]──[ISP Router]──[Internet]
                                                            |
[Warehouse Devices]──[Warehouse-SW]──[Warehouse-Router]────┘
```

### The Three Routers

| Router | Hostname | Role |
|--------|----------|------|
| **Office Router** | `Office-Router` | Routes all office VLANs, DHCP server, NAT, ACLs |
| **Warehouse Router** | `Warehouse-Router` | Routes all warehouse VLANs, default route to ISP |
| **ISP Router** | — | Simulates the ISP; has static routes back to both sites |

### The Two Switches

| Switch | Hostname | Model | Role |
|--------|----------|-------|------|
| **Office Switch** | `Office-Core-SW` | Cisco 3560-24PS | Connects all office devices; 8 VLANs |
| **Warehouse Switch** | `Warehouse-SW` | Cisco 2960-24TT | Connects all warehouse devices; 3 VLANs |

> **Switch vs Router — Simple Explanation:**
> - A **Switch** is like a power strip — it connects many devices on the *same* floor/network
> - A **Router** is like a post office — it *routes* messages between *different* networks

---

## 3. Key Networking Concepts Explained

### 🔹 IP Address
An IP address is a device's **unique address on a network**, like a house number on a street.
- Example: `192.168.10.5`
- There are two types used in this project:
  - **Private IPs** (`192.168.x.x`, `172.16.x.x`) — used inside the company, invisible on the Internet
  - **Public IPs** (`200.1.1.x`, `100.1.1.x`) — used on the Internet

### 🔹 Subnet Mask
A subnet mask defines **how many devices** can exist in a network and which part of the IP is the "street name" vs the "house number."
- `255.255.255.0` = `/24` = up to 254 usable hosts per network
- `255.255.255.252` = `/30` = only 2 usable hosts (used for router-to-router links)

### 🔹 Wildcard Mask
Used in ACLs — it's the **opposite** of a subnet mask. `0` means "must match this bit exactly," `255` means "any value is fine."
- `0.0.0.255` → match any host in that /24 network
- `0.0.255.255` → match any host in that /16 network

### 🔹 Default Gateway
The **exit door** of your local network. Every PC needs to know its gateway so it can send traffic to other networks or the Internet.

### 🔹 Routing Table
A list stored in a router that says *"to reach network X, send the packet toward Y."* Every router has one. We'll read the actual routing tables from this project in Section 9.

### 🔹 Protocol
A set of rules for communication. Examples: TCP (reliable delivery), UDP (fast, no guarantee), ICMP (ping), HTTP (web), SSH (remote access).

### 🔹 Port Numbers
When data travels, it also carries a **port number** that identifies which service it's for:
- Port `80` = HTTP (web browsing)
- Port `22` = SSH
- Port `21` = FTP
- Port `53` = DNS

---

## 4. VLANs — Virtual Local Area Networks

### What is a VLAN?

Imagine a big open office where HR, IT, and Finance all share the same physical switches and cables. A VLAN **logically divides** that one physical network into separate isolated networks. Devices on different VLANs **cannot talk to each other** unless a router explicitly allows it.

**Real-world analogy:** Think of VLANs like apartments in a building. All apartments share the same walls and plumbing (physical switch), but each apartment (VLAN) is private — you need a key (router rule) to enter another one.

### VLANs in This Project

#### Office Floor VLANs (on `Office-Core-SW`)

| VLAN ID | Name | Subnet | Gateway IP |
|---------|------|--------|------------|
| 10 | HR | 192.168.10.0/24 | 192.168.10.1 |
| 20 | IT | 192.168.20.0/24 | 192.168.20.1 |
| 30 | Finance | 192.168.30.0/24 | 192.168.30.1 |
| 40 | Printers | 192.168.40.0/24 | 192.168.40.1 |
| 50 | Servers | 192.168.50.0/24 | 192.168.50.1 |
| 60 | DMZ | 192.168.60.0/24 | 192.168.60.1 |
| 70 | Guest | 192.168.70.0/24 | 192.168.70.1 |
| 99 | Management | 192.168.99.0/24 | 192.168.99.1 |

#### Warehouse Floor VLANs (on `Warehouse-SW`)

| VLAN ID | Name | Subnet | Gateway IP |
|---------|------|--------|------------|
| 110 | Warehouse-PC | 172.16.10.0/24 | 172.16.10.1 |
| 120 | Warehouse-WiFi | 172.16.20.0/24 | 172.16.20.1 |
| 130 | Warehouse-Printer | 172.16.30.0/24 | 172.16.30.1 |

---

## 5. IP Addressing & Subnetting

### WAN Links (Router-to-Router)

These are the serial cable links that connect routers — they simulate real Internet connections.

| Link | Subnet | Office/Warehouse End | ISP End |
|------|--------|---------------------|---------|
| Office ↔ ISP | 200.1.1.0/30 | 200.1.1.2 | 200.1.1.1 |
| Warehouse ↔ ISP | 200.1.1.8/30 | 200.1.1.10 | 200.1.1.9 |

> **Why /30?**  
> A `/30` subnet has exactly 4 IPs: 1 network address, **2 usable** (one per router end), 1 broadcast.  
> This is perfect for a cable between two routers — no wasted IPs.

### Internet Side

| Device | IP |
|--------|----|
| ISP Router (G0/0 — Internet facing) | 100.1.1.1 |
| Simulated Web Server (`internet-website.com`) | 100.1.1.10 |

---

## 6. Devices in This Network

| Device | VLAN | IP Assignment | IP Address |
|--------|------|---------------|------------|
| HR Laptop | 10 | DHCP | 192.168.10.21+ |
| IT PC | 20 | DHCP | 192.168.20.21+ |
| Finance PC | 30 | DHCP | 192.168.30.21+ |
| Office Printer | 40 | DHCP | 192.168.40.21+ |
| Internal Server | 50 | Static | 192.168.50.10 |
| DMZ Server | 60 | Static | 192.168.60.10 |
| Guest Laptop | 70 | DHCP | 192.168.70.21+ |
| Warehouse PC-1 / PC-2 | 110 | DHCP | 172.16.10.21+ |
| Warehouse Printer | 130 | DHCP | 172.16.30.21+ |

> **Why do servers get static IPs?**  
> Other devices (and DNS records) need to find the server at a predictable, fixed address. If the server's IP changed every reboot, nothing could reach it.

> **Why do DHCP IPs start at .21+?**  
> Because we excluded `.1` to `.20` — those are reserved for gateways and fixed devices.

---

## 7. Switch Configuration

Both switches need VLANs created, then each port assigned to the correct VLAN.

### Office Switch (`Office-Core-SW`)

```bash
hostname Office-Core-SW

! Create all VLANs and name them
vlan 10
 name HR
vlan 20
 name IT
vlan 30
 name Finance
vlan 40
 name Printers
vlan 50
 name Servers
vlan 60
 name DMZ
vlan 70
 name Guest
vlan 99
 name Management

! Trunk port — carries all VLANs to the Office Router (one cable, all VLANs)
interface g1/0/1
 switchport mode trunk

! Access ports — each connected to exactly one VLAN
interface g1/0/2
 switchport mode access
 switchport access vlan 10         ! HR Laptop

interface range g1/0/3-4
 switchport mode access
 switchport access vlan 20         ! IT PCs

interface range g1/0/5-6
 switchport mode access
 switchport access vlan 30         ! Finance PCs

interface g1/0/7
 switchport mode access
 switchport access vlan 40         ! Office Printer

interface g1/0/8
 switchport mode access
 switchport access vlan 50         ! Internal Server

interface g1/0/9
 switchport mode access
 switchport access vlan 60         ! DMZ Server
```

### Warehouse Switch (`Warehouse-SW`)

```bash
hostname Warehouse-SW

vlan 110
 name Warehouse-PC
vlan 120
 name Warehouse-WiFi
vlan 130
 name Warehouse-Printer

! Trunk port to Warehouse Router
interface g1/0/1
 switchport mode trunk

! Access port for Warehouse WiFi Access Point
interface g1/0/2
 switchport mode access
 switchport access vlan 120

! Access ports for Warehouse PCs
interface range g1/0/3-6
 switchport mode access
 switchport access vlan 110

! Access port for Warehouse Printer
interface g1/0/7
 switchport mode access
 switchport access vlan 130
```

> **Key concepts:**
> - `switchport mode trunk` — This port carries **all VLANs** tagged (used between switch and router)
> - `switchport mode access` — This port carries **exactly one** VLAN untagged (used for end devices like PCs)
> - `switchport access vlan X` — Assigns the access port to VLAN X
> - `interface range g1/0/3-6` — Configures multiple ports at once (shortcut)

---

## 8. Router Subinterfaces (Router-on-a-Stick)

### The Problem
Routers have limited physical ports. We have 8 VLANs on the office side but don't want 8 separate cables between the router and switch.

### The Solution
We use **one physical cable (trunk)** but create **virtual sub-ports (subinterfaces)** on the router — one per VLAN. The switch sends all VLAN traffic tagged down that one trunk, and the router uses the VLAN tags (802.1Q) to sort them.

```
Physical trunk cable
    │
    ├─ Tag=10 → G0/0.10 → 192.168.10.1  (HR gateway)
    ├─ Tag=20 → G0/0.20 → 192.168.20.1  (IT gateway)
    ├─ Tag=30 → G0/0.30 → 192.168.30.1  (Finance gateway)
    └─ ...and so on
```

### Office Router Subinterfaces

```bash
hostname Office-Router

interface g0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0

interface g0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0

interface g0/0.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0

interface g0/0.40
 encapsulation dot1Q 40
 ip address 192.168.40.1 255.255.255.0

interface g0/0.50
 encapsulation dot1Q 50
 ip address 192.168.50.1 255.255.255.0

interface g0/0.60
 encapsulation dot1Q 60
 ip address 192.168.60.1 255.255.255.0

interface g0/0.70
 encapsulation dot1Q 70
 ip address 192.168.70.1 255.255.255.0

interface g0/0.99
 encapsulation dot1Q 99
 ip address 192.168.99.1 255.255.255.0

! WAN link to ISP
interface s0/1/0
 ip address 200.1.1.2 255.255.255.252
 no shutdown
```

### Warehouse Router Subinterfaces

```bash
hostname Warehouse-Router

interface g0/0.110
 encapsulation dot1Q 110
 ip address 172.16.10.1 255.255.255.0

interface g0/0.120
 encapsulation dot1Q 120
 ip address 172.16.20.1 255.255.255.0

interface g0/0.130
 encapsulation dot1Q 130
 ip address 172.16.30.1 255.255.255.0

! WAN link to ISP
interface s0/1/0
 ip address 200.1.1.10 255.255.255.252
 no shutdown
```

> **Command breakdown:**
> - `interface g0/0.10` — Creates subinterface `.10` on physical port `g0/0`. By convention, the number after the dot matches the VLAN ID
> - `encapsulation dot1Q 10` — "Listen for frames tagged with VLAN 10" (802.1Q is the IEEE standard for VLAN tagging)
> - `ip address 192.168.10.1 255.255.255.0` — This IP becomes the **default gateway** for all VLAN 10 devices
> - `no shutdown` — Turns the interface **ON** (all Cisco router interfaces are off by default)

---

## 9. Routing — How Packets Find Their Way

### What is a Routing Table?
Every router keeps a **routing table** — a map of known networks and where to forward traffic. When a packet arrives, the router looks up the destination IP and sends it in the right direction.

### Types of Route Entries

| Symbol | Type | Meaning |
|--------|------|---------|
| `C` | Connected | Network directly attached to this router's interface |
| `L` | Local | The router's own interface IP address |
| `S` | Static | Manually configured route by an admin |
| `S*` | Default static | The "last resort" — used when no other route matches |

### Default Routes

A default route tells the router: *"If you don't know where to send this, send it here."*

```bash
! Office Router — unknown traffic goes toward ISP
ip route 0.0.0.0 0.0.0.0 200.1.1.1

! Warehouse Router — unknown traffic goes toward ISP
ip route 0.0.0.0 0.0.0.0 200.1.1.9
```

> `0.0.0.0 0.0.0.0` matches **everything**. `200.1.1.1` and `200.1.1.9` are the ISP router's interfaces — called the **next hop**.

### Static Routes on the ISP Router

The ISP needs to know how to send return traffic back to the office and warehouse:

```bash
! Route to all office networks — via Office Router
ip route 192.168.0.0 255.255.0.0 200.1.1.2

! Route to all warehouse networks — via Warehouse Router
ip route 172.16.0.0 255.255.0.0 200.1.1.10
```

### Reading the Actual Routing Tables

#### Office Router (`show ip route`)

```
S    172.16.0.0/16 [1/0] via 200.1.1.1         ← Warehouse networks (reached via ISP)
C    192.168.10.0/24  connected  G0/0.10        ← HR VLAN (directly attached)
L    192.168.10.1/32  connected  G0/0.10        ← Router's own IP on HR subinterface
C    192.168.20.0/24  connected  G0/0.20        ← IT VLAN
L    192.168.20.1/32  connected  G0/0.20
...  (one C + L pair for each VLAN subinterface)
C    200.1.1.0/30     connected  Serial0/1/0    ← WAN link subnet
L    200.1.1.2/32     connected  Serial0/1/0    ← Router's own WAN IP
S*   0.0.0.0/0 [1/0] via 200.1.1.1             ← Default route (for Internet)
```

> **What does `[1/0]` mean?**  
> This is `[administrative distance / metric]`. AD of `1` = static route (very trustworthy). Metric `0` = cost to reach. Lower is better for both.

> **Notice `172.16.0.0/16`** — This is a **summary route** covering all warehouse subnets at once. One routing entry handles `.110`, `.120`, and `.130` networks. The ISP router taught the Office Router about this.

#### Warehouse Router (`show ip route`)

```
C    172.16.10.0/24   connected  G0/0.110       ← Warehouse PCs
L    172.16.10.1/32   connected  G0/0.110
C    172.16.20.0/24   connected  G0/0.120       ← Warehouse WiFi
C    172.16.30.0/24   connected  G0/0.130       ← Warehouse Printers
S    192.168.0.0/16 [1/0] via 200.1.1.9         ← Office networks (via ISP)
C    200.1.1.8/30     connected  Serial0/1/0    ← WAN link
L    200.1.1.10/32    connected  Serial0/1/0
S*   0.0.0.0/0 [1/0] via 200.1.1.9             ← Default route (Internet)
```

#### ISP Router (`show ip route`)

```
C    100.1.1.0/24     connected  G0/0           ← Internet network
S    172.16.0.0/16    via 200.1.1.10            ← Warehouse (static)
S    192.168.0.0/16   via 200.1.1.2             ← Office (static)
C    200.1.1.0/30     connected  Serial0/1/0    ← Link to Office Router
C    200.1.1.8/30     connected  Serial0/1/1    ← Link to Warehouse Router
```

> The ISP router has **two serial interfaces** (`S0/1/0` and `S0/1/1`) — one for each site. It acts as the hub connecting both locations to the Internet.

---

## 10. DHCP — Automatic IP Assignment

### What is DHCP?
DHCP **(Dynamic Host Configuration Protocol)** automatically hands devices an IP address, subnet mask, gateway, and DNS server the moment they connect. Without DHCP, you'd manually configure every single PC.

**How it works — the DORA process:**
1. **D**iscover — PC broadcasts: *"Is there a DHCP server out there?"*
2. **O**ffer — Server replies: *"Yes! I'll give you 192.168.10.21"*
3. **R**equest — PC says: *"I'll take that IP, please confirm"*
4. **A**cknowledge — Server confirms: *"It's yours for 24 hours (lease time)"*

### DHCP Configuration (on the Office Router)

```bash
! ── Reserve IPs .1 to .20 in every VLAN so they're never handed out automatically.
! These low IPs are for gateways, static servers, and printers with fixed addresses.
ip dhcp excluded-address 192.168.10.1 192.168.10.20
ip dhcp excluded-address 192.168.20.1 192.168.20.20
ip dhcp excluded-address 192.168.30.1 192.168.30.20
ip dhcp excluded-address 192.168.40.1 192.168.40.20
ip dhcp excluded-address 192.168.50.1 192.168.50.20
ip dhcp excluded-address 192.168.60.1 192.168.60.20
ip dhcp excluded-address 192.168.70.1 192.168.70.20

! ── Office VLAN Pools ──
ip dhcp pool HR
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 192.168.50.10
 domain-name markos.local

ip dhcp pool IT
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 dns-server 192.168.50.10
 domain-name markos.local

ip dhcp pool FINANCE
 network 192.168.30.0 255.255.255.0
 default-router 192.168.30.1
 dns-server 192.168.50.10
 domain-name markos.local

ip dhcp pool PRINTERS
 network 192.168.40.0 255.255.255.0
 default-router 192.168.40.1
 dns-server 192.168.50.10
 domain-name markos.local

ip dhcp pool SERVERS
 network 192.168.50.0 255.255.255.0
 default-router 192.168.50.1
 dns-server 192.168.50.10
 domain-name markos.local

ip dhcp pool DMZ
 network 192.168.60.0 255.255.255.0
 default-router 192.168.60.1
 dns-server 192.168.50.10
 domain-name markos.local

ip dhcp pool GUEST_WIFI
 network 192.168.70.0 255.255.255.0
 default-router 192.168.70.1
 dns-server 192.168.50.10
 domain-name markos.local

! ── Warehouse VLAN Pools ──
ip dhcp excluded-address 172.16.10.1 172.16.10.20
ip dhcp excluded-address 172.16.20.1 172.16.20.20
ip dhcp excluded-address 172.16.30.1 172.16.30.20

ip dhcp pool WAREHOUSE_PC
 network 172.16.10.0 255.255.255.0
 default-router 172.16.10.1
 dns-server 192.168.50.10
 domain-name markos.local

ip dhcp pool WAREHOUSE_WIFI
 network 172.16.20.0 255.255.255.0
 default-router 172.16.20.1
 dns-server 192.168.50.10
 domain-name markos.local

ip dhcp pool WAREHOUSE_PRINTER
 network 172.16.30.0 255.255.255.0
 default-router 172.16.30.1
 dns-server 192.168.50.10
 domain-name markos.local
```

> **Command breakdown:**
> - `ip dhcp excluded-address X Y` — Reserve IPs X through Y. Don't hand these out automatically
> - `ip dhcp pool NAME` — Create a named pool (group of addresses to distribute)
> - `network X.X.X.X mask` — The subnet this pool covers
> - `default-router` — The gateway address sent to every client in this pool
> - `dns-server 192.168.50.10` — All VLANs (even Warehouse!) use the same **internal DNS server**
> - `domain-name markos.local` — Appended to short names, so typing `markos` resolves `markos.local`

---

## 11. DNS — Domain Name System

### What is DNS?
DNS is the **phonebook of the network**. You type `markos.local` and DNS looks up the IP (`192.168.50.10`) for you. Without DNS, you'd memorize IP addresses for everything.

### DNS Records on the Internal Server (`192.168.50.10`)

| Hostname | Record Type | Resolves To | Purpose |
|----------|-------------|-------------|---------|
| `markos.local` | A | 192.168.50.10 | Internal company intranet server |
| `public.markos.local` | A | 192.168.60.10 | Public-facing DMZ website |
| `internet-website.com` | A | 100.1.1.10 | Simulated external Internet server |

> In Packet Tracer, DNS is configured via the **server GUI** (click server → Services tab → DNS). You manually add each record — no CLI needed for the server itself.

> **A Record** = "Address Record" — maps a domain name to an IPv4 address.

---

## 12. NAT/PAT — Internet Access

### What is NAT?
**NAT (Network Address Translation)** translates **private IPs** (`192.168.x.x`, `172.16.x.x`) into the router's **public IP** before sending traffic out to the Internet. Private IPs are not routable on the public Internet — they only exist inside private networks.

**Real-world analogy:** Think of NAT like a company's **receptionist**. All employees (private IPs) send mail through the receptionist (router), who puts the company's public address on every envelope. When replies arrive, the receptionist knows which employee to deliver them to.

**PAT (Port Address Translation)** is the specific variant used here. It uses **port numbers** to track which internal device sent which request, allowing **hundreds of devices to share a single public IP simultaneously.**

### NAT/PAT Configuration (on the Office Router)

```bash
! Step 1: Define which traffic qualifies for NAT translation.
! ACL 1 matches ALL office (192.168.x.x) AND warehouse (172.16.x.x) traffic.
access-list 1 permit 192.168.0.0 0.0.255.255
access-list 1 permit 172.16.0.0 0.0.255.255

! Step 2: Mark every subinterface as NAT "inside" (private/internal side).
interface g0/0
 ip nat inside
interface g0/0.10
 ip nat inside
interface g0/0.20
 ip nat inside
interface g0/0.30
 ip nat inside
interface g0/0.40
 ip nat inside
interface g0/0.50
 ip nat inside
interface g0/0.60
 ip nat inside
interface g0/0.70
 ip nat inside
interface g0/0.99
 ip nat inside

! Step 3: Mark the WAN interface as NAT "outside" (Internet-facing side).
interface s0/1/0
 ip nat outside

! Step 4: Activate PAT — translate matching inside traffic using the outside IP.
ip nat inside source list 1 interface s0/1/0 overload
```

> **Command breakdown:**
> - `access-list 1 permit 192.168.0.0 0.0.255.255` — Standard ACL rule matching any IP in the `192.168.x.x` range
> - `ip nat inside` — Tag this interface as the **private/internal** side
> - `ip nat outside` — Tag this interface as the **public/Internet** side
> - `ip nat inside source list 1 interface s0/1/0 overload`:
>   - `inside source` — Translate the private **source IP** on outgoing packets
>   - `list 1` — Only translate traffic that matches ACL 1
>   - `interface s0/1/0` — Use this interface's IP as the translated public address
>   - `overload` — Enable **PAT** (many private IPs → one public IP, differentiated by port numbers)

> **Why mark all subinterfaces `ip nat inside`?**  
> Even though subinterfaces are virtual, Packet Tracer requires each one to be explicitly tagged. Otherwise NAT won't process traffic coming in on unmarked subinterfaces.

---

## 13. ACLs — Access Control Lists (Security)

### What is an ACL?
An ACL is a **list of permit/deny rules** applied to a router interface. It inspects each packet and either allows or drops it based on the rules.

**Three rules you must never forget:**
1. Rules are evaluated **top to bottom** — the first matching rule wins, the rest are skipped
2. There is an **invisible `deny all`** at the very end — anything not explicitly permitted is dropped
3. ACLs are applied to an interface in either `in` (for packets arriving on this interface) or `out` (for packets leaving this interface) direction

**Standard ACL (1–99):** Filters by **source IP only**  
**Extended ACL (100–199 or named):** Filters by source IP, destination IP, protocol, and port number

---

### Your ACLs — Fully Explained

#### ACL 1 — Standard (Used for NAT, not security)

```
Standard IP access list 1
  10 permit 172.16.0.0 0.0.255.255
  20 permit 192.168.0.0 0.0.255.255
```

This ACL is **only used by NAT** to identify which traffic should be translated for Internet access. It covers both the warehouse (`172.16.x.x`) and office (`192.168.x.x`) ranges.

---

#### ACL 100 — Extended (HR VLAN — applied on `g0/0.10 in`)

```
Extended IP access list 100
  10 permit ip 192.168.10.0 0.0.0.255 192.168.40.0 0.0.0.255
  20 deny ip 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255
  30 deny ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255
  40 permit ip any any
```

| Rule | What It Means |
|------|--------------|
| `10 permit → 192.168.40.0` | ✅ HR **can** reach the Printers VLAN (they need to print!) |
| `20 deny → 192.168.20.0` | ❌ HR **cannot** reach the IT Department VLAN |
| `30 deny → 192.168.30.0` | ❌ HR **cannot** reach the Finance VLAN |
| `40 permit ip any any` | ✅ HR can reach everything else — servers, DMZ, Internet |

> **Why is rule 10 first?** Because ACLs are read top-to-bottom. If `deny IT` came first, packets to printers would never get a chance to be permitted. Always place specific permits before broad denies.

---

#### ACL 101 — Extended (Inter-site traffic isolation)

```
Extended IP access list 101
  10 deny ip 172.16.0.0 0.0.255.255 192.168.0.0 0.0.255.255
  20 permit ip 172.16.0.0 0.0.255.255 any
  30 deny ip 192.168.0.0 0.0.255.255 172.16.0.0 0.0.255.255
  40 permit ip 192.168.0.0 0.0.255.255 any
```

| Rule | What It Means |
|------|--------------|
| `10 deny 172.16.x.x → 192.168.x.x` | ❌ Warehouse **cannot** connect to Office internal networks |
| `20 permit 172.16.x.x → any` | ✅ Warehouse **can** reach the Internet (and everything else) |
| `30 deny 192.168.x.x → 172.16.x.x` | ❌ Office **cannot** connect into Warehouse internal networks |
| `40 permit 192.168.x.x → any` | ✅ Office can reach the Internet |

> This enforces **complete floor isolation** — neither the Office nor the Warehouse can initiate connections into the other's private network.

---

#### ACL 110 — Extended (DMZ VLAN — applied on `g0/0.60 in`)

```
Extended IP access list 110
  10 deny icmp 192.168.60.0 0.0.0.255 192.168.10.0 0.0.0.255 echo
  20 deny icmp 192.168.60.0 0.0.0.255 192.168.30.0 0.0.0.255 echo
  30 permit ip any any
```

| Rule | What It Means |
|------|--------------|
| `10 deny icmp ... echo` | ❌ DMZ Server **cannot ping** (probe) the HR VLAN |
| `20 deny icmp ... echo` | ❌ DMZ Server **cannot ping** (probe) the Finance VLAN |
| `30 permit ip any any` | ✅ Everything else is allowed |

> `icmp echo` = ping requests. This prevents the DMZ server (which faces the Internet and could be compromised) from being used to **discover and scan** sensitive internal VLANs.

---

#### ACL 120 — Extended (Guest WiFi — applied on `g0/0.70 in`)

```
Extended IP access list 120
  10 deny ip 192.168.70.0 0.0.0.255 192.168.0.0 0.0.255.255
  20 permit ip any any
```

| Rule | What It Means |
|------|--------------|
| `10 deny 192.168.70.x → 192.168.x.x` | ❌ Guests **cannot** reach any office internal network |
| `20 permit ip any any` | ✅ Guests **can** reach the Internet (and only the Internet) |

> Classic **Guest WiFi isolation** — visitors connect, browse the web, but have zero access to company servers, HR systems, finance data, or printers.

---

### ACL Security Summary — Who Can Access What?

| From ↓  \ To → | HR | IT | Finance | Printers | Servers | DMZ | Guest | Warehouse | Internet |
|----------------|----|----|---------|----------|---------|-----|-------|-----------|----------|
| **HR** | — | ❌ | ❌ | ✅ | ✅ | ✅ | — | — | ✅ |
| **IT** | ✅ | — | ✅ | ✅ | ✅ | ✅ | — | — | ✅ |
| **Finance** | ✅ | ✅ | — | ✅ | ✅ | ✅ | — | — | ✅ |
| **Guest** | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | — | ❌ | ✅ |
| **DMZ** | ❌(ping) | ✅ | ❌(ping) | ✅ | ✅ | — | — | — | ✅ |
| **Warehouse** | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | — | — | ✅ |

---

## 14. SSH — Secure Remote Management

### What is SSH?
**SSH (Secure Shell)** lets an admin **remotely log in** to a router or switch and run CLI commands over an **encrypted** connection. The older alternative — Telnet — sends everything in plain text, including passwords. SSH is the industry standard for secure remote access.

In this network, SSH is configured on all devices, but should only be used from **Management VLAN 99** (`192.168.99.0/24`).

### SSH Configuration (applied to all routers and switches)

```bash
hostname Office-Router
ip domain-name markos.local
username admin secret admin123
crypto key generate rsa
line vty 0 4
 login local
 transport input ssh
```

> **Command breakdown:**
> - `hostname Office-Router` — Sets the device name; required before generating RSA keys
> - `ip domain-name markos.local` — Combined with hostname to create the unique key identity `Office-Router.markos.local`
> - `username admin secret admin123` — Creates local user `admin`. The word `secret` stores the password as a **one-way hash** (much more secure than `password`)
> - `crypto key generate rsa` — Generates the **RSA key pair** that encrypts SSH sessions. Enter `1024` bits when prompted
> - `line vty 0 4` — Configures 5 virtual terminal slots (5 admins can be logged in simultaneously)
> - `transport input ssh` — **Only SSH is allowed**; Telnet is blocked on these lines
> - `login local` — Authenticate using the local `username/secret` database on this device

### How to Connect via SSH

```bash
! From a PC terminal in the Management VLAN:
ssh -l admin 192.168.99.1
! -l admin     = login username
! 192.168.99.1 = the Office Router's management IP
```

---

## 15. DMZ — Demilitarized Zone

### What is a DMZ?
A **DMZ** is a specially isolated network zone for servers that need to be reachable from the Internet (like a public website), while being **separated from sensitive internal systems**.

If an attacker compromises the DMZ server, they still face a wall before reaching HR data or finance records — because ACL 110 blocks the DMZ from probing internal VLANs.

```
Internet ──→ [DMZ Server: 192.168.60.10]  (HTTP access allowed)
                      │
                      ↓
       Internal VLANs (can access DMZ)
                      
DMZ ──X──→ HR / Finance (pinging blocked by ACL 110)
```

### DMZ in This Project

| Property | Value |
|----------|-------|
| VLAN | 60 |
| Subnet | 192.168.60.0/24 |
| Server IP | 192.168.60.10 (static) |
| Public hostname | `public.markos.local` |
| Protection | ACL 110 — blocks DMZ from pinging HR and Finance |

---

## 16. Services: HTTP, FTP, SYSLOG

All services run on the **Internal Server (`192.168.50.10`)**.

### HTTP — Web Server
Hosts the internal company intranet at `markos.local`. The DMZ server separately hosts `public.markos.local`.

In Packet Tracer: Click the server → **Services → HTTP** → toggle On.

### FTP — File Transfer Protocol
FTP allows users to **upload and download files**. Useful for sharing documents across the company.

In Packet Tracer: Click the server → **Services → FTP** → add username/password.

```bash
! Connect from a PC command prompt:
ftp 192.168.50.10
! Username: admin   Password: admin123
```

### SYSLOG — Centralized Logging
SYSLOG collects **log messages** from all network devices (routers, switches) to one central server. Helps admins spot problems, monitor changes, and audit events.

```bash
! On each router and switch:
Router(config)# logging 192.168.50.10
Router(config)# logging trap informational
```

> - `logging 192.168.50.10` — Forward all log messages to this server IP
> - `logging trap informational` — Log messages at severity level 6 (informational) and above  
>   (Severity scale: 0=Emergency → 7=Debugging. Lower number = more critical)

---

## 17. Wireless Access Points (WiFi)

Three WiFi access points serve different groups:

| Access Point | VLAN | Subnet | Who Uses It | Internet? | Internal Access? |
|-------------|------|--------|-------------|-----------|-----------------|
| HR WiFi (Internal) | 10 | 192.168.10.0/24 | HR Staff | ✅ | ✅ (limited by ACL 100) |
| Guest WiFi | 70 | 192.168.70.0/24 | Visitors | ✅ | ❌ (blocked by ACL 120) |
| Warehouse WiFi | 120 | 172.16.20.0/24 | Warehouse workers | ✅ | ❌ (blocked by ACL 101) |

In Packet Tracer:
1. Place an **Access Point** and connect it to the switch port assigned to the correct VLAN
2. Open the AP → **Config → Port 1** → Set the SSID (WiFi network name)
3. On a laptop: go to **Config → Wireless** → enter the matching SSID to connect

---

## 18. Additional Networking Concepts You Should Know

These are important concepts that your project uses, but that beginners often skip over.

---

### 🔹 The OSI Model — 7 Layers of Networking

Every network communication can be broken into 7 layers. Each layer handles one specific job:

| Layer | Name | What It Does | Example in This Project |
|-------|------|-------------|------------------------|
| 7 | Application | User-facing services | HTTP, FTP, DNS, SSH |
| 6 | Presentation | Data format, encryption | TLS/SSL for HTTPS |
| 5 | Session | Managing connections | Session tracking |
| 4 | Transport | Port numbers, reliable delivery | TCP, UDP; ACL port rules |
| 3 | Network | IP addressing, routing | IP addresses, routing tables, NAT, ACLs |
| 2 | Data Link | MAC addresses, VLANs, switching | 802.1Q tagging, trunk/access ports |
| 1 | Physical | Cables and signals | Ethernet cable, serial WAN link, WiFi |

> Most of what you configured in this project lives at **Layer 2** (VLANs, switching) and **Layer 3** (IP, routing, NAT, ACLs).

---

### 🔹 TCP vs UDP

| Feature | TCP | UDP |
|---------|-----|-----|
| Reliability | ✅ Guaranteed delivery, retransmits lost data | ❌ No guarantee — fire and forget |
| Speed | Slower (more overhead) | Faster |
| Use case | SSH, FTP, HTTP, Email | DNS lookups, Video streaming, VoIP |
| How it starts | Connection-oriented (3-way handshake) | Connectionless |

> SSH uses **TCP** — you need every command to arrive in order and intact.  
> DNS uses **UDP** — fast one-shot queries. If it fails, just retry.

---

### 🔹 ICMP — The Protocol Behind `ping`

**ICMP (Internet Control Message Protocol)** is used purely for network diagnostics. The `ping` command sends ICMP Echo Requests and listens for Echo Replies.

In ACL 110, you specifically block `icmp echo` from the DMZ to internal VLANs — this is a real-world security practice because ping can be used to **discover and enumerate internal hosts** on a network.

---

### 🔹 802.1Q VLAN Tagging — How Trunk Links Work

When a switch sends a frame down a **trunk** port, it inserts a **4-byte tag** into the Ethernet frame containing the VLAN ID. The router reads this tag via `encapsulation dot1Q` and knows which subinterface to process the frame on.

```
Normal Ethernet frame:   [Dest MAC | Src MAC | Type | Data]
802.1Q Tagged frame:     [Dest MAC | Src MAC | 802.1Q Tag (VLAN=10) | Type | Data]
```

> This is why `encapsulation dot1Q 10` is required on every subinterface — it tells the router "frames tagged with VLAN 10 belong to this subinterface."

---

### 🔹 Administrative Distance (AD)

When a router learns about the same destination from multiple sources, it picks the **most trustworthy** one using Administrative Distance (lower = more trusted):

| Route Type | AD |
|------------|-----|
| Connected (C) | 0 |
| Static (S) | 1 |
| OSPF | 110 |
| RIP | 120 |

That's why you see `[1/0]` next to your static routes — AD of `1` (very trusted), metric of `0`.

---

### 🔹 `secret` vs `password` — Why It Matters

```bash
username admin secret admin123    ! Stored as a SHA hash — SECURE (use this)
username admin password admin123  ! Stored in plain text — INSECURE (avoid)
enable secret admin123            ! Hashed — SECURE
enable password admin123          ! Plain text — INSECURE
```

With `password`, anyone who runs `show running-config` can read the password directly. With `secret`, they only see a hash that cannot be reversed.

---

### 🔹 Running Config vs Startup Config

Cisco devices have two separate configs:

| Config | Location | Survives Reboot? |
|--------|----------|-----------------|
| **Running config** | RAM (live memory) | ❌ Lost on reboot |
| **Startup config** | NVRAM (flash storage) | ✅ Persists forever |

This is why **always saving** is critical:
```bash
copy running-config startup-config
```
If you forget to save and the device reboots, all your work is gone.

---

### 🔹 Interface Naming on Cisco Devices

| Full Name | Short Form | Meaning |
|-----------|------------|---------|
| `GigabitEthernet0/0` | `g0/0` | Gigabit port 0 on module 0 (router) |
| `GigabitEthernet0/0.10` | `g0/0.10` | Subinterface 10 on G0/0 |
| `Serial0/1/0` | `s0/1/0` | Serial (WAN) port (module/slot/port) |
| `GigabitEthernet1/0/1` | `g1/0/1` | Switch port (stack/module/port) |
| `FastEthernet0/1` | `f0/1` | Fast Ethernet port (older devices) |

---

## 19. Complete Command Reference

Every command used in this project, grouped by category, with a plain-English explanation.

---

### Basic Navigation

| Command | What It Does |
|---------|-------------|
| `enable` | Enter **Privileged EXEC** mode (prompt changes to `#`) |
| `configure terminal` | Enter **Global Configuration** mode to make changes |
| `exit` | Go back one level in the CLI |
| `end` | Jump directly back to Privileged EXEC mode from anywhere |
| `do show ...` | Run a `show` command while inside config mode |

---

### Saving & Viewing Config

| Command | What It Does |
|---------|-------------|
| `copy running-config startup-config` | **Save** current config permanently |
| `show running-config` | View the full active configuration |
| `show startup-config` | View the saved (boot) configuration |
| `show version` | See device model, IOS version, and uptime |

---

### VLAN & Switch Commands

| Command | What It Does |
|---------|-------------|
| `vlan 10` | Create VLAN 10 and enter its config sub-mode |
| `name HR` | Assign a name to the VLAN |
| `switchport mode access` | Set port to access mode — carries one VLAN only (for PCs) |
| `switchport access vlan 10` | Assign this access port to VLAN 10 |
| `switchport mode trunk` | Set port to trunk mode — carries all VLANs tagged (for router link) |
| `interface range g1/0/3-6` | Configure ports 3 through 6 simultaneously |
| `show vlan brief` | List all VLANs and their assigned ports |

---

### Interface & IP Commands (Router)

| Command | What It Does |
|---------|-------------|
| `interface g0/0.10` | Create/enter subinterface `.10` on physical port `g0/0` |
| `encapsulation dot1Q 10` | Listen for VLAN 10 tagged frames (802.1Q standard) |
| `ip address 192.168.10.1 255.255.255.0` | Assign an IP and subnet mask to this interface |
| `no shutdown` | Turn the interface **ON** |
| `shutdown` | Turn the interface **OFF** |
| `show ip interface brief` | Summary table of all interfaces, their IPs, and up/down status |

---

### Routing Commands

| Command | What It Does |
|---------|-------------|
| `ip route 0.0.0.0 0.0.0.0 200.1.1.1` | Default route — send all unknown traffic to `200.1.1.1` |
| `ip route 192.168.0.0 255.255.0.0 200.1.1.2` | Static route to the 192.168.0.0/16 network via `200.1.1.2` |
| `show ip route` | Display the full routing table with all route types |

---

### DHCP Commands

| Command | What It Does |
|---------|-------------|
| `ip dhcp excluded-address 192.168.10.1 192.168.10.20` | Reserve this range — do not hand out automatically |
| `ip dhcp pool HR` | Create a DHCP address pool named "HR" |
| `network 192.168.10.0 255.255.255.0` | The subnet this pool serves |
| `default-router 192.168.10.1` | Gateway address sent to all clients in this pool |
| `dns-server 192.168.50.10` | DNS server address sent to clients |
| `domain-name markos.local` | Domain suffix appended to hostnames |
| `show ip dhcp binding` | See all IP-to-MAC address assignments currently active |
| `show ip dhcp pool` | See pool statistics (total, used, available addresses) |
| `show ip dhcp conflict` | See if any IP conflicts were detected |

---

### NAT/PAT Commands

| Command | What It Does |
|---------|-------------|
| `access-list 1 permit 192.168.0.0 0.0.255.255` | Standard ACL: match the `192.168.x.x` range (used by NAT) |
| `ip nat inside` | Mark this interface as the **private/internal** side |
| `ip nat outside` | Mark this interface as the **public/Internet** side |
| `ip nat inside source list 1 interface s0/1/0 overload` | Enable PAT: translate ACL 1 traffic using the serial IP |
| `show ip nat translations` | View active NAT table (private IP:port ↔ public IP:port) |
| `show ip nat statistics` | See total translation counts and interface assignments |

---

### ACL Commands

| Command | What It Does |
|---------|-------------|
| `access-list 100 permit ip src dst` | Add a rule to numbered extended ACL 100 |
| `ip access-list extended NAME` | Create or enter a named extended ACL |
| `permit ip 192.168.10.0 0.0.0.255 192.168.40.0 0.0.0.255` | Allow traffic from HR subnet to Printers subnet |
| `deny ip 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255` | Block traffic from HR subnet to IT subnet |
| `deny icmp 192.168.60.0 0.0.0.255 192.168.10.0 0.0.0.255 echo` | Block ping (ICMP Echo) from DMZ to HR |
| `permit ip any any` | Allow all traffic (used as catch-all at the end) |
| `ip access-group 100 in` | Apply ACL 100 to **incoming** traffic on this interface |
| `ip access-group 100 out` | Apply ACL 100 to **outgoing** traffic on this interface |
| `show access-lists` | View all ACLs and match hit counters |

---

### SSH Commands

| Command | What It Does |
|---------|-------------|
| `hostname Office-Router` | Set the device name (required for crypto key generation) |
| `ip domain-name markos.local` | Set the domain name (required for crypto key generation) |
| `username admin secret admin123` | Create a local user with a securely hashed password |
| `crypto key generate rsa` | Generate RSA key pair for SSH encryption (use 1024 bits) |
| `line vty 0 4` | Configure 5 virtual terminal (remote login) lines |
| `transport input ssh` | Permit SSH only; block Telnet on VTY lines |
| `login local` | Use local username/password database for authentication |
| `show ssh` | View active SSH sessions and version info |
| `ssh -l admin 192.168.99.1` | Connect to a device via SSH (run from PC terminal) |

---

### Logging Commands

| Command | What It Does |
|---------|-------------|
| `logging 192.168.50.10` | Send syslog messages to the server at this IP |
| `logging trap informational` | Set the log level to informational (level 6) and above |

---

### 🛠️ Troubleshooting & Verification Commands

| Command | What It Does |
|---------|-------------|
| `ping 192.168.20.1` | Test basic reachability — sends ICMP echo requests |
| `traceroute 100.1.1.10` | Show the hop-by-hop path packets take to a destination |
| `show vlan brief` | Confirm VLAN assignments and port membership |
| `show ip interface brief` | Check all interface IPs and up/down state at a glance |
| `show ip route` | Read the complete routing table |
| `show ip dhcp binding` | Verify DHCP is assigning IPs correctly |
| `show ip nat translations` | Confirm NAT/PAT is working |
| `show access-lists` | Check ACL rules and see how many packets each has matched |
| `show running-config` | View the full live configuration |

---

## 20. How to Upload This Project to GitHub

### Step 1: Install Git
Download from [https://git-scm.com](https://git-scm.com) and install with default settings.

### Step 2: Create a GitHub Account
Go to [https://github.com](https://github.com) and sign up (free).

### Step 3: Create a New Repository

1. Click **"+"** → **New repository**
2. Name: `enterprise-network-packet-tracer`
3. Description: `Enterprise network with VLANs, NAT, ACLs, DHCP, SSH and DMZ — built in Cisco Packet Tracer`
4. Set to **Public**
5. Check **"Add a README file"**
6. Click **Create repository**

### Step 4: Organize Your Files

```
Basic-Network-Architecture/
│
├── Enterprise-network.pkt
├── README.md
├── enterprise-network-diagram.png
├── configurations/
│   ├── office-router-config.txt
│   ├── warehouse-router-config.txt
│   ├── office-switch-config.txt
│   └── warehouse-switch-config.txt
│
├── documentation/
│   └── Network_Guide.docx
```

### Step 5: Export Your Configs from Packet Tracer

On each device:
```bash
Router# show running-config
```
Copy the output and paste it into the matching `.txt` file.

### Step 6: Push to GitHub

Open a terminal inside your project folder and run:

```bash
git init
git add .
git commit -m "Initial commit: Enterprise network Packet Tracer project"
git remote add origin https://github.com/YOUR_USERNAME/enterprise-network-packet-tracer.git
git branch -M main
git push -u origin main
```

> Replace `YOUR_USERNAME` with your actual GitHub username. After running `git push`, refresh your GitHub page — your files will appear there.

---

## 21. Summary — What You've Learned

By building and understanding this project end-to-end, you've practiced real enterprise networking skills:

| Concept | What You Implemented |
|---------|---------------------|
| **VLANs** | 11 isolated VLANs across two floors |
| **Subnetting** | /24 for LANs, /30 for point-to-point WAN links |
| **Trunk & Access Ports** | Connected switches to routers and devices to correct VLANs |
| **Router-on-a-Stick** | Subinterfaces + dot1Q encapsulation for inter-VLAN routing |
| **Static Routing** | ISP routes, inter-site summary routes, default routes |
| **Routing Table** | Read and understood C, L, S, S* route types |
| **DHCP** | 10 named pools serving all office and warehouse VLANs |
| **DNS** | Internal name resolution for 3 hostnames |
| **NAT/PAT** | All private devices share one public IP via overload |
| **ACLs** | 4 extended ACLs enforcing HR, Guest, DMZ, and site isolation |
| **SSH** | Encrypted remote management on all routers and switches |
| **DMZ** | Public web server isolated from internal VLANs |
| **SYSLOG** | Centralized logging to the internal server |
| **HTTP / FTP** | Internal intranet and file transfer services |
| **WiFi** | Three access points for HR, Guests, and Warehouse |

---

*Built with ❤️ using Cisco Packet Tracer — a beginner's journey into enterprise networking.*
