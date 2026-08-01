# My Nmap Learning Journey

> Date: August 1, 2026

---

# Introduction

Today I started learning **Nmap (Network Mapper)**, one of the most widely used network scanning and reconnaissance tools in cybersecurity.

My goal is to understand how security professionals discover devices, identify running services, and assess network exposure in authorized environments.

---

# What is Nmap?

Nmap stands for **Network Mapper**.

It is an open-source tool used for:

- Network discovery
- Host discovery
- Port scanning
- Service enumeration
- Operating system detection
- Network inventory
- Security assessments (with authorization)

Nmap is commonly used by:

- Network Administrators
- Security Analysts
- Penetration Testers
- SOC Analysts
- Ethical Hackers

---

# Why Learn Nmap?

I learned that before defending a network, it is important to understand what devices and services are exposed.

Nmap helps answer questions like:

- Which devices are online?
- Which ports are open?
- Which services are running?
- What operating system might the target be using?
- Are there unnecessary services exposed?

---

# Installation

I installed Nmap and verified the installation by running:

```bash
nmap --version
```

This confirmed that Nmap was installed successfully.

---

# Basic Command Structure

The general syntax is:

```bash
nmap [options] target
```

Examples:

```bash
nmap 192.168.1.10
```

```bash
nmap scanme.nmap.org
```

---

# Concepts I Learned

## IP Address

Every device connected to a network has an IP address.

Example:

```
192.168.1.100
```

---

## Ports

Ports are communication endpoints on a device.

Examples include:

| Port | Service |
|------|----------|
|22|SSH|
|80|HTTP|
|443|HTTPS|
|3389|RDP|

---

## Open Ports

An open port means a service is listening and can accept connections.

Example:

```
80/tcp open http
```

---

## Closed Ports

A closed port is reachable but no service is listening.

---

## Filtered Ports

A filtered port usually means a firewall is blocking access.

---

# Host Discovery

I learned that Nmap can identify which hosts are alive on a network.

Example:

```bash
nmap -sn 192.168.1.0/24
```

This performs host discovery without scanning ports.

---

# Port Scanning

I learned how to scan a target for open ports.

Basic scan:

```bash
nmap 192.168.1.10
```

Specific port:

```bash
nmap -p 80 192.168.1.10
```

Port range:

```bash
nmap -p 1-1000 192.168.1.10
```

All TCP ports:

```bash
nmap -p- 192.168.1.10
```

---

# Service Detection

Using:

```bash
nmap -sV target
```

Nmap attempts to determine which services are running and their versions.

Example output:

```
22/tcp open ssh OpenSSH
80/tcp open http Apache
```

---

# Operating System Detection

I learned that Nmap can estimate the operating system of a host.

Command:

```bash
sudo nmap -O target
```

---

# Aggressive Scan

The command:

```bash
sudo nmap -A target
```

combines several features including:

- OS Detection
- Version Detection
- Default Scripts
- Traceroute

---

# Timing Templates

I learned that scan speed can be adjusted.

Examples:

```
-T0
-T1
-T2
-T3
-T4
-T5
```

Higher timing values generally increase speed but may be more detectable or less reliable on some networks.

---

# Output Formats

Nmap results can be saved.

Normal:

```bash
-oN results.txt
```

XML:

```bash
-oX results.xml
```

All formats:

```bash
-oA scan
```

---

# Nmap Scripting Engine (NSE)

Nmap includes scripts that extend its capabilities.

Default scripts:

```bash
nmap -sC target
```

Example:

```bash
nmap --script http-title target
```

---

# Important Things I Learned

- Nmap is one of the most important tools in cybersecurity.
- It is useful for network discovery and assessment.
- Open ports may indicate exposed services.
- Running services should be identified and reviewed.
- Scan results should be interpreted carefully.
- Scanning systems without permission may violate laws or organizational policies.

---

# Commands I Practiced

```bash
nmap target
```

```bash
nmap -sn subnet
```

```bash
nmap -p 80 target
```

```bash
nmap -p- target
```

```bash
nmap -sV target
```

```bash
nmap -O target
```

```bash
nmap -A target
```

```bash
nmap -oA scan target
```

---

# Challenges

While learning Nmap, I realized:

- There are many scan types.
- Understanding networking concepts is essential.
- Reading scan results takes practice.
- Different targets may respond differently depending on firewalls and configurations.

---

# Key Takeaways

Today I learned:

- What Nmap is
- Why it is important
- How to install it
- Basic command syntax
- Host discovery
- Port scanning
- Service detection
- Operating system detection
- Saving scan results
- Basic NSE usage

I now have a solid foundation and plan to continue learning more advanced Nmap features, networking concepts, and defensive monitoring techniques in future sessions.

---

# Next Learning Goals

- Learn different TCP scan types
- Understand UDP scanning
- Explore NSE scripts in more detail
- Practice in authorized lab environments
- Learn how defenders detect scanning activity
- Study packet captures with Wireshark while running Nmap scans

---

**Learning Status:** ✅ Beginner Foundation Completed
