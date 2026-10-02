# Personal Homelab Infrastructure

A personal self-hosted homelab built to gain practical experience with **virtualization, Linux administration, networking, DNS, monitoring, alerting, remote access, Docker, and infrastructure management**.

The infrastructure is built on **Proxmox VE** running on a small dedicated computer and is continuously expanded, monitored, and documented as a practical infrastructure project.

---

## Overview

This project documents the design, deployment, configuration, monitoring, troubleshooting, and ongoing improvement of my personal homelab.

The goal is to gain practical experience by running and managing real infrastructure rather than relying entirely on simulated environments.

The homelab currently includes:

* Proxmox VE virtualization
* LXC containers
* QEMU virtual machines
* Debian Linux
* Xubuntu
* Pi-hole
* Prometheus
* Grafana
* Prometheus PVE Exporter
* Node Exporter
* Blackbox Exporter
* Uptime Kuma
* ntfy notifications
* Tailscale
* Docker
* SSH
* DNS
* IPv4 networking
* Infrastructure monitoring
* Service availability monitoring
* Linux system administration
* Network troubleshooting

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
              ┌─────────────────┼─────────────────┐
              │                 │                 │
              ▼                 ▼                 ▼
         LXC CT100         LXC CT102         LXC CT104
          Pi-hole          Monitoring        Uptime Kuma
       192.168.1.121      192.168.1.122      192.168.1.124
              │                 │                 │
              │          ┌──────┼──────┐          │
              │          │      │      │          │
              │          ▼      ▼      ▼          │
              │     Prometheus Grafana Blackbox   │
              │          │             │           │
              │          │        Service Probes  │
              │          │                         │
              │          ▼                         │
              │     Alert Rules                    │
              │          │                         │
              │          └──────────┬──────────────┘
              │                     │
              │                     ▼
              │              ntfy Notifications
              │
              ▼
         DNS Filtering


                     Tailscale
                         │
                         ▼
                    Remote Device
                      / Phone
                         │
                         ▼
                 Home Network Services
