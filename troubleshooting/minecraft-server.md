# Minecraft Server Troubleshooting

## Overview

This document covers troubleshooting steps used to verify that the self-hosted Minecraft server is running correctly and can accept multiplayer connections.

The Minecraft server runs inside VM103 on Proxmox.

---

## Server Information

| Property          | Value                 |
| ----------------- | --------------------- |
| VM                | `VM103`               |
| VM Name           | `mcserver`            |
| Server IP         | `192.168.1.159`       |
| Minecraft Version | `1.21.8`              |
| Server Port       | `25565/TCP`           |
| Server Directory  | `/home/tun/minecraft` |

---

## Step 1 — Check the Server Process

First, verify that the Java Minecraft server process is running:

```bash
ps aux | grep java
```

A running server should show a Java process associated with `server.jar`.

---

## Step 2 — Check the Listening Port

The next step is to verify that Minecraft is listening on TCP port `25565`:

```bash
ss -tulpn | grep 25565
```

A successful result should resemble:

```text
tcp LISTEN 0 4096 *:25565 *:* users:(("java",pid=...,fd=...))
```

This confirms that the Minecraft server is listening for incoming connections.

---

## Step 3 — Check the VM IP Address

The VM's network configuration can be checked with:

```bash
ip addr
```

The Minecraft VM should have:

```text
192.168.1.159
```

on its network interface.

---

## Step 4 — Check the Default Gateway

Check the routing table:

```bash
ip route
```

The expected default gateway is:

```text
192.168.1.1
```

This confirms that the VM has a route to the home network and internet.

---

## Step 5 — Test Local Network Access

A device on the same home network can test the Minecraft server using:

```text
192.168.1.159:25565
```

If local clients can connect but external players cannot, the Minecraft server itself is likely working and the problem is more likely related to router configuration or external connectivity.

---

## Step 6 — Check Port Forwarding

For external multiplayer access, the router needs to forward:

```text
TCP 25565
```

to:

```text
192.168.1.159:25565
```

The expected connection path is:

```text
External Player
       │
       ▼
Internet
       │
       ▼
Home Router
       │
       │ TCP 25565
       ▼
192.168.1.159
       │
       ▼
Minecraft Server
```

---

## Local vs External Troubleshooting

The most useful distinction is whether the server works locally.

### Local connection works

If:

```text
192.168.1.159:25565
```

works from another device on the LAN, but external players cannot connect, investigate:

* Router port forwarding
* WAN connectivity
* Public IP changes
* ISP restrictions
* Firewall rules

### Local connection fails

If local clients cannot connect, investigate:

* Minecraft server process
* Port `25565`
* VM IP address
* VM networking
* Server configuration
* Linux firewall

This prevents unnecessary changes to the router when the actual problem is inside the VM.

---

## Minecraft Server Startup

The server is started from:

```bash
cd /home/tun/minecraft
```

using:

```bash
java -Xms2G -Xmx3G -jar server.jar nogui
```

If the server fails to start, check the terminal output for Java or Minecraft server errors.

---

## Common Diagnostic Commands

### Check Java process

```bash
ps aux | grep java
```

### Check listening ports

```bash
ss -tulpn | grep 25565
```

### Check IP configuration

```bash
ip addr
```

### Check routing

```bash
ip route
```

### Check network connectivity

```bash
ping 192.168.1.1
```

### Check internet connectivity

```bash
ping 1.1.1.1
```

### Check DNS

```bash
ping google.com
```

---

## DNS vs Server Connectivity

A previous DNS issue demonstrated that Minecraft-related problems are not always caused by the Minecraft server.

For example:

```text
Minecraft Launcher
       │
       ▼
DNS Resolution
       │
       ▼
resources.download.minecraft.net
```

This is separate from players connecting to the Minecraft server:

```text
Minecraft Client
       │
       ▼
TCP 25565
       │
       ▼
Minecraft Server
```

Understanding this distinction helps avoid troubleshooting the wrong component.

---

## Security Considerations

Only the Minecraft server port should be exposed for external multiplayer access.

The Proxmox management interface should remain private.

The intended architecture is:

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

Proxmox management remains accessible through the local network or private remote-access solutions such as Tailscale.

---

## Lessons Learned

Troubleshooting the Minecraft server provided practical experience with:

* Linux processes
* Java applications
* TCP ports
* Virtual machines
* IP addressing
* Routing
* DNS
* Port forwarding
* Router configuration
* Local vs external connectivity testing

The main lesson is to troubleshoot from the inside out:

```text
Application
    ↓
Listening Port
    ↓
VM Network
    ↓
Router
    ↓
Internet
```

Checking each layer individually makes it easier to identify where connectivity problems originate.
