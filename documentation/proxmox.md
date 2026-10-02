# Proxmox Infrastructure

Proxmox VE is the foundation of the homelab.

It provides the virtualization layer used to run isolated Linux containers and virtual machines.

## Host

```text
Hostname: darwish
IP:       192.168.1.120
Gateway:  192.168.1.1
Bridge:   vmbr0
```

## Network Architecture

```text
Router
192.168.1.1
    │
    │ Ethernet
    ▼
Proxmox
192.168.1.120
    │
    ▼
vmbr0
    │
    ├── CT100  Pi-hole
    │    192.168.1.121
    │
    ├── CT102  Monitoring
    │    192.168.1.122
    │
    ├── CT104  Uptime Kuma
    │    192.168.1.124
    │
    └── VM103  Minecraft
         192.168.1.159
```

## Workloads

| ID | Type | Purpose | IP |
|---|---|---|---|
| 100 | LXC | Pi-hole | 192.168.1.121 |
| 102 | LXC | Prometheus/Grafana monitoring | 192.168.1.122 |
| 103 | QEMU VM | Minecraft server | 192.168.1.159 |
| 104 | LXC | Uptime Kuma | 192.168.1.124 |

VM103 is currently stopped.

## LXC Containers

LXC containers are used for lightweight services that do not require a full virtual machine.

Current examples include:

- Pi-hole
- Monitoring
- Uptime Kuma

Advantages for this homelab include:

- Low resource overhead
- Fast startup
- Simple management
- Network isolation
- Easy resource allocation

## QEMU Virtual Machines

A QEMU VM provides stronger isolation and a complete virtual hardware environment.

The Minecraft server was deployed in a dedicated Xubuntu VM.

This provided practical experience with:

- VM creation
- CPU and RAM allocation
- Virtual disks
- Linux installation
- SSH administration
- Network services

## Resource Management

The host has limited hardware resources, so resource allocation is an important part of the design.

Each workload receives resources based on its purpose.

Monitoring resource usage is itself part of the monitoring project.

Useful commands include:

```bash
free -h
```

```bash
df -h
```

```bash
top
```

and Proxmox's built-in resource graphs.

## Networking

The `vmbr0` bridge connects virtual workloads to the physical LAN.

This allows containers and VMs to receive addresses on the same home network:

```text
192.168.1.0/24
```

with:

```text
Gateway: 192.168.1.1
```

## Management

Proxmox provides a web interface for:

- Starting and stopping workloads
- Resource configuration
- Console access
- Storage management
- Network configuration
- Monitoring

SSH is also used for Linux administration inside the workloads.

## Future Improvements

- Automated backups
- Dedicated backup storage
- VLAN/network segmentation
- More granular firewall rules
- Infrastructure-as-Code
- Automated provisioning
- Improved disaster recovery testing