```

> VM103 Minecraft Server previously hosted the Minecraft server but is currently stopped.

---

## Infrastructure

| Component    | IP / Identifier | Purpose                         | Technology                    |
| ------------ | --------------- | ------------------------------- | ----------------------------- |
| Proxmox Host | `192.168.1.120` | Virtualization platform         | Proxmox VE                    |
| CT100        | `192.168.1.121` | Network-wide DNS filtering      | Pi-hole / Debian              |
| CT102        | `192.168.1.122` | Infrastructure monitoring       | Prometheus + Grafana / Debian |
| CT104        | `192.168.1.124` | Service availability monitoring | Uptime Kuma                   |
| VM103        | `192.168.1.159` | Minecraft server                | Xubuntu + Java                |
| Tailscale    | —               | Secure remote access            | VPN / subnet routing          |

---

# Proxmox

The Proxmox server acts as the central virtualization platform for the homelab.

It provides:

* Virtual machine management
* LXC container management
* Virtual networking
* Resource allocation
* Storage management
* Centralized infrastructure administration
* Service isolation

The Proxmox host uses the `vmbr0` network bridge to provide network connectivity to containers and virtual machines.

### Current Host

```text
Hostname: darwish
IP:       192.168.1.120
Gateway:  192.168.1.1
Bridge:   vmbr0
```

More information:

[Proxmox Documentation](documentation/proxmox.md)

---

# Pi-hole

Pi-hole runs inside **CT100** and provides network-wide DNS filtering.

### Functions

* DNS-based advertisement blocking
* Tracker blocking
* DNS query monitoring
* Network-wide filtering
* DNS troubleshooting

### Container

```text
CT:       100
IP:       192.168.1.121
OS:       Debian
```

Pi-hole is connected directly to the Proxmox network bridge and can provide DNS services to devices on the home network.

More information:

[Pi-hole Documentation](documentation/pihole.md)

---

# Infrastructure Monitoring

A dedicated monitoring stack runs inside **CT102**.

The monitoring container is intentionally kept focused on infrastructure monitoring rather than hosting unrelated applications.

## Monitoring Stack

### Prometheus

Prometheus collects and stores metrics from the homelab.

It is used to monitor:

* Proxmox
* Linux system resources
* Service availability
* Infrastructure health

### Grafana

Grafana provides dashboards for visualizing Prometheus metrics.

Example metrics include:

* CPU usage
* Memory usage
* System resources
* Proxmox resources
* Service availability
* Monitoring status

### Prometheus PVE Exporter

The PVE Exporter collects Proxmox-related metrics for Prometheus.

This allows the monitoring system to observe the virtualization host and its workloads.

### Node Exporter

Node Exporter collects Linux system-level metrics such as:

* CPU usage
* Memory usage
* Disk usage
* Network statistics
* System load

### Blackbox Exporter

Blackbox Exporter performs external probes against services.

It can test:

* HTTP availability
* TCP connectivity
* Network reachability
* Service response status

An important distinction in this setup is that **Blackbox Exporter being healthy does not mean the monitored service is healthy**.

The actual probe result is determined using metrics such as:

```text
probe_success
```

---

# Uptime Kuma

**CT104** runs Uptime Kuma as a dedicated service availability monitoring system.

```text
CT:  104
IP:  192.168.1.124
```

Uptime Kuma complements Prometheus by providing a service-oriented monitoring interface.

It can monitor whether services are reachable and available.

Examples include:

* HTTP services
* TCP services
* Internal infrastructure
* Web applications
* Network services

Uptime Kuma is intentionally kept outside CT102 so that infrastructure metrics and service availability monitoring remain separated.

---

# Alerting and Notifications

The monitoring infrastructure is configured to send notifications when important conditions occur.

The current notification system uses **ntfy**.

Examples of alert conditions include:

* High CPU usage
* High memory usage
* Service downtime
* Failed monitoring probes
* Infrastructure availability problems

The goal is to move beyond dashboards that only show problems and instead create a system that can actively notify when something requires attention.

---

# Minecraft Server

A dedicated Xubuntu virtual machine was previously used to host a vanilla Minecraft Java Edition server.

### Configuration

* Minecraft Java Edition 1.21.8
* Xubuntu
* Java
* 4 CPU cores
* 5 GB RAM
* 32 GB virtual disk
* TCP port `25565`

The server was also monitored using Blackbox Exporter to test TCP availability.

The Minecraft server is **currently stopped** because it is not actively being used.

The VM remains documented as part of the project's history and demonstrates experience with:

* QEMU virtual machines
* Linux server administration
* Java application hosting
* Network service configuration
* TCP connectivity
* Service monitoring

More information:

[Minecraft Server Documentation](documentation/minecraft.md)

---

# Remote Management

Tailscale is used for secure remote access to the homelab.

The Proxmox host is connected to the Tailscale network and can provide access to services on the home network.

Example:

```text
Phone
  │
  │ Mobile Data / Internet
  │
  ▼
Tailscale
  │
  ▼
Proxmox Host
  │
  │ Subnet Routing
  ▼
Home Network
  │
  ├── Proxmox
  ├── Pi-hole
  ├── Grafana
  └── Other Services
```

This allows remote administration without directly exposing infrastructure management interfaces to the public internet.

More information:

[Tailscale Documentation](documentation/tailscale.md)

---

# Docker

Docker is used for services where containerized application deployment is appropriate.

The monitoring stack in CT102 uses Docker Compose to manage its services.

Example architecture:

```text
CT102
 │
 └── Docker
      │
      ├── Prometheus
      ├── Grafana
      └── Blackbox Exporter
