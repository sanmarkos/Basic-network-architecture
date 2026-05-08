# Enterprise Network Architecture Simulation

## Project Overview

This project simulates a professional enterprise network infrastructure using Cisco Packet Tracer. The environment represents a corporate office and warehouse network with centralized services, internet connectivity, wireless access, VLAN segmentation, and enterprise security controls.

The architecture was designed to demonstrate practical networking concepts used in real enterprise environments including routing, switching, access control, network segmentation, infrastructure services, and troubleshooting.

---

# Enterprise Network Architecture

```text

                                         INTERNET
                                             |
                                   +----------------+
                                   |   ISP Router   |
                                   +----------------+
                                      /          \
                                     /            \
                                    /              \
                                   /                \
                    Serial Connection            Serial Connection
                                 /                      \
                                /                        \
                               /                          \
                 +--------------------+      +-----------------------+
                 |   Office Router    |      |   Warehouse Router    |
                 +--------------------+      +-----------------------+
                            |                            |
                     Trunk Connection             Trunk Connection
                            |                            |
          +--------------------------------+   +--------------------------------+
          | Office Multilayer Switch       |   | Warehouse Multilayer Switch    |
          +--------------------------------+   +--------------------------------+
             |        |        |      |             |         |           |
             |        |        |      |             |         |           |
          HR WiFi     IT    Finance  Servers   Warehouse    WiFi       Printer
             |                            |        PCs
        Guest WiFi                   DMZ Server

```

---

# Infrastructure Components

## Office Network

* HR Wireless Network
* Guest Wireless Network
* IT Department Systems
* Finance Department Systems
* Office Printer Network
* Internal Server Infrastructure
* DMZ Public Server
* VLAN Segmentation
* Inter-VLAN Routing

---

## Warehouse Network

* Warehouse User Systems
* Warehouse Wireless Access
* Warehouse Printer Network
* VLAN Segmentation
* Routed Connectivity

---

## Enterprise Services

### Internal Services

* DNS Server
* HTTP Internal Website
* FTP File Transfer Service
* SYSLOG Logging Server

### Public Services

* Public DMZ Website
* Internet Web Server Simulation

---

## Security Implementation

* ACL Traffic Filtering
* Guest WiFi Isolation
* Department Segmentation
* SSH Remote Management
* NAT/PAT Internet Access
* DMZ Isolation
* Management VLAN

---

# Skills Demonstrated

## Networking

* Enterprise Network Design
* VLAN Architecture
* Inter-VLAN Routing
* Router-on-a-Stick Configuration
* Static Routing
* WAN Connectivity
* Wireless Network Integration

---

## Security

* Access Control Lists (ACLs)
* Guest Network Isolation
* DMZ Deployment
* SSH Secure Remote Management
* Network Segmentation
* Traffic Filtering

---

## Infrastructure Services

* DHCP Configuration
* DNS Configuration
* HTTP Web Services
* FTP Services
* SYSLOG Monitoring
* NAT/PAT Configuration

---


# Troubleshooting Experience

During development of this project, multiple enterprise networking issues were identified and resolved.

## Issues Solved

* VLAN trunking problems
* DHCP configuration failures
* NAT overload conflicts
* DNS resolution problems
* SSH timeout troubleshooting
* Inter-VLAN communication issues
* Browser request timeout problems
* Routing path verification
* ACL filtering conflicts
* Serial DCE/DTE configuration issues
* Configuration persistence and reload verification

---

# Project Purpose

The purpose of this project is to demonstrate practical enterprise networking implementation using Cisco Packet Tracer.

This environment was built to simulate real-world corporate infrastructure concepts commonly used in:

* Enterprise Offices
* Warehouses
* Corporate Campuses
* SOC/NOC Environments
* IT Infrastructure Teams
* Network Administration Roles
* Cybersecurity Operations

The project focuses on practical implementation, security segmentation, enterprise services, infrastructure management, and troubleshooting skills.

---

