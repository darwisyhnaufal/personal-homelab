# Pi-hole DNS Troubleshooting

## Overview

A DNS resolution problem occurred while using Pi-hole as the DNS server for the home network.

The issue became noticeable when the Minecraft Launcher was unable to download resources from:

```text
resources.download.minecraft.net
```

Other internet connectivity was available, so the problem required testing DNS separately from general network connectivity.

---

## Symptoms

The main symptom was:

```text
Minecraft Launcher
        │
        ▼
resources.download.minecraft.net
        │
        ▼
DNS request through Pi-hole
        │
        ▼
Request timed out
```

When Pi-hole was not being used as the DNS resolver, the same domain could be resolved successfully using external DNS resolvers.

---

## Initial Testing

The first step was to test DNS directly through Pi-hole.

Inside the Pi-hole container:

```bash
dig @127.0.0.1 google.com
```

This tests whether Pi-hole's local DNS service can resolve a domain.

An external DNS resolver was then tested:

```bash
dig @1.1.1.1 google.com
```

This bypasses Pi-hole and tests the upstream DNS resolver directly.

---

## Comparing DNS Results

The comparison can be represented as:

```text
                Pi-hole
                  │
                  ▼
              DNS Query
                  │
              Problem
                  │
                  ▼
               Timeout


                1.1.1.1
                  │
                  ▼
              DNS Query
                  │
                  ▼
             Successful
```

Testing both paths helped determine that the issue was related to DNS resolution rather than the Minecraft server itself.

---

## Domain Testing

The Minecraft resource domain was tested independently from the Minecraft application.

The domain was:

```text
resources.download.minecraft.net
```

Testing DNS through different resolvers helped establish that the domain itself was reachable through external DNS.

This prevented the troubleshooting process from incorrectly focusing on the Minecraft server configuration.

---

## Blocklist Verification

Pi-hole blocklist behavior was also tested using:

```bash
pihole -q doubleclick.net
```

The command returned matches from configured blocklists.

This confirmed that Pi-hole was actively processing DNS requests and applying filtering rules.

A Windows client was also tested with:

```cmd
nslookup doubleclick.net
```

The domain resolved to:

```text
0.0.0.0
```

and:

```text
::
```

This demonstrated that Pi-hole was correctly blocking the test domain.

---

## Troubleshooting Process

The problem was investigated using a layered approach:

```text
1. Test application
        ↓
2. Identify affected domain
        ↓
3. Test DNS through Pi-hole
        ↓
4. Test external DNS directly
        ↓
5. Test Pi-hole blocklist behavior
        ↓
6. Compare results
        ↓
7. Adjust DNS configuration
        ↓
8. Retest application
```

This approach helped isolate DNS resolution from the Minecraft server itself.

---

## Important Lesson

A device can have working internet connectivity while still experiencing application-specific problems caused by DNS.

For example:

```text
Internet Connectivity
        │
        ├── Working
        │
        └── DNS Resolution
                │
                └── Problem
```

Testing only whether a device can access the internet is therefore not enough when troubleshooting DNS-related issues.

---

## Useful Diagnostic Commands

### Check Pi-hole status

```bash
pihole status
```

### Test local DNS

```bash
dig @127.0.0.1 google.com
```

### Test external DNS

```bash
dig @1.1.1.1 google.com
```

### Query Pi-hole blocklists

```bash
pihole -q doubleclick.net
```

### Test DNS from Windows

```cmd
nslookup google.com
```

### Test a specific domain

```cmd
nslookup resources.download.minecraft.net
```

---

## Lessons Learned

This incident provided practical experience with:

* DNS resolution
* Pi-hole
* Upstream DNS servers
* DNS filtering
* Blocklists
* `dig`
* `nslookup`
* Application-level troubleshooting
* Layered network troubleshooting

The main lesson was to test the same DNS query through multiple resolvers when diagnosing application connectivity problems.

This makes it possible to determine whether the problem originates from the application, local DNS server, upstream DNS, or the wider network.
