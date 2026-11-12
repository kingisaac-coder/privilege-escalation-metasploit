# Privilege Escalation via Metasploit & Meterpreter — CEH Module 06

A hands-on lab demonstrating privilege escalation and post-exploitation techniques against a Windows target using the Metasploit Framework and Meterpreter, performed entirely within an isolated virtual lab environment (Parrot Security OS attacker machine + Windows 10 victim VM on a private host-only network).

## Objective

Walk through a Meterpreter-based post-exploitation workflow on a Windows target to understand:

1. How an attacker operates once a Meterpreter session is established
2. How file access leaves forensic traces (MACE attributes) — and how those traces can be tampered with
3. How keystroke capture works as a credential-harvesting technique
4. What level of system control a privileged session actually grants

## Environment

| Component | Details |
|---|---|
| Attacker | Parrot Security OS (VirtualBox VM) |
| Target | Windows 10 (Build 19045, 22H2) |
| Network | Host-only / NAT, isolated lab segment |
| Framework | Metasploit Framework |

## Tools Used

- **msfvenom** — payload generation (`windows/meterpreter/reverse_tcp`)
- **Metasploit (`multi/handler`)** — listener and session management
- **Meterpreter** — post-exploitation shell and built-in modules
- **Meterpreter `timestomp`** — MACE timestamp manipulation (anti-forensics)
- **Meterpreter `keyscan`** — keystroke capture

## Methodology

### 1. Session Establishment
A reverse TCP Meterpreter payload was generated with `msfvenom` and delivered to the target VM. A Metasploit `multi/handler` listener caught the callback, establishing a Meterpreter session with `getuid` used to confirm the active privilege context.

### 2. File System Enumeration
Used Meterpreter's `ls` and `cat` to browse the target's filesystem and read file contents (e.g. a planted `Secret.txt`), simulating how an attacker locates and accesses sensitive data on a compromised host.

### 3. Anti-Forensics — MACE Timestamp Manipulation
Inspected a file's MACE attributes (Modified, Accessed, Created, Entry Modified) with `timestomp -v`, then selectively altered each value (`-m`, `-a`, `-c`, `-e`) to arbitrary dates. This demonstrates how reading or dropping a file changes its metadata — and how that metadata can be falsified to blend malicious activity in with legitimate file history.
