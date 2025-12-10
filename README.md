# CCNA – Basic Router Configuration (Module 10)

Quick-reference notes for NetAcad CCNA – **Module 10: Basic Router Configuration**.  
Focus is on bringing a new router online: initial setup, interface IP addressing,
default gateway config on hosts/switches, and basic verification/troubleshooting.

I use this repo to revise:

- The **standard first steps** on a new router (hostname, passwords, banners, etc.).  
- How to **configure router interfaces** with IPv4/IPv6 addresses and enable them.  
- The commands to **verify interface status and connectivity**.  
- How to configure the **default gateway on hosts and switches**.  
- Common **default-gateway and interface misconfig issues** and how to fix them.  

---

## Table of Contents

- [Module 10 Overview](#module-10-overview)  
- [Module Map](#module-map)  

- [10.0 Introduction](#100-introduction)  
  - [10.0.1 Why should I take this module?](#1001-why-should-i-take-this-module)  
  - [10.0.2 What will I learn in this module?](#1002-what-will-i-learn-in-this-module)  

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
  - [10.2.5 Syntax Checker – Configure Interfaces](#1025-syntax-checker--configure-interfaces)  

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

