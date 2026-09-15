# Personal Homelab Infrastructure

A personal self-hosted homelab built to gain practical experience with virtualization, Linux administration, networking, DNS, remote access, and server management.

The infrastructure is built on **Proxmox VE** running on a small dedicated computer.

---

## Overview

This project documents the design, configuration, troubleshooting, and future improvements of my personal homelab.

The main goal is to build and manage real infrastructure rather than relying entirely on simulated environments.

### Main Technologies

* Proxmox VE
* LXC Containers
* QEMU Virtual Machines
* Debian Linux
* Xubuntu
* Pi-hole
* Tailscale
* SSH
* DNS
* IPv4 Networking
* Linux System Administration
* Network Troubleshooting

---

## Architecture

```text
                         Internet
                            │
                            │
                     Home Router
                      192.168.1.1
                            │
                         Ethernet
                            │
                  ┌──────────────────┐
                  │   Proxmox Host   │
                  │     darwish      │
                  │   192.168.1.120  │
                  └────────┬─────────┘
                           │
                         vmbr0
                           │
              ┌────────────┴────────────┐
              │                         │
          LXC CT100                  VM103
           Pi-hole                 Minecraft
       192.168.1.121             192.168.1.159
              │
        DNS Filtering

              ┌───────────────────────┐
              │       Tailscale       │
              │   Remote Management   │
              └───────────┬───────────┘
                          │
                        Phone
                    Remote Access
```

---

## Infrastructure

| Component    | Purpose                    | Technology     |
| ------------ | -------------------------- | -------------- |
| Proxmox Host | Virtualization platform    | Proxmox VE     |
| CT100        | Network-wide DNS filtering | Pi-hole        |
| VM103        | Minecraft server           | Xubuntu + Java |
| Tailscale    | Remote management          | VPN mesh       |

---

## Proxmox

The Proxmox server acts as the central virtualization platform for the homelab.

It provides:

* Virtual machine management
* LXC container management
* Virtual networking
* Resource allocation
* Storage management
* Centralized infrastructure administration

The Proxmox host uses the `vmbr0` network bridge to provide network connectivity to the containers and virtual machines.

More information:

`documentation/proxmox.md`

---

## Pi-hole

Pi-hole runs inside an LXC container and provides network-wide DNS filtering.

### Functions

* DNS-based advertisement blocking
* Tracker blocking
* DNS query monitoring
* Network-wide filtering
* DNS troubleshooting

The Pi-hole container is connected directly to the Proxmox network bridge.

More information:

`documentation/pihole.md`

---

## Minecraft Server

A dedicated Xubuntu virtual machine hosts a vanilla Minecraft Java Edition server.

The server is isolated from the Proxmox host and other infrastructure services.

### Configuration

* Minecraft Java Edition 1.21.8
* Xubuntu
* Java
* 4 CPU cores
* 5 GB RAM
* 32 GB virtual disk
* TCP port `25565`

More information:

`documentation/minecraft.md`

---

## Remote Management

Tailscale is installed on the Proxmox host for secure remote management.

This allows the Proxmox web interface to be accessed remotely without directly exposing the Proxmox management interface to the public internet.

Example:

```text
Phone
  │
  │ Mobile Data / Internet
  │
Tailscale
  │
  ▼
Proxmox Host
  │
  ▼
Proxmox Web Interface
```

More information:

`documentation/tailscale.md`

---

## Troubleshooting

One of the main purposes of this project is to document real problems encountered while building and operating the infrastructure.

### Examples

* Proxmox network configuration issues
* DNS resolution problems
* Pi-hole filtering issues
* Minecraft server connectivity
* Port forwarding
* Virtual machine networking
* Remote management

Troubleshooting documentation:

```text
troubleshooting/
├── proxmox-networking.md
├── pihole-dns.md
└── minecraft-server.md
```

---

## Skills Demonstrated

This project provides practical experience with:

### Virtualization

* Proxmox VE
* LXC containers
* QEMU virtual machines
* Virtual networking
* Resource allocation

### Linux

* Debian
* Xubuntu
* SSH
* Linux networking
* Service management
* Command-line administration

### Networking

* IPv4 addressing
* Subnets
* Default gateways
* DNS
* DHCP
* Network bridges
* Port forwarding
* VPN-based remote access

### Infrastructure

* Server deployment
* Service isolation
* Network troubleshooting
* Remote administration
* Infrastructure documentation

---

## Lessons Learned

Building this homelab has provided hands-on experience with problems that are difficult to understand from theory alone.

Some of the most important lessons include:

* Understanding how virtual machines and containers connect to a physical network
* Configuring Linux networking
* Troubleshooting DNS resolution
* Managing services remotely
* Separating infrastructure services from application workloads
* Diagnosing connectivity problems
* Understanding the relationship between routers, DNS, virtual networks, and servers

---

## Future Improvements

Planned improvements include:

* Automated Proxmox backups
* Dedicated backup storage
* Infrastructure monitoring
* Resource monitoring
* Network segmentation
* Improved security controls
* Infrastructure automation
* Additional self-hosted services
* Better documentation and infrastructure diagrams

---

## Repository Structure

```text
personal-homelab/
│
├── README.md
│
├── documentation/
│   ├── proxmox.md
│   ├── pihole.md
│   ├── tailscale.md
│   └── minecraft.md
│
├── troubleshooting/
│   ├── proxmox-networking.md
│   ├── pihole-dns.md
│   └── minecraft-server.md
│
└── diagrams/
```

---

## Project Goals

The long-term goal of this project is to develop a practical understanding of infrastructure and systems administration by continuously building, maintaining, troubleshooting, and improving a real self-hosted environment.

This homelab also serves as a portfolio project demonstrating practical experience beyond academic coursework.
