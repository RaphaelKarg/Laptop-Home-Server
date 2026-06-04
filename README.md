# 🏡 Laptop Home Server

> **Design and Implementation of a Personal Homelab powered by a Linux Server on the CasaOS Platform: Centralizing Data Management, Network Security, and Multiple Services to Build a Private Home Cloud and Achieve Complete Independence from Global Public Clouds.**

![Linux](https://img.shields.io/badge/OS-Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Docker](https://img.shields.io/badge/Container-Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![CasaOS](https://img.shields.io/badge/Dashboard-CasaOS-00A2D4?style=for-the-badge)
![Network](https://img.shields.io/badge/Network-Self_Hosted-4CAF50?style=for-the-badge)

---

## 📑 Table of Contents

1. [Abstract](#1-abstract)
2. [Project Introduction](#2-project-introduction)
   - [2.1 Vision & Purpose](#21-vision--purpose)
   - [2.2 Theoretical Background (Linux & Self-Hosting)](#22-theoretical-background)
   - [2.3 Project Goals](#23-project-goals)
3. [System Architecture & Infrastructure](#3-system-architecture--infrastructure)
   - [3.1 Physical Network Topology](#31-physical-network-topology)
   - [3.2 Server Hardware](#32-server-hardware)
   - [3.3 Server Structure & Software](#33-server-structure--software)
4. [Services Analysis (Implementation & Troubleshooting)](#4-services-analysis)
   - [4.1 AdGuard Home & Quad9 DoH (Security Server)](#41-adguard-home--quad9-doh)
   - [4.2 File Server, Automations & Backup Strategy](#42-file-server-automations--backup-strategy)
   - [4.3 Game Server Management (Crafty) & Playit.gg](#43-game-server-management-crafty--playitgg)
   - [4.4 Media Server (Jellyfin)](#44-media-server-jellyfin)
   - [4.5 Tailscale (Mesh VPN)](#45-tailscale-mesh-vpn)
   - [4.6 NetAlertX (Network Monitoring)](#46-netalertx-network-monitoring)
5. [Future Expansions](#5-future-expansions)
6. [Conclusions](#6-conclusions)

---

## 1. Abstract

This project presents the architecture, deployment, and documentation of a comprehensive, self-hosted **Homelab** tailored specifically for a large family residence. Designed to achieve digital sovereignty and complete independence from commercial public clouds (*De-Clouding*), the system is built to facilitate the everyday digital needs of the household, offering easy data management, robust security, and centralized entertainment.

By upcycling an older personal laptop that was about to be retired following a hardware upgrade—giving it a second life and preventing e-waste, while inherently taking advantage of its built-in battery as an Uninterruptible Power Supply (UPS)—this initiative establishes a robust, highly available, and cost-effective private cloud ecosystem. The infrastructure is powered by a **Linux Server** environment and managed through the **CasaOS** platform, utilizing **Docker containerization** for isolated, scalable, and efficient service deployment.

The architecture is built upon four foundational pillars:

* **🛡️ Security & Safe Browsing:** Implementing network-wide protection against ads, trackers, and malware via **AdGuard Home** to ensure a highly secure internet surfing experience for all family members, routing requests through encrypted DNS (DoH/DNSSEC) via **Quad9**. **NetAlertX** acts as a continuous network scanner for intrusion detection.
* **📂 Data Management & Family Sharing:** A centralized **Local File Server (NAS)** that provides the entire family with effortless access, sharing, and management of their personal files, backed by custom scripting for automated directory backups to an external 1TB drive.
* **🌍 Secure Remote Access (Zero-Trust):** Eliminating the risks of traditional port forwarding by utilizing **Tailscale** (Mesh VPN) for encrypted remote access and management—allowing family members to safely connect to home services from anywhere—and **Playit.gg** (Reverse Tunneling) to safely expose local game servers.
* **🎮 Entertainment Hub:** Self-hosting a centralized media library for family movie nights via **Jellyfin** (Media Server) and managing a dedicated Minecraft Game Server for recreation through the **Crafty Controller** web interface.

Ultimately, this homelab serves as a centralized hub demonstrating how enterprise-grade network management, stringent data privacy, and diverse digital services can be successfully consolidated to elevate the digital lifestyle of a modern household.

---

## 2. Project Introduction

### 2.1 Vision & Purpose

In an era where personal and digital lives are increasingly reliant on commercial tech giants (Big Tech), the core vision of this project is **"De-Clouding"**—the deliberate migration away from public cloud services (such as Google Drive, iCloud, or Dropbox) to regain absolute control over personal data. 

Beyond data sovereignty, this project is driven by a commitment to resourcefulness and sustainability. By **upcycling an aging personal laptop** that would have otherwise been left idle after a recent equipment upgrade, the project actively prevents electronic waste (e-waste) and transforms older hardware into a powerful, centralized network hub. The purpose is to prove that enterprise-grade security, seamless data syncing, and high-quality digital services can be achieved at home without expensive hardware or monthly subscription fees.

### 2.2 Theoretical Background (Linux & Self-Hosting)

To understand the architecture of this homelab, two core concepts must be defined:

* **Self-Hosting:** This is the practice of running and maintaining software applications on your own private server rather than consuming them as Software-as-a-Service (SaaS) from third parties. Self-hosting shifts the paradigm from "renting digital space" to "owning your digital infrastructure," guaranteeing that data never leaves the local network unless explicitly authorized.
* **Linux as the Foundation:** The server is powered by a minimal Linux distribution. Linux is the undisputed industry standard for server environments due to its open-source nature, unparalleled stability, and low resource overhead. By combining Linux with the **CasaOS** platform, the system utilizes **Docker containerization**. Containers ensure that every service runs in its own isolated environment with its own dependencies, preventing system conflicts and allowing for effortless updates and scalability.

### 2.3 Project Goals

The implementation of this laptop-based homelab was guided by the following concrete objectives:

1. **Complete Data Ownership:** Deploy a reliable Local Area Network (LAN) File Server / NAS for rapid data transfer and media storage.
2. **Network-Wide Security:** Implement a DNS-level sinkhole to block ads, malicious domains, and trackers for every device connected to the home router, while encrypting outbound DNS requests.
3. **Zero-Trust Remote Access:** Establish a secure, encrypted tunnel to the local network from anywhere in the world, completely eliminating the need for vulnerable port forwarding.
4. **Self-Hosted Entertainment:** Run lag-free, dedicated gaming environments (Minecraft) and media streaming platforms (Jellyfin) managed via intuitive web interfaces.
5. **Hardware Efficiency & Resilience:** Capitalize on the laptop's built-in battery to act as an Uninterruptible Power Supply (UPS), ensuring the server remains active and safely shuts down during unexpected power outages.

## 3. System Architecture & Infrastructure

This section outlines the physical and logical layers of the homelab, detailing the network topology, the upcycled hardware acting as the core server, and the underlying software environment.

### 3.1 Physical Network Topology

The network is designed to support a large household (6 individuals) with numerous concurrent devices, ensuring high throughput, minimal latency, and future scalability. The foundation of the external connection is a Fiber-to-the-Home (FTTH) line providing speeds of **1000/500 Mbps**.

![Network Topology Architecture](./images/topology_Diagram_with_grid.png)
> *Figure 1: Logical and Physical Flow of the Homelab Environment*

**Core Networking Components & IP Allocation:**
To maintain network stability and avoid conflicts, a strict IP allocation strategy is enforced at the router level.
* **ISP Router (Gateway - `192.168.1.1`):** A Sercomm Speedport Plus 2 (Wi-Fi 6 certified) serves as the primary gateway to the WAN.
* **Core Switch:** A **TP-Link TL-SG1016PE v3 (16-Port Gigabit PoE+)**. Housed within a 19-inch rack for optimal cable management. The Power over Ethernet (PoE+) capability and high port count provide massive scalability for future projects (e.g., Access Points).
* **DHCP vs. Static Pool:** The IP range `192.168.1.1` to `192.168.1.10` is strictly reserved for **Static IP assignments** (e.g., the Laptop Server is pinned to `192.168.1.2`). The remaining pool (`192.168.1.11` to `192.168.1.254`) is handled by the DHCP server for dynamic allocation to client devices.
* **Cabling:** All hardwired connections utilize **Lanberg S/FTP Cat.6a** cables. The Shielded Foiled Twisted Pair (S/FTP) construction eliminates electromagnetic interference, effortlessly handling 1Gbps traffic while being future-proofed for 10Gbps.

**Connected Local Hosts:**
* **Wired (Ethernet via Switch):** 2x Desktop PCs, 1x Laptop, and the central Homelab Server (`192.168.1.2`).
* **Wireless (Wi-Fi 6 via Router):** 1x Smart TV, 2x Tablets, and 4-5 Smartphones.

### 3.2 Server Hardware

The core of the homelab is built upon a repurposed gaming laptop. This upcycling strategy provides a massive advantage for server hosting: the internal 6-Cell battery acts as a built-in Uninterruptible Power Supply (UPS), guaranteeing graceful shutdowns during power outages.

**System Specifications: MSI GL62M 7REX & External Storage**
| Hardware Component | Specifications / Model | Details & Purpose |
| :--- | :--- | :--- |
| **CPU** | Intel Core i7-7700HQ @ 2.80GHz | 7th Gen (Kaby Lake), 4 Cores / 8 Threads. Provides excellent multi-tasking for concurrent Docker containers. |
| **RAM** | 8GB DDR4 | 1x 8GB installed (1 slot free, upgradeable to 32GB). Sufficient for the current container load. |
| **OS Drive (Disk 1)** | 120GB SSD (Kingston RBUSNS8) | M.2 SSD. Houses the Linux OS and the Docker engine for rapid boot times. |
| **Storage Drive (Disk 2)**| 1TB HDD (Seagate ST1000LM048) | 2.5" SATA. Primary internal mass storage for the NAS and media files. |
| **Backup Drive (External)**| 1TB HDD (WD Elements) | USB 3.0 External Drive. Dedicated destination for automated scripts and system backups, ensuring critical data redundancy outside the internal chassis. |
| **Power Supply** | AC Adapter (150W) & Li-Ion Battery | 6-Cell (41 Whr) battery ensuring 100% uptime during short electrical grid fluctuations. |

*(Note: The dedicated NVIDIA GTX 1050 Ti is currently inactive to minimize power consumption, relying solely on the integrated Intel HD Graphics 630).*

### 3.3 Server Structure & Software

To maximize the hardware's efficiency, a "bare-metal to container" approach was adopted.

![CasaOS Dashboard](./images/casaos_dashboard.png)
> *Figure 2: The CasaOS Web Interface managing the system resources and Docker Containers.*

1. **Host Operating System:** A minimal Linux Server distribution acts as the bare-metal foundation, eliminating the overhead of a Desktop Environment (GUI).
2. **CasaOS (The Dashboard):** Deployed on top of Linux, CasaOS serves as the central orchestration interface. It provides an elegant, web-based UI to monitor system resources and manage storage drives.
3. **Docker Containerization:** Every service in this homelab (AdGuard, Jellyfin, Crafty, etc.) runs as an isolated Docker Container managed through CasaOS, ensuring that dependencies never conflict.
