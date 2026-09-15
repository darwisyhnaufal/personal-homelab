# Tailscale

## Overview

Tailscale is used in this homelab to provide secure remote access to the Proxmox server.

It creates a private network between trusted devices without requiring the Proxmox management interface to be directly exposed to the public internet.

---

## Deployment

Tailscale is installed directly on the Proxmox host.

| Property          | Value             |
| ----------------- | ----------------- |
| Host              | Proxmox           |
| Hostname          | `darwish`         |
| Network Interface | `tailscale0`      |
| Purpose           | Remote management |

---

## Remote Access

When Tailscale is enabled on the phone, the Proxmox web interface can be accessed remotely using:

```text
https://<PROXMOX-TAILSCALE-IP>:8006
```

For example:

```text
https://100.x.x.x:8006
```

The actual Tailscale IP is intentionally not published in this repository.

---

## Remote Access Architecture

```text
                    Internet
                       │
                       │
                ┌──────▼──────┐
                │  Tailscale  │
                │ Private VPN │
                └──────┬──────┘
                       │
              ┌────────▼────────┐
              │  Proxmox Host   │
              │     darwish     │
              └────────┬────────┘
                       │
                       ▼
              Proxmox Web Interface
                    Port 8006
```

---

## Mobile Access

The Proxmox interface can be accessed from a phone even when the phone is using mobile data.

The connection path is:

```text
Phone
  │
  │ Mobile Data
  │
  ▼
Tailscale
  │
  ▼
Proxmox Host
  │
  ▼
HTTPS :8006
```

### Access Command

Open the following address in a browser while Tailscale is connected:

```text
https://<PROXMOX-TAILSCALE-IP>:8006
```

For the actual homelab, replace `<PROXMOX-TAILSCALE-IP>` with the Tailscale IP assigned to the Proxmox host.

---

## Verification

Check Tailscale status:

```bash
tailscale status
```

Check the Tailscale network interface:

```bash
ip addr show tailscale0
```

Check the Tailscale service:

```bash
systemctl status tailscaled
```

---

## Security Considerations

The Proxmox management port `8006` is not directly exposed to the public internet.

Instead, remote access is provided through the Tailscale network.

The following information is intentionally excluded from this public repository:

* Actual Tailscale IP address
* Private keys
* Authentication credentials
* Device-specific secrets
* Account information

---

## Why Tailscale Is Useful

Tailscale provides practical experience with:

* VPN networking
* Remote administration
* Private network connectivity
* Linux networking
* Secure infrastructure access
* Remote troubleshooting

---

## Lessons Learned

Remote administration does not necessarily require exposing management services directly to the internet.

Using a private overlay network provides a controlled way to access infrastructure remotely while keeping the Proxmox management interface off the public internet.

---

## Future Improvements

Possible improvements include:

* Remote monitoring
* Additional trusted devices
* Access control improvements
* Service-specific remote access
* Monitoring Tailscale connectivity
* Remote-access troubleshooting documentation
