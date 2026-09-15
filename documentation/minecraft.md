# Minecraft Server

## Overview

A vanilla Minecraft Java Edition server is hosted inside a dedicated Xubuntu virtual machine running on Proxmox.

The purpose of this setup is to provide a self-hosted multiplayer Minecraft server while keeping the application isolated from the Proxmox host.

---

## Virtual Machine

The Minecraft server runs on VM103.

| Property         | Value           |
| ---------------- | --------------- |
| VM ID            | `103`           |
| Name             | `mcserver`      |
| Operating System | Xubuntu         |
| CPU              | 4 cores         |
| RAM              | 5 GB            |
| Storage          | 32 GB           |
| LAN IP           | `192.168.1.159` |

The VM is connected to the Proxmox `vmbr0` network bridge.

---

## Minecraft Configuration

| Property         | Value                 |
| ---------------- | --------------------- |
| Edition          | Java Edition          |
| Version          | 1.21.8                |
| Server Type      | Vanilla               |
| Server Port      | `25565`               |
| Server Directory | `/home/tun/minecraft` |
| Java Version     | OpenJDK 21            |

The server does not use mods or a mod loader.

---

## Server Directory

The Minecraft server files are stored in:

```bash
/home/tun/minecraft
```

The main server JAR is:

```text
server.jar
```

---

## Starting the Server

The server is started with:

```bash
cd /home/tun/minecraft
java -Xms2G -Xmx3G -jar server.jar nogui
```

The JVM is configured with:

* Initial memory: 2 GB
* Maximum memory: 3 GB

This leaves resources available for the Xubuntu operating system and other processes running on the VM.

---

## Server Port

Minecraft Java Edition uses TCP port:

```text
25565
```

The server can be checked from inside the VM with:

```bash
ss -tulpn | grep 25565
```

A successful result shows Java listening on port `25565`.

Example:

```text
tcp LISTEN 0 4096 *:25565 *:* users:(("java",pid=...,fd=...))
```

This confirms that the Minecraft server is actively listening for connections.

---

## Network Architecture

```text
Internet
   │
   ▼
Home Router
192.168.1.1
   │
   │ Port Forwarding
   │ TCP 25565
   ▼
Proxmox
192.168.1.120
   │
   ▼
vmbr0
   │
   ▼
VM103
192.168.1.159
   │
   ▼
Minecraft Server
TCP 25565
```

The Minecraft server is isolated inside VM103 rather than running directly on the Proxmox host.

---

## Multiplayer Access

For players on the same local network, the Minecraft server can be accessed using the VM's local IP address:

```text
192.168.1.159:25565
```

For players outside the home network, the router must forward TCP port `25565` to:

```text
192.168.1.159:25565
```

The public IP address is intentionally not documented in this repository.

---

## Port Forwarding

The router is configured to forward Minecraft traffic to the Minecraft VM.

```text
External TCP 25565
        │
        ▼
Router
        │
        ▼
192.168.1.159:25565
        │
        ▼
Minecraft Server
```

Only the required Minecraft server port should be exposed.

The Proxmox management port should not be exposed through the same public forwarding configuration.

---

## Port Forwarding Security

Port forwarding makes the Minecraft server reachable from outside the home network. Because the forwarded port is exposed to the internet, it should be limited to the service that actually needs external access.

The configuration should follow this principle:

```text
Internet
   │
   │ TCP 25565
   ▼
Home Router
   │
   │ Only forward required port
   ▼
192.168.1.159:25565
   │
   ▼
Minecraft Server
```

### Security Measures

The following practices are used:

* Only TCP port `25565` is forwarded for Minecraft.
* The forwarding target is the Minecraft VM, not the Proxmox host.
* Proxmox management port `8006` is not exposed through the Minecraft forwarding rule.
* SSH should not be exposed publicly unless there is a specific requirement.
* The Minecraft server runs inside a dedicated VM to provide separation from the Proxmox host.
* Router administration should remain accessible only from the trusted local network.
* Public IP addresses and router credentials are not documented in this public repository.

### Why This Matters

A port-forwarded service is potentially reachable by anyone on the internet who can connect to that port.

Therefore, port forwarding should be treated as an intentional exposure of **one specific service**, rather than exposing the entire home network.

For this homelab, the intended path is:

```text
Internet
   │
   │ TCP 25565
   ▼
Router
   │
   ▼
Minecraft VM
   │
   ▼
Minecraft Server
```

Other infrastructure services such as Proxmox management remain inaccessible from the public internet through this forwarding rule.

### Additional Hardening

Future improvements could include:

* Keeping the Minecraft server and Java runtime updated
* Regular world backups
* Monitoring server logs
* Restricting who can join the server
* Using firewall rules to limit unnecessary traffic
* Reviewing router port-forwarding rules periodically
* Removing the port-forwarding rule when the server is no longer needed


## Troubleshooting

### Check whether the server is running

```bash
ps aux | grep java
```

### Check whether Minecraft is listening

```bash
ss -tulpn | grep 25565
```

### Check the VM's IP address

```bash
ip addr
```

### Check the default gateway

```bash
ip route
```

The expected gateway is:

```text
192.168.1.1
```

---

## DNS Troubleshooting

A DNS issue was encountered while using Pi-hole as the network DNS server.

The Minecraft Launcher had difficulty downloading resources from:

```text
resources.download.minecraft.net
```

Testing DNS using external resolvers helped determine that the issue was related to DNS resolution rather than the Minecraft server itself.

This provided a practical example of how DNS problems can affect applications even when general internet connectivity is available.

---

## Why Use a Virtual Machine?

The Minecraft server is hosted inside a dedicated VM instead of directly on the Proxmox host.

This provides:

* Separation from the virtualization host
* Independent resource allocation
* Independent operating system
* Easier server management
* Reduced impact on other infrastructure services

The VM can be restarted or modified without directly affecting the Proxmox management environment.

---

## Lessons Learned

This project provided practical experience with:

* Linux server administration
* Java application deployment
* Virtual machine management
* Network configuration
* TCP ports
* Port forwarding
* Server troubleshooting
* DNS troubleshooting
* Multiplayer server hosting

It also demonstrated the relationship between the application, virtual machine, local network, router, and internet connection.

---

## Future Improvements

Possible improvements include:

* Automated server startup
* Automated backups
* Server monitoring
* Resource monitoring
* Scheduled backups of the Minecraft world
* Improved firewall rules
* Dedicated backup storage
* Infrastructure monitoring
