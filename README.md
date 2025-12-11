# CCNA – Basic Router Configuration (Module 10)

Quick-reference notes for NetAcad CCNA – **Module 10: Basic Router Configuration**.  
Focus is on bringing a Cisco IOS router from factory-default to a **secure, working L3 device**:
initial setup, interface configuration, default gateways, and verification.

I use this repo to revise:

- The main **IOS modes** and the first steps to secure a new router.
- How to **configure hostnames, passwords, banners, and save configs**.
- How to **configure IPv4 and IPv6 addresses** on router interfaces.
- How to **verify interfaces and routing** with core `show` commands.
- How to configure and verify **default gateways** on hosts and switches.
- Typical **misconfigurations and troubleshooting patterns** (down interfaces, wrong gateway, etc.).

> **Suggested repo name:**  
> `ccna-basic-router-configuration-cheat-sheet-2025`

---

## Table of Contents

- [Module 10 Overview](#module-10-overview)  
- [Module Map](#module-map)  

- [10.0 Introduction](#100-introduction)  
  - [10.0.1 Why should I take this module?](#1001-why-should-i-take-this-module)  
  - [10.0.2 What will I learn to do in this module?](#1002-what-will-i-learn-to-do-in-this-module)  

- [10.1 Configure Initial Router Settings](#101-configure-initial-router-settings)  
  - [10.1.1 Basic Router Configuration Steps](#1011-basic-router-configuration-steps)  
  - [10.1.2 Basic Router Configuration Example](#1012-basic-router-configuration-example)  
  - [10.1.3 Syntax Checker – Configure Initial Router Settings](#1013-syntax-checker--configure-initial-router-settings)  
  - [10.1.4 Packet Tracer – Configure Initial Router Settings](#1014-packet-tracer--configure-initial-router-settings)  

- [10.2 Configure Interfaces](#102-configure-interfaces)  
  - [10.2.1 Configure Router Interfaces](#1021-configure-router-interfaces)  
  - [10.2.2 Configure Router Interfaces Example](#1022-configure-router-interfaces-example)  
  - [10.2.3 Verify Interface Configuration](#1023-verify-interface-configuration)  
  - [10.2.4 Configuration Verification Commands](#1024-configuration-verification-commands)  

- [10.3 Configure the Default Gateway](#103-configure-the-default-gateway)  
  - [10.3.1 Default Gateway on a Host](#1031-default-gateway-on-a-host)  
  - [10.3.2 Default Gateway on a Switch](#1032-default-gateway-on-a-switch)  
  - [10.3.3 Syntax Checker – Configure the Default Gateway](#1033-syntax-checker--configure-the-default-gateway)  
  - [10.3.4 Packet Tracer – Connect a Router to a LAN](#1034-packet-tracer--connect-a-router-to-a-lan)  
  - [10.3.5 Packet Tracer – Troubleshoot Default Gateway Issues](#1035-packet-tracer--troubleshoot-default-gateway-issues)  

- [10.4 Module Practice and Quiz](#104-module-practice-and-quiz)  
  - [10.4.1 Video – Network Device Differences – Part 1](#1041-video--network-device-differences--part-1)  
  - [10.4.2 Video – Network Device Differences – Part 2](#1042-video--network-device-differences--part-2)  
  - [10.4.3 Lab – Build a Switch and Router Network](#1043-lab--build-a-switch-and-router-network)  
  - [10.4.4 What did I learn in this module?](#1044-what-did-i-learn-in-this-module)  
  - [10.4.5 Module Quiz – Basic Router Configuration](#1045-module-quiz--basic-router-configuration)  

---

## Module 10 Overview

> **Goal:** Be able to take a brand-new Cisco IOS router, apply a **secure baseline
> configuration**, bring the right interfaces **up** with IPv4/IPv6 addresses, and make
> end devices communicate using **correct default gateways**.

---

## Module Map

| Topic                                  | Focus                                                                 |
|----------------------------------------|-----------------------------------------------------------------------|
| 10.1 Configure Initial Router Settings | Hostname, passwords, banner, encryption, saving config               |
| 10.2 Configure Interfaces              | IPv4/IPv6 addressing, descriptions, `no shutdown`, basic verification|
| 10.3 Configure the Default Gateway     | Default gateway on hosts and switches, troubleshooting               |
| 10.4 Practice and Quiz                 | Device comparison, building a small routed network, final review     |

---

## 10.0 Introduction

### 10.0.1 Why should I take this module?

Routing packets is like a **relay race**:

- Each router is a runner that forwards the packet to the next hop.
- If the first runner never gets configured (no IPs, no routes), the race never really starts.

Routers ship with a very basic default config.  
Before they can route or even be remotely managed, you must:

- Secure access (console, vty, privileged EXEC).
- Give the device an identity (hostname, banner).
- Configure and **bring up** interfaces.
- Save the configuration so it survives a reload.

This module walks through that entire initial-setup process step by step, with
Syntax Checker and Packet Tracer activities so you can drill the commands.

### 10.0.2 What will I learn to do in this module?

| Topic Title                        | Topic Objective                                             |
|-----------------------------------|-------------------------------------------------------------|
| Configure Initial Router Settings | Configure initial settings on an IOS Cisco router.         |
| Configure Interfaces              | Configure two active interfaces on an IOS router.          |
| Configure the Default Gateway     | Configure devices to use the correct default gateway.      |

---

## 10.1 Configure Initial Router Settings

### 10.1.1 Basic Router Configuration Steps

**Baseline tasks when you touch a brand-new router:**

1. **Configure the hostname**

```text
Router(config)# hostname R1
```

2. **Secure privileged EXEC mode**

```text
R1(config)# enable secret <strong-secret>
```

3. **Secure local (console) user EXEC**

```text
R1(config)# line console 0
R1(config-line)# password <console-pass>
R1(config-line)# login
```

4. **Secure remote Telnet / SSH (vty) access**

```text
R1(config)# line vty 0 4
R1(config-line)# password <vty-pass>
R1(config-line)# login
R1(config-line)# transport input ssh telnet
```

> In production, prefer **SSH only**: `transport input ssh`.

5. **Encrypt all plain-text line passwords**

```text
R1(config)# service password-encryption
```

6. **Provide legal notification (MOTD banner)**

```text
R1(config)# banner motd #Authorized Access Only!#
```

7. **Save the configuration to NVRAM**

```text
R1(config)# end
R1# copy running-config startup-config
Destination filename [startup-config]?  <ENTER>
```

**Key ideas**

- `enable secret` overrides `enable password` and is stored as a hash.
- `service password-encryption` converts existing and future line passwords
  into weakly encrypted form – just enough to hide them from casual viewing.
- The MOTD banner is your **legal warning** for unauthorized users.

---

### 10.1.2 Basic Router Configuration Example

**Topology (simplified)**

- **PC1**
  - IPv4: `192.168.10.10/24`
  - IPv6: `2001:db8:acad:10::10/64`
- **R1 G0/0/0** (towards PC1 LAN)
  - IPv4: `192.168.10.1/24`
  - IPv6: `2001:db8:acad:10::1/64`
- **R1 G0/0/1** (towards R2)
  - IPv4: `209.165.200.225/30`
  - IPv6: `2001:db8:feed:224::1/64`
- **R2 G0/0/1** (towards R1)
  - IPv4: `209.165.200.226/30`
  - IPv6: `2001:db8:feed:224::2/64`
- **R2 LAN toward PC2**
  - R2: IPv4 `10.1.1.1/24`, IPv6 `2001:db8:cafe:1::1/64`
  - PC2: IPv4 `10.1.1.10/24`, IPv6 `2001:db8:cafe:1::10/64`

**Sample initial configuration for R1**

```text
Router> enable
Router# configure terminal
Router(config)# hostname R1

! Secure modes
R1(config)# enable secret class

! Console access
R1(config)# line console 0
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit

! VTY (Telnet/SSH)
R1(config)# line vty 0 4
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# transport input ssh telnet
R1(config-line)# exit

! Encrypt line passwords
R1(config)# service password-encryption

! MOTD banner
R1(config)# banner motd #
***********************************************
WARNING: Unauthorized access is prohibited!
***********************************************
#
R1(config)# end

! Save to NVRAM
R1# copy running-config startup-config
```

---

### 10.1.3 Syntax Checker – Configure Initial Router Settings

**Practice task (summary)**

You are asked to:

1. Set the hostname to **R1**.
2. Configure `class` as the **secret** password.
3. Configure console password **cisco** and require login.
4. Configure vty `0 4` password **cisco**, allow SSH and Telnet, require login.
5. Run `service password-encryption`.
6. Configure a MOTD banner: **Authorized Access Only!**
7. Exit back to privileged EXEC.

**Command sequence (solution)**

```text
Router> enable
Router# configure terminal
Router(config)# hostname R1

R1(config)# enable secret class

R1(config)# line console 0
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit

R1(config)# line vty 0 4
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# transport input ssh telnet
R1(config-line)# exit

R1(config)# service password-encryption
R1(config)# banner motd #Authorized Access Only!#
R1(config)# exit
R1#
```

**Concept checks**

- If you forget `login` under `line console 0`, the console lets you in **without**
  prompting for a password.
- After you set `enable secret` and leave `enable password` configured, only the
  **secret** works for entering privileged EXEC.
- Once `service password-encryption` is enabled, all future line passwords
  appear as type-7 encrypted strings in `show running-config`.

---

### 10.1.4 Packet Tracer – Configure Initial Router Settings

Packet Tracer activity objectives:

- **Part 1 – Verify default config**
  - Connect via console.
  - Use `show running-config` to view the default.
  - Use `show startup-config` and see the message  
    `startup-config is not present` (no config saved in NVRAM yet).

- **Part 2 – Configure and verify**
  - Change hostname to `R1`.
  - Set MOTD banner (e.g. *Unauthorized access is strictly prohibited.*).
  - Configure and encrypt passwords.
  - Verify with `show running-config`.

- **Part 3 – Save running configuration**
  - Save with `copy running-config startup-config`.
  - (Optional) Back up startup config to flash: `copy startup-config flash`.

**Typical short-answer notes**

- **Why `startup-config is not present`?**  
  Because the router has never had its running configuration saved to NVRAM.

- **Why have a MOTD banner on every router?**  
  To display a legal warning / acceptable-use notice to all users who connect.

- **Command to view the current configuration?**  
  `show running-config`.

- **Command to save running config to NVRAM?**  
  `copy running-config startup-config` (short form: `copy run start`).

---

## 10.2 Configure Interfaces

### 10.2.1 Configure Router Interfaces

Core steps to configure an L3 interface:

```text
Router(config)# interface <type> <slot/port>
Router(config-if)# description <description-text>
Router(config-if)# ip address <ipv4-address> <subnet-mask>
Router(config-if)# ipv6 address <ipv6-address>/<prefix-length>
Router(config-if)# no shutdown
```

Notes:

- `description` is optional but **strongly recommended** (helps troubleshooting).
- `no shutdown` is required to **bring the interface up**.
- On point-to-point links (router–router without a switch), **both** interfaces
  must be configured and enabled.

---

### 10.2.2 Configure Router Interfaces Example

Using the same topology, R1’s directly connected interfaces are configured as:

```text
R1> enable
R1# configure terminal

! LAN towards PC1
R1(config)# interface gigabitEthernet 0/0/0
R1(config-if)# description Link to LAN
R1(config-if)# ip address 192.168.10.1 255.255.255.0
R1(config-if)# ipv6 address 2001:db8:acad:10::1/64
R1(config-if)# no shutdown
R1(config-if)# exit

! Link to R2
R1(config)# interface gigabitEthernet 0/0/1
R1(config-if)# description Link to R2
R1(config-if)# ip address 209.165.200.225 255.255.255.252
R1(config-if)# ipv6 address 2001:db8:feed:224::1/64
R1(config-if)# no shutdown
R1(config-if)# exit
```

Informational messages like `%LINK-3-UPDOWN` and `%LINEPROTO-5-UPDOWN` confirm
that the physical interface and line protocol have changed to **up**.

---

### 10.2.3 Verify Interface Configuration

Fast way to check addresses and basic status:

```text
R1# show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0/0   192.168.10.1    YES manual up                    up
GigabitEthernet0/0/1   209.165.200.225 YES manual up                    up
Vlan1                  unassigned      YES unset  administratively down down

R1# show ipv6 interface brief
GigabitEthernet0/0/0       [up/up]
    FE80::201:C9FF:FE89:4501
    2001:DB8:ACAD:10::1
GigabitEthernet0/0/1       [up/up]
    FE80::201:C9FF:FE89:4502
    2001:DB8:FEED:224::1
Vlan1                      [administratively down/down]
    unassigned
```

Interpretation:

- Both G0/0/0 and G0/0/1 are **up/up** with correct IPv4 and IPv6 addresses.
- VLAN1 exists but is **administratively down** and has no IP – normal on a router.

---

### 10.2.4 Configuration Verification Commands

**Goal:** Use core *show* commands to verify interface status, IP addressing and routing on R1.

---

#### Quick command summary

| Command                      | What it shows (IPv4/IPv6)                                                                 |
|-----------------------------|-------------------------------------------------------------------------------------------|
| `show ip interface brief`   | All interfaces, IPv4 addresses, and **Status/Protocol** (up/down/admin down).            |
| `show ipv6 interface brief` | All interfaces, IPv6 link-local + global addresses, and **[Status/Protocol]**.           |
| `show ip route`             | IPv4 routing table: connected networks, local host routes, static/dynamic routes.        |
| `show ipv6 route`           | IPv6 routing table: connected prefixes, local routes, special routes (e.g. `FF00::/8`).  |
| `show interfaces g0/0/0`    | Detailed Layer 1–2 stats for the interface (errors, throughput, duplex, bandwidth…).     |
| `show ip interface g0/0/0`  | IPv4-specific info for the interface (address, broadcasts, ACLs, helper, CEF, etc.).     |
| `show ipv6 interface g0/0/0`| IPv6-specific info (link-local, global unicast, joined multicast groups, ND timers…).     |

---

#### Example – `show ip interface brief`

```text
R1# show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0/0   192.168.10.1    YES manual up                    up
GigabitEthernet0/0/1   209.165.200.225 YES manual up                    up
Vlan1                  unassigned      YES unset  administratively down down
R1#
```

**Key checks**

- Correct IPv4 addresses on each interface.
- For working links you want **`Status up`** and **`Protocol up`**.
- `administratively down` = interface is shut down (needs `no shutdown`).

---

#### Example – `show ipv6 interface brief`

```text
R1# show ipv6 interface brief
GigabitEthernet0/0/0       [up/up]
    FE80::201:C9FF:FE89:4501
    2001:DB8:ACAD:10::1
GigabitEthernet0/0/1       [up/up]
    FE80::201:C9FF:FE89:4502
    2001:DB8:FEED:224::1
Vlan1                      [administratively down/down]
    unassigned
R1#
```

**Key checks**

- Every active interface must have a **link-local** and usually a **global unicast** address.
- State `[up/up]` confirms both line and protocol are up.

---

#### Example – `show ip route`

```text
R1# show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
      ...
Gateway of last resort is not set

     192.168.10.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.10.0/24 is directly connected, GigabitEthernet0/0/0
L       192.168.10.1/32 is directly connected, GigabitEthernet0/0/0
     209.165.200.0/24 is variably subnetted, 2 subnets, 2 masks
C       209.165.200.224/30 is directly connected, GigabitEthernet0/0/1
L       209.165.200.225/32 is directly connected, GigabitEthernet0/0/1
R1#
```

**Key points**

- `C` = directly connected network via an interface.
- `L` = local host route (router’s own IP on that interface).
- No default route yet: *Gateway of last resort is not set*.

---

#### Example – `show ipv6 route`

```text
R1# show ipv6 route
IPv6 Routing Table - default - 5 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
      ...
C   2001:DB8:ACAD:10::/64 [0/0]
     via GigabitEthernet0/0/0, directly connected
L   2001:DB8:ACAD:10::1/128 [0/0]
     via GigabitEthernet0/0/0, receive
C   2001:DB8:FEED:224::/64 [0/0]
     via GigabitEthernet0/0/1, directly connected
L   2001:DB8:FEED:224::1/128 [0/0]
     via GigabitEthernet0/0/1, receive
L   FF00::/8 [0/0]
     via Null0, receive
R1#
```

**Key points**

- Same logic as IPv4: `C` connected IPv6 prefixes, `L` local addresses.
- `FF00::/8` is the IPv6 multicast range.

---

#### Example – `show interfaces g0/0/0`

```text
R1# show interfaces gig0/0/0
GigabitEthernet0/0/0 is up, line protocol is up
  Hardware is ISR4321-2x1GE, address is a0e0.af0d.e140 (bia a0e0.af0d.e140)
  Description: Link to LAN
  Internet address is 192.168.10.1/24
  MTU 1500 bytes, BW 100000 Kbit/sec, DLY 100 usec,
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation ARPA, loopback not set
  Full Duplex, 100Mbps, media type is RJ45
  ...
  1180 packets input, 109486 bytes, 0 no buffer
  65 packets output, 22292 bytes, 0 underruns
  0 input errors, 0 output errors
R1#
```

**Key checks**

- Interface **description**, **MTU**, **bandwidth**, duplex/speed.
- Error counters (input/output errors, CRCs, collisions) should be **0** in a clean lab.

---

#### Example – `show ip interface g0/0/0`

```text
R1# show ip interface g0/0/0
GigabitEthernet0/0/0 is up, line protocol is up
  Internet address is 192.168.10.1/24
  Broadcast address is 255.255.255.255
  ...
  IP fast switching is enabled
  IP CEF switching is enabled
  ...
  IPv4 WCCP Redirect outbound is disabled
R1#
```

**Key checks**

- Correct IPv4 address and mask.
- Whether input/output ACLs, helper addresses, NAT, etc. are configured (here they are not).
- CEF/fast switching normally enabled on modern IOS.

---

#### Example – `show ipv6 interface g0/0/0`

```text
R1# show ipv6 interface g0/0/0
GigabitEthernet0/0/0 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::868A:8DFF:FE44:49B0
  Description: Link to LAN
  Global unicast address(es):
    2001:DB8:ACAD:10::1, subnet is 2001:DB8:ACAD:10::/64
  Joined group address(es):
    FF02::1
    FF02::1:FF00:1
    FF02::1:FF44:49B0
  MTU is 1500 bytes
  ICMP redirects are enabled
  ND reachable time is 30000 milliseconds
R1#
```

**Key checks**

- IPv6 enabled + correct global unicast.
- The interface has joined standard IPv6 multicast groups (`FF02::1`, solicited-node multicast).
- ND timers and ICMP settings are normal unless you are tuning them.

**Exam-style takeaway:**  
For quick troubleshooting you usually start with **`show ip interface brief`** /
**`show ipv6 interface brief`**, then go deeper with **`show interfaces`** and the
per-protocol `show ip/ipv6 interface` commands.

---

### 10.2.5 Syntax Checker – Configure Interfaces

**Goal:** Practise configuring G0/0/0 with IPv4 + IPv6, description, and enabling it.

**Required configuration for G0/0/0**

- Description: `Link to LAN`
- IPv4 address: `192.168.10.1 255.255.255.0`
- IPv6 address: `2001:db8:acad:10::1/64`
- Administrative state: `no shutdown`

```text
R1# configure terminal
R1(config)# interface gigabitethernet0/0/0
R1(config-if)# description Link to LAN
R1(config-if)# ip address 192.168.10.1 255.255.255.0
R1(config-if)# ipv6 address 2001:db8:acad:10::1/64
R1(config-if)# no shutdown
*Aug  1 01:43:53.435: %LINK-3-UPDOWN: Interface GigabitEthernet0/0/0, changed state to down
*Aug  1 01:43:56.447: %LINK-3-UPDOWN: Interface GigabitEthernet0/0/0, changed state to up
*Aug  1 01:43:57.447: %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/0, changed state to up
R1(config-if)# exit
R1#
```

**Checklist**

- After `no shutdown`, you should see link up / line protocol up messages.
- `show ip interface brief` and `show ipv6 interface brief` should now list the
  configured addresses and show `up/up`.

---

## 10.3 Configure the Default Gateway

**Big idea:**  
A **default gateway** is the router interface address that a device uses to reach
**remote networks** (anything not on its own subnet).

---

### 10.3.1 Default Gateway on a Host

- Hosts need three basic IPv4 settings:
  - **IP address**
  - **Subnet mask**
  - **Default gateway** (router interface on the same LAN)

- If the **destination is on the same network**:
  - Host sends the frame directly to the destination MAC (no gateway used).

- If the **destination is on a different network**:
  - Host forwards the packet to the **default gateway MAC**.
  - Router consults its routing table and forwards the packet out the correct exit interface.

- Same logic for IPv6:
  - Hosts use the router’s **IPv6 address** on the local link as their default gateway.

> Exam hint: The default gateway must be in the **same subnet** as the host;
> otherwise the host can’t ARP/ND for it.

---

### 10.3.2 Default Gateway on a Switch

- A pure Layer 2 switch does **not** need an IP address to switch frames,
  but we configure one so we can **manage it remotely**.

- Management is done on a **Switch Virtual Interface (SVI)**, usually `Vlan1`:
  - Assign IPv4 address + mask to the SVI.
  - Configure a **default gateway** so the switch can reply to management traffic from other networks.

**Example – SVI + default gateway on S1**

```text
S1(config)# interface vlan 1
S1(config-if)# ip address 192.168.10.50 255.255.255.0
S1(config-if)# no shutdown
S1(config-if)# exit
S1(config)# ip default-gateway 192.168.10.1
```

- Here:
  - S1’s management IP is `192.168.10.50/24`.
  - Default gateway is the router’s G0/0/0 interface: `192.168.10.1`.

- For IPv6:
  - The switch can have an IPv6 address on the SVI.
  - The default gateway is learned automatically from **ICMPv6 Router Advertisements**, so we usually don’t manually configure `ipv6 default-gateway`.

---

### 10.3.3 Syntax Checker – Configure the Default Gateway

**Task:** Configure `192.168.10.1` as the default gateway for switch S1.

```text
S1# configure terminal
S1(config)# ip default-gateway 192.168.10.1
S1(config)# end
S1#
```

**Verification:**  
Use `show running-config` or `show ip route` (on multilayer switches) to confirm
the default gateway line is present.

---

### 10.3.4 Packet Tracer – Connect a Router to a LAN

**What you practise in this PT:**

1. Use **show commands** on a router:
   - `show ip interface brief`
   - `show ipv6 interface brief`
   - `show ip route`
   - `show ipv6 route`
2. Configure router Ethernet interfaces using the **Addressing Table**:
   - Set IPv4 / IPv6 addresses.
   - Add descriptions.
   - `no shutdown` each active interface.
3. Verify and test:
   - Ping between PCs and the router.
   - Check that routes exist for all directly connected networks.

**Mini-checklist before finishing the activity**

- All router interfaces are `up/up` with the correct addressing.
- PCs have correct IP, mask, and default gateway.
- End-to-end pings (e.g., PC1 → PC2) succeed.

---

### 10.3.5 Packet Tracer – Troubleshoot Default Gateway Issues

**Scenario:**  
You’re given a partially documented network. Some hosts cannot reach remote networks.

**Structured troubleshooting approach (from the activity):**

1. **Verify documentation**  
   - Compare diagrams/addressing tables to actual device configs:
     - `ipconfig` / `ip a` on hosts.
     - `show ip interface brief`, `show ipv6 interface brief` on routers/switches.

2. **Isolate the problem with tests**
   - Ping:
     - Host → its own IP (local stack).
     - Host → default gateway.
     - Host → remote host.
   - If ping to the gateway fails, check:
     - Host default gateway setting.
     - Router interface status and addressing.
   - If gateway ping works but remote ping fails:
     - Check routing on routers, or default gateway on intermediate devices.

3. **Determine and implement the fix**
   - Common issues:
     - Wrong **default gateway** on host or switch.
     - Router interface shut down or wrong IP.
     - Missing route / default route.

4. **Retest**
   - Re-run pings and, if needed, `tracert` / `traceroute` to confirm connectivity.

5. **Document the solution**
   - Note misconfigurations and final working settings for future reference.

**Exam-style takeaway:**  
If a host can reach local devices but **not remote networks**, always check the
**default gateway configuration** first (on both the host and the router
interface that should be acting as the gateway).


---

## 10.4 Module Practice and Quiz

This section is a wrap-up of **Module 10 – Basic Router Configuration**.  
Use it to quickly review hardware differences, core CLI commands, and the
concepts checked in the module quiz.

---

### 10.4.1 Video – Network Device Differences – Part 1

**Focus:** Physical ports and modules on Cisco routers used in NetAcad labs.

**Key devices shown**

- **Cisco 4000 Series ISR (e.g., 4321, 4221)**
  - Two **integrated Gigabit Ethernet ports**:
    - `GigabitEthernet0/0/0`
    - `GigabitEthernet0/0/1`
  - Some Gigabit ports are **shared with an SFP slot**:
    - Yellow line on chassis = same logical interface (copper *or* SFP).
    - Only one side (copper or SFP) can be active at a time.
  - **NIM slots (Network Interface Module)** on the right:
    - Take modular cards for extra serial or Ethernet interfaces.
    - Serial interfaces appear as `Serial0/0/0`, `Serial0/0/1`, etc.

- **Cisco 2900 Series Router**
  - Larger 2-RU router with **EH WIC slots**  
    (Enhanced High-speed WAN Interface Cards).
  - Multiple integrated Gigabit ports (`Gig0/0`, `Gig0/1`, `Gig0/2`).
  - Some Gigabit ports share an **SFP uplink** (again, copper or SFP, not both).

- **Cisco 1900 Series Router**
  - Smaller chassis, fewer EH WIC slots.
  - Typically **two integrated Gigabit ports** plus serial ports via WICs.

**Study notes**

- **Integrated ports** = built-in on the router motherboard.  
- **Modular ports (NIM/WIC)** = added via plug-in cards.
- **Interface naming:**
  - `GigabitEthernet0/0/0` → type / slot / port.
  - Shared copper/SFP: same logical name, you choose the medium.
- In labs, always **identify which exact model you have**  
  and which ports are active in the topology.

---

### 10.4.2 Video – Network Device Differences – Part 2  
*(Subnetting & ANDing review)*

Even though it’s under the “device differences” heading, this video
actually revises **IPv4 subnetting** and **binary ANDing**.

**Logical ANDing**

- IP address and subnet mask are converted to **binary**.
- Each bit is ANDed:
  - `1 AND 1 = 1`, everything else = `0`.
- Result = **network address**.

Example:

- Host IP: `192.168.1.10`
- Mask: `255.255.255.0` (`/24`)
- Binary AND → **network** `192.168.1.0`.

**Classful masks (review)**

- Class A → `/8` → `255.0.0.0`
- Class B → `/16` → `255.255.0.0`
- Class C → `/24` → `255.255.255.0`

**Moving to classless masks (subnetting)**

Example: starting network `192.168.1.0/24`

- Convert mask to binary and **borrow host bits** from left to right.
- Borrow **1 bit** from host portion:
  - Mask becomes `/25` → `255.255.255.128`
  - New host bits: `7`
  - **# of subnets:** `2^1 = 2`
  - **Hosts per subnet:** `2^7 - 2 = 128 - 2 = 126`

Subnets created:

1. `192.168.1.0/25`
   - Hosts: `192.168.1.1 – 192.168.1.126`
   - Broadcast: `192.168.1.127`
2. `192.168.1.128/25`
   - Hosts: `192.168.1.129 – 192.168.1.254`
   - Broadcast: `192.168.1.255`

**Proving with ANDing**

- IP `192.168.1.68` AND `/25` → network `192.168.1.0`
- IP `192.168.1.138` AND `/25` → network `192.168.1.128`

**Formulas to remember**

- `# subnets = 2^(# subnet bits borrowed)`
- `# hosts per subnet = 2^(# host bits) - 2`
- First address in subnet = **network address**.  
  Last address in subnet = **broadcast address**.

---

### 10.4.3 Lab – Build a Switch and Router Network

Lab / Packet Tracer activity to practise building a small routed network.

**Skills practised**

1. **Set up the topology**
   - Cable routers, switches, and PCs according to the addressing table.
2. **Configure devices**
   - Basic router config (hostname, passwords, banner).
   - Interface IPs (IPv4/IPv6) and `no shutdown`.
   - Switch SVI IP and default gateway (for management).
3. **Verify connectivity**
   - Use `show ip interface brief` / `show ipv6 interface brief`.
   - Verify routing tables with `show ip route` / `show ipv6 route`.
   - Test pings end-to-end.

**Lab mindset**

- Configure **one device at a time**, then verify before moving on.
- If pings fail, check:
  - Interface status (up/up?).
  - IP addressing & masks.
  - Default gateways on hosts & switch.

---

### 10.4.4 What did I learn in this module?

#### Configure Initial Router Settings

Core one-time tasks when bringing up a new router:

1. **Configure hostname**

   ```text
   Router(config)# hostname R1
   ```

2. **Secure privileged EXEC mode**

   ```text
   R1(config)# enable secret <STRONG-PASSWORD>
   ```

3. **Secure user EXEC (console) access**

   ```text
   R1(config)# line console 0
   R1(config-line)# password <PASSWORD>
   R1(config-line)# login
   ```

4. **Secure remote VTY (Telnet/SSH) access**

   ```text
   R1(config)# line vty 0 4
   R1(config-line)# password <PASSWORD>
   R1(config-line)# login
   R1(config-line)# transport input ssh telnet
   ```

5. **Encrypt all clear-text passwords**

   ```text
   R1(config)# service password-encryption
   ```

6. **Configure a legal notification (MOTD banner)**

   ```text
   R1(config)# banner motd #Authorized Access Only!#
   ```

7. **Save the configuration**

   ```text
   R1# copy running-config startup-config
   ```

---

#### Configure Interfaces

Routers must have **interfaces configured and enabled** to be reachable.

Typical steps (per interface):

```text
R1(config)# interface gigabitEthernet0/0/0
R1(config-if)# description Link to LAN
R1(config-if)# ip address 192.168.10.1 255.255.255.0
R1(config-if)# ipv6 address 2001:db8:acad:10::1/64
R1(config-if)# no shutdown
```

**Key verification commands**

- `show ip interface brief`
- `show ipv6 interface brief`
- `show ip route`
- `show ipv6 route`
- `show interfaces`
- `show ip interface`
- `show ipv6 interface`

---

#### Configure the Default Gateway

**On hosts**

- Default gateway = **IP of the router interface** on the same network.
- Used only when destination is on a **different network**.

**On a Layer 2 switch**

- Configure management SVI:

  ```text
  S1(config)# interface vlan 1
  S1(config-if)# ip address 192.168.10.50 255.255.255.0
  S1(config-if)# no shutdown
  ```

- Configure default gateway (router interface IP):

  ```text
  S1(config)# ip default-gateway 192.168.10.1
  ```

- For IPv6, switches typically learn the default gateway from **Router
  Advertisement (RA)** messages; manual config is less common.

---

### 10.4.5 Module Quiz – Basic Router Configuration

Use this as a **Q&A quick review** before the official quiz.

---

#### Q1  
**A router boots and enters setup mode. What is the reason for this?**

> **Answer:** The configuration file is missing from NVRAM.

- No startup-config → router drops into **setup mode** to help you create one.

---

#### Q2  
**Which command is used to encrypt all passwords in a router configuration file?**

> **Answer:** `service password-encryption`

- Encrypts all current clear-text passwords (console, vty, `enable password`).

---

#### Q3  
**Company policy requires the most secure method to protect privileged EXEC access. The password is `trustknow1`. Which command provides the highest security?**

> **Answer:** `enable secret trustknow1`

- `enable secret` stores a **hashed** password; `enable password` is plain text.

---

#### Q4  
**What is the router prompt after `Router(config)# hostname portsmouth` is entered?**

> **Answer:** `portsmouth(config)#`

- You stay in **global configuration mode**, but the hostname in the prompt changes.

---

#### Q5  
**An administrator is configuring out-of-band management access. Which commands allow login using the password `cisco`?**

> **Answer:**
> ```text
> Router(config)# line console 0
> Router(config-line)# password cisco
> Router(config-line)# login
> ```

- Out-of-band management uses the **console line**.

---

#### Q6  
**Which command displays all interfaces, IPv4 addresses, and current status?**

> **Answer:** `show ip interface brief`

- Gives a **one-line summary per interface**: IP, method, status, protocol.

---

#### Q7  
**Which CLI mode allows users to access all device commands (configuration, management, troubleshooting)?**

> **Answer:** **Privileged EXEC mode** (`Router#`)

- User EXEC (`>` prompt) is limited; configuration modes hang off Privileged EXEC.

---

#### Q8  
**What is the purpose of the startup configuration file on a Cisco router?**

> **Answer:** To contain the commands that are used to initially configure a router on startup.

- `startup-config` (NVRAM) is loaded into RAM as the **running configuration** at boot.

---

#### Q9  
**Which characteristic describes the default gateway of a host computer?**

> **Answer:** The **logical address (IP)** of the router interface on the same network as the host.

- Hosts send all **off-net traffic** to this IP.

---

#### Q10  
**What is the purpose of the `banner motd` command?**

> **Answer:** It provides a way to make announcements or warnings to those who log in to a router.

- Commonly used for **legal warnings**: “Authorized access only!”, etc.

---

#### Q11  
**A technician is typing the command `login` as part of configuring management access. Which configuration mode must be used?**

> **Answer:** **Any line configuration mode**  
> (e.g. `line console 0`, `line vty 0 4`)

- `login` is a **line sub-command**, not a global command.

---

#### Q12  
**What is stored in the NVRAM of a Cisco router?**

> **Answer:** The **startup configuration**.

- IOS is in **flash**, running-config is in **RAM**, boot instructions in **ROM**.

---

#### Q13  
**Which statement about the `service password-encryption` command is true?**

> **Answer:** As soon as `service password-encryption` is entered, all currently set passwords that were formerly displayed in plain text are encrypted.

- It **encrypts existing clear-text passwords** in the configuration.
- `no service password-encryption` does *not* decrypt them; it just stops encrypting new ones.

---
