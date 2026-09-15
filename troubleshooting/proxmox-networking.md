# Proxmox Networking Troubleshooting

## Overview

During the initial Proxmox setup, a network configuration issue occurred where the Proxmox web interface was not accessible using the expected IP address.

This troubleshooting process helped identify the correct LAN configuration and verify connectivity between the Proxmox host and the home network.

---

## Initial Problem

After installing Proxmox, the web interface was initially accessed using an incorrect network address.

The server was later configured on the home network using:

```text
Proxmox IP: 192.168.1.120
Gateway:    192.168.1.1
Network:    192.168.1.0/24
```

The Proxmox web interface uses HTTPS on port `8006`.

The correct access address is:

```text
https://192.168.1.120:8006
```

---

## Network Architecture

```text
Home Router
192.168.1.1
     │
     │ Ethernet
     ▼
Proxmox Host
192.168.1.120
     │
     ▼
   vmbr0
     │
 ┌───┴──────────────┐
 │                  │
LXC Containers    Virtual Machines
```

The Proxmox host uses `vmbr0` as its main network bridge.

---

## Network Verification

The host network configuration can be checked with:

```bash
ip addr
```

The routing table can be checked with:

```bash
ip route
```

The bridge configuration can be inspected with:

```bash
ip addr show vmbr0
```

The expected Proxmox bridge address is:

```text
192.168.1.120/24
```

---

## Connectivity Testing

The default gateway can be tested with:

```bash
ping 192.168.1.1
```

Internet connectivity can be tested with:

```bash
ping 1.1.1.1
```

DNS resolution can be tested with:

```bash
ping google.com
```

These tests help separate different types of network problems.

| Test               | Purpose                                 |
| ------------------ | --------------------------------------- |
| `ping 192.168.1.1` | Tests local gateway connectivity        |
| `ping 1.1.1.1`     | Tests internet connectivity without DNS |
| `ping google.com`  | Tests internet connectivity and DNS     |
| `ip route`         | Checks routing configuration            |
| `ip addr`          | Checks interface and IP configuration   |

---

## Network Interface

The physical Ethernet interface is connected to the Proxmox bridge.

The general network path is:

```text
Physical Ethernet
       │
       ▼
    nic0
       │
       ▼
    vmbr0
       │
       ├── LXC Containers
       │
       └── Virtual Machines
```

The Wi-Fi interface is not used for the Proxmox host's primary network connection.

---

## Web Interface Verification

Once the host has the correct LAN address, the Proxmox web interface can be accessed from another device on the same network:

```text
https://192.168.1.120:8006
```

The browser may display a certificate warning because Proxmox uses a self-signed certificate by default.

This does not necessarily indicate a network problem.

---

## Troubleshooting Approach

The issue was approached by checking the network layer by layer:

```text
1. Check network interface
        ↓
2. Check IP address
        ↓
3. Check default gateway
        ↓
4. Test local network connectivity
        ↓
5. Test internet connectivity
        ↓
6. Test DNS
        ↓
7. Test Proxmox web interface
```

This approach makes it easier to determine whether a problem is caused by the physical network, IP configuration, routing, DNS, or the Proxmox web service.

---

## Lessons Learned

The main lesson was that an incorrect IP address can make a correctly installed Proxmox server appear inaccessible.

Network troubleshooting should therefore begin with basic connectivity and configuration checks before changing higher-level services.

The experience also provided practical understanding of:

* Linux network interfaces
* IP addressing
* Default gateways
* Network bridges
* Routing
* DNS
* Proxmox web management
