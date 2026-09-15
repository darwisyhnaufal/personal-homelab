# Pi-hole

## Overview

Pi-hole is deployed as an LXC container on the Proxmox host and provides network-wide DNS filtering.

It is used to block advertisements, trackers, and known unwanted domains at the DNS level.

---

## Deployment

Pi-hole runs inside an unprivileged Debian LXC container.

| Property         | Value            |
| ---------------- | ---------------- |
| Container        | CT100            |
| Operating System | Debian           |
| CPU              | 1 core           |
| RAM              | 512 MB           |
| Swap             | 512 MB           |
| Storage          | 8 GB             |
| IP Address       | `192.168.1.121`  |
| Gateway          | `192.168.1.1`    |
| Network Bridge   | `vmbr0`          |
| Container Type   | Unprivileged LXC |

The container is connected to the Proxmox `vmbr0` bridge, allowing it to communicate with devices on the home network.

---

## Pi-hole Version

The deployed Pi-hole components are:

| Component    | Version |
| ------------ | ------- |
| Pi-hole Core | 6.4.3   |
| Pi-hole Web  | 6.6     |
| Pi-hole FTL  | 6.7     |

---

## DNS Architecture

The basic DNS flow is:

```text
Client Device
     │
     │ DNS Query
     ▼
Pi-hole
192.168.1.121
     │
     │ Allowed DNS Query
     ▼
Upstream DNS
     │
     ▼
Internet
```

When a client requests a domain, Pi-hole checks whether the domain is present in its configured blocklists.

If the domain is blocked, Pi-hole prevents the request from reaching the destination.

If the domain is allowed, the request is forwarded to the configured upstream DNS resolver.

---

## Network Configuration

The Pi-hole container uses:

```text
IP Address: 192.168.1.121
Gateway:    192.168.1.1
Network:    192.168.1.0/24
```

The container uses `1.1.1.1` as its configured nameserver.

---

## Verification

Pi-hole status can be checked with:

```bash
pihole status
```

DNS resolution can be tested with:

```bash
dig @127.0.0.1 google.com
```

A domain can be checked against the Pi-hole blocklists with:

```bash
pihole -q doubleclick.net
```

For example, `doubleclick.net` was found in multiple configured blocklists.

This confirmed that Pi-hole was actively applying DNS filtering.

---

## Windows Client Testing

DNS filtering was also tested from a Windows client.

Using:

```cmd
nslookup doubleclick.net
```

The domain was resolved to:

```text
0.0.0.0
```

and:

```text
::
```

This indicates that Pi-hole was blocking the domain instead of returning its normal destination address.

---

## DNS Troubleshooting

During the setup, a DNS-related problem was encountered while using Minecraft.

The Minecraft Launcher experienced problems downloading resources from:

```text
resources.download.minecraft.net
```

When the computer was using Pi-hole as its DNS resolver, the domain request timed out.

Testing the same domain using external DNS resolvers such as:

```text
1.1.1.1
8.8.8.8
```

returned an address associated with the Azure CDN.

This helped identify that the problem was related to DNS resolution rather than the Minecraft server itself.

Further DNS configuration and testing eventually restored normal resolution.

---

## Troubleshooting Method

The issue was investigated by testing DNS at different points.

### 1. Test DNS through Pi-hole

```bash
dig @127.0.0.1 google.com
```

### 2. Test an external DNS resolver

```bash
dig @1.1.1.1 google.com
```

### 3. Compare the results

If the external resolver works but Pi-hole does not, the problem is likely related to the Pi-hole configuration, upstream DNS, or filtering.

This approach helps isolate whether a problem originates from:

* The client
* Pi-hole
* The upstream DNS resolver
* The wider network

---

## Why Pi-hole Is Useful

Running Pi-hole provides practical experience with:

* DNS
* Network-wide filtering
* Linux services
* DNS troubleshooting
* Blocklists
* Network configuration
* Client-side DNS configuration

Instead of installing Pi-hole on an individual computer, deploying it as a dedicated LXC container allows multiple devices on the network to use the same DNS filtering service.

---

## Lessons Learned

The Pi-hole deployment demonstrated that DNS problems can affect applications even when general internet connectivity appears to be working.

Testing the same domain through multiple DNS resolvers is useful for determining whether a problem is caused by:

* DNS filtering
* Upstream DNS
* Network connectivity
* Application configuration

This troubleshooting process was particularly useful when diagnosing the Minecraft resource download issue.

---

## Future Improvements

Possible improvements include:

* Configure reliable DNS fallback
* Improve DNS monitoring
* Add DNS query logging and analysis
* Configure additional blocklists carefully
* Monitor Pi-hole availability
* Integrate Pi-hole with remote VPN access
* Document DNS failure scenarios