```

This provides practical experience with:

* Docker containers
* Docker Compose
* Container networking
* Persistent volumes
* Configuration files
* Service management

---

# Troubleshooting

A major purpose of this project is documenting real problems encountered while building and operating the infrastructure.

### Examples

* Proxmox network configuration
* DNS resolution problems
* Pi-hole filtering issues
* Virtual machine networking
* Prometheus exporter configuration
* Monitoring target failures
* Alert configuration
* Docker resource usage
* Tailscale remote access
* Service connectivity
* Network routing
* Linux service management

### Troubleshooting Guides

* [Proxmox Networking](troubleshooting/proxmox-networking.md)
* [Pi-hole DNS](troubleshooting/pihole-dns.md)
* [Minecraft Server](troubleshooting/minecraft-server.md)

---

# Skills Demonstrated

## Virtualization

* Proxmox VE
* LXC containers
* QEMU virtual machines
* Virtual networking
* Resource allocation
* Service isolation

## Linux

* Debian
* Xubuntu
* SSH
* Linux networking
* Service management
* Command-line administration
* Package management
* System troubleshooting

## Networking

* IPv4 addressing
* Subnets
* Default gateways
* DNS
* DHCP
* Network bridges
* TCP services
* Port forwarding
* VPN-based remote access
* Tailscale subnet routing

## Monitoring

* Prometheus
* Grafana
* PromQL
* Node Exporter
* Blackbox Exporter
* Prometheus PVE Exporter
* Proxmox monitoring
* Service availability monitoring
* Alert rules
* ntfy notifications

## Containers

* LXC
* Docker
* Docker Compose
* Container networking
* Persistent storage
* Service isolation

## Infrastructure

* Server deployment
* Infrastructure monitoring
* Remote administration
* Network troubleshooting
* Alerting
* Technical documentation
* Self-hosted services

---

# Lessons Learned

Building this homelab has provided hands-on experience with problems that are difficult to fully understand through theory alone.

Some of the most important lessons include:

* Understanding how virtual machines and containers connect to a physical network
* Configuring Linux networking
* Troubleshooting DNS resolution
* Managing services remotely
* Monitoring infrastructure and service availability
* Configuring exporters and monitoring targets
* Creating and testing infrastructure alerts
* Separating infrastructure services from application workloads
* Diagnosing connectivity problems
* Understanding the relationship between routers, DNS, virtual networks, and servers
* Managing limited CPU and memory resources
* Understanding the differences between LXC and Docker
* Using monitoring data to troubleshoot real infrastructure problems

A particularly important lesson from the monitoring setup was that **monitoring infrastructure and monitoring the service being tested are two different things**.

For example, Blackbox Exporter can be running normally while the service it probes is unavailable. Therefore, the monitoring system must distinguish between:

```text
Exporter Health
       │
       └── Is Blackbox Exporter running?

Probe Result
       │
       └── Is the monitored service reachable?
```

This distinction became important when configuring service availability monitoring and alerting.

---

# Security Considerations

Security is treated as an ongoing part of the infrastructure rather than a one-time configuration.

Current practices include:

* Remote administration through Tailscale
* Avoiding unnecessary public exposure of infrastructure services
* Container and VM isolation
* Proxmox firewall configuration
* SSH-based administration
* Monitoring service availability
* Separating infrastructure services by workload

Future security improvements include:

* Network segmentation
* More granular firewall rules
* Centralized authentication
* Automated security scanning
* Regular backup testing
* Improved secret management

---

# Future Improvements

The homelab is intended to evolve continuously.

Potential future improvements include:

### Infrastructure

* Automated Proxmox backups
* Dedicated backup storage
* Infrastructure-as-Code
* Terraform
* Ansible
* Improved infrastructure diagrams

### Networking

* Network segmentation
* VLANs
* Improved firewall policies
* Network traffic monitoring

### Monitoring

* Additional service monitoring
* Temperature monitoring
* More advanced alerting
* Additional exporters
* Long-term metrics and capacity planning

### Self-Hosted Applications

Potential services to experiment with include:

* Syncthing
* Paperless-ngx
* Immich
* Jellyfin
* Mealie
* Password management

### Automation

Future automation work may include:

* Automated deployments
* Configuration management
* Infrastructure testing
* GitHub Actions
* Automated documentation
* Infrastructure health checks

---

# Repository Structure

```text
personal-homelab/
│
├── README.md
│
├── documentation/
│   ├── proxmox.md
│   ├── pihole.md
│   ├── tailscale.md
│   ├── minecraft.md
│   ├── monitoring.md
│   └── uptime-kuma.md
│
├── troubleshooting/
│   ├── proxmox-networking.md
│   ├── pihole-dns.md
│   └── minecraft-server.md
│
├── docs/
│   └── images/
│       ├── grafana-dashboard.png
│       ├── prometheus-targets.png
│       └── prometheus-alerts.png
│
└── diagrams/
```

---

# Project Goals

The long-term goal of this project is to develop a practical understanding of infrastructure and systems administration by continuously:

* Building infrastructure
* Deploying services
* Monitoring systems
* Troubleshooting failures
* Automating repetitive tasks
* Improving security
* Documenting configurations
* Testing new technologies

Rather than being a static server installation, the homelab is treated as an evolving infrastructure environment where new technologies can be introduced, tested, monitored, and documented.

This project serves as a portfolio demonstrating practical experience with **Linux, virtualization, networking, monitoring, containers, infrastructure management, and systems administration** beyond academic coursework.
