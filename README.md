# Lynis Custom Security Tests — Enhanced Password Policy & DNS Resolver Auditing

Custom Lynis audit modules for hardening Linux security. These scripts extend the default Lynis audit suite with in-depth password aging validation, shadow account inspection, and DNS resolver security checks.

---

## Overview

This repository contains two enhanced custom Lynis test scripts that replace and improve upon a baseline reference implementation:

| Test ID | Name | What It Audits |
|---------|------|----------------|
| CUST-0001 | Enhanced Password Aging & Shadow Audit | PASS_MAX_DAYS, PASS_MIN_DAYS, PASS_WARN_AGE, blank passwords in /etc/shadow |
| CUST-0002 | Enhanced DNS Resolver Security Audit | Symlink detection, stub resolver identification, nameserver redundancy, edns0, search domain hijacking |

---

## Prerequisites

- **OS:** Ubuntu Linux (tested on Ubuntu 26.04)
- **Lynis:** Version 3.1.6 or later
- **Privileges:** Root access required (`sudo`)

### Install Lynis

```bash
sudo apt update
sudo apt install lynis -y
lynis show version
```

---

## Installation

1. Clone this repository:

```bash
git clone https://github.com/omkurkute9545/lynis-custom-security-tests.git
cd lynis-custom-security-tests
```

2. Copy the combined script to the Lynis include directory:

```bash
sudo cp tests_custom /usr/share/lynis/include/tests_custom
```

3. Verify the file is in place:

```bash
ls -la /usr/share/lynis/include/tests_custom
```

---

## Usage

### Run both custom tests

```bash
sudo lynis audit system --tests CUST-0001,CUST-0002
```

### Run only the password audit

```bash
sudo lynis audit system --tests CUST-0001
```

### Run only the DNS audit

```bash
sudo lynis audit system --tests CUST-0002
```

### Run a full system audit (includes custom tests automatically)

```bash
sudo lynis audit system
```

---

## Expected Output

### CUST-0001 — Password Aging & Shadow Audit (Hardened System)

```
[+] Enhanced Password & Authentication Checks
--------------------------------------------------------------------------------
  - Auditing Password Aging & Authentication Policy...
    Password Max Age (90 days)                  [ OK ]
    Password Min Age (7 days)                   [ OK ]
    Password Warning Window (7 days)            [ OK ]
    Blank Password Shadow Audit                 [ OK ]
```

### CUST-0001 — Password Aging & Shadow Audit (Default/Unhardened System)

```
[+] Enhanced Password & Authentication Checks
--------------------------------------------------------------------------------
  - Auditing Password Aging & Authentication Policy...
    Password Max Age (99999 days)               [ WARNING ]
    Password Min Age (0 days)                   [ WARNING ]
    Password Warning Window (7 days)            [ OK ]
    Blank Password Shadow Audit                 [ OK ]
```

### CUST-0002 — DNS Resolver Security Audit

```
[+] Enhanced DNS & Resolver Security Checks
--------------------------------------------------------------------------------
  - Auditing DNS Configuration and Resolver Security...
    Resolv.conf: Managed Symlink                [ OK ]
    DNS Stub Resolver (127.0.0.53 detected)     [ OK ]
    Nameserver Availability (1 entry/entries)    [ OK ]
    DNS Extension (edns0 active)                [ OK ]
    DNS Search Path (lan)                       [ OK ]
```

---

## Security Thresholds

### Password Policy (CUST-0001)

| Parameter | Recommended Value | Why |
|-----------|-------------------|-----|
| PASS_MAX_DAYS | ≤ 90 days | Forces regular credential rotation |
| PASS_MIN_DAYS | ≥ 1 day | Prevents immediate password reuse |
| PASS_WARN_AGE | ≥ 7 days | Gives users advance notice before lockout |
| Shadow blank passwords | None | Prevents unauthenticated login |

### DNS Resolver (CUST-0002)

| Check | Expected | Why |
|-------|----------|-----|
| Symlink detection | Managed symlink | Prevents silent overwrite by network managers |
| Stub resolver | 127.0.0.53 identified | Clarifies local caching vs upstream DNS |
| Redundancy | ≥ 2 nameservers (or stub) | Avoids single point of failure |
| edns0 | Enabled | Required for DNSSEC and large DNS payloads |
| Search domains | ≤ 3 | Prevents query leakage and suffix hijacking |

---

## Hardening Your System

If the scripts report warnings, apply these fixes:

```bash
# Fix password aging policy
sudo nano /etc/login.defs
# Set:
#   PASS_MAX_DAYS   90
#   PASS_MIN_DAYS   7
#   PASS_WARN_AGE   7

# Enable edns0 for DNS
echo "options edns0" | sudo tee -a /etc/resolv.conf
```

Then re-run the audit to verify:

```bash
sudo lynis audit system --tests CUST-0001,CUST-0002
```

---

## Repository Structure

```
lynis-custom-security-tests/
├── README.md              # This file
└── tests_custom           # Combined enhanced Lynis custom test script
```

---

---

## License

This project is for educational purposes as part of the Cyber Security coursework at VIT Chennai.
