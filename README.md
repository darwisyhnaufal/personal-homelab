# Personal Homelab Infrastructure

A self-hosted home infrastructure project built using **Proxmox VE** to gain practical experience in virtualization, Linux system administration, networking, DNS, VPNs, remote access, and server management.

The homelab is continuously developed as a personal learning environment for experimenting with IT infrastructure and networking technologies.

---

## Architecture

> Network architecture diagram will be added here.

### Current Infrastructure

```text
                         INTERNET
                             │
                     ┌───────▼───────┐
                     │ Home Router   │
                     │ 192.168.1.1   │
                     └───────┬───────┘
                             │
                         Ethernet
                             │
                    ┌────────▼────────┐
                    │    Proxmox      │
                    │     darwish     │
                    │ 192.168.1.120   │
                    └────────┬────────┘
                             │
                           vmbr0
                             │
          ┌──────────────────┼──────────────────┐
          │                  │                  │
    ┌─────▼─────┐      ┌─────▼──────┐     ┌─────▼──────┐
    │   CT100   │      │   CT101    │     │   VM103    │
    │  Pi-hole  │      │ WireGuard  │     │ Minecraft  │
    │192.168.1.121│    │192.168.1.155│    │192.168.1.159│
    └───────────┘      │            │     └────────────┘
                       │ 10.0.0.1/24│
                       └────────────┘

                    Tailscale
                       │
                       │
                  Remote Access
                       │
                     Phone
```

---

## Technologies

* Proxmox VE
* Debian Linux
* Xubuntu
* LXC Containers
* QEMU Virtual Machines
* Pi-hole
* WireGuard
* Tailscale
* SSH
* DNS
* IPv4 networking
* Network troubleshooting

---

## Infrastructure

### Proxmox VE

**Version:** 9.2.5
**Hostname:** `darwish`
**LAN IP:** `192.168.1.120`

Proxmox VE is used as the primary virtualization platform for managing the homelab's virtual machines and LXC containers.

The host uses `vmbr0` as its primary network bridge and is connected to the home network through Ethernet.

### Pi-hole

**Container:** CT100
**IP:** `192.168.1.121`
**Operating System:** Debian
**CPU:** 1 core
**Memory:** 512 MB
**Storage:** 8 GB

Pi-hole provides DNS-based network-wide ad and tracker blocking.

### WireGuard

**Container:** CT101
**IP:** `192.168.1.155`
**Operating System:** Debian
**CPU:** 1 core
**Memory:** 512 MB
**Storage:** 4 GB

WireGuard provides VPN connectivity to the home network.

The WireGuard interface uses:

```text
Network: 10.0.0.0/24
Server: 10.0.0.1
```

This allows authorized devices to establish a VPN connection to the homelab.

### Tailscale

**Proxmox Tailscale IP:** `100.86.131.18`

Tailscale provides remote access to the homelab from outside the home network.

It allows the Proxmox management interface to be accessed remotely without directly exposing the Proxmox web interface to the public internet.

### Minecraft Server

**Virtual Machine:** VM103
**IP:** `192.168.1.159`
**Operating System:** Xubuntu
**CPU:** 4 cores
**Memory:** 5 GB
**Storage:** 32 GB

A Minecraft Java Edition server is hosted inside a dedicated virtual machine.

The server is managed through Linux and SSH and provides practical experience with server administration, networking, and port forwarding.

---

## Skills Demonstrated

### Virtualization

* Proxmox VE administration
* LXC container deployment
* QEMU virtual machine management
* CPU and memory allocation
* Virtual storage management
* Virtual networking

### Linux Administration

* Debian administration
* Xubuntu administration
* SSH
* Linux networking
* Service management
* Package management
* Command-line troubleshooting

### Networking

* IPv4 addressing
* DHCP
* DNS
* Network bridges
* VPN networking
* Port forwarding
* Local network troubleshooting
* Remote network access

### Infrastructure Management

* Service deployment
* Server configuration
* Remote administration
* Network security
* Troubleshooting
* Resource management

---

## Troubleshooting Experience

This homelab has also been used as a practical environment for diagnosing real infrastructure problems.

Examples include:

* Proxmox network connectivity problems
* DNS resolution issues involving Pi-hole
* Minecraft server connectivity problems
* VPN connectivity troubleshooting
* Remote access configuration
* Network and IP address conflicts

Detailed troubleshooting reports will be added to the `troubleshooting/` directory.

---

## Future Improvements

Planned improvements include:

* [ ] Proxmox backup strategy
* [ ] Automated backups
* [ ] Infrastructure monitoring
* [ ] Uptime monitoring
* [ ] Grafana and Prometheus
* [ ] NAS integration
* [ ] Network segmentation
* [ ] Improved security controls
* [ ] Infrastructure automation
* [ ] Additional service deployment

---

## Project Goals

The primary goal of this project is to gain practical experience designing, deploying, managing, monitoring, and troubleshooting a small-scale IT infrastructure environment.

Rather than relying solely on theoretical knowledge, the homelab provides a controlled environment for experimenting with real infrastructure technologies and documenting the results.

The project will continue to evolve as new technologies and services are introduced.
