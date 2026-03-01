# Metasploit-Attack-Defence-Simulation

### Full Exploitation --> Privilege Escalation --> Persistence --> Defensive Cleanup
---

## Project Overview

This project demonstrates a real-world penetration testing scenario using the **Metasploit Framework**.

The project showcases a **complete attck chain** which includes:

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
- **Tools used**

      - Metasploit
      - Nmap
      - Bash
      - Meterpreter

#### **Target Machine**

- **Metasploitable 2**
- *It is intentionally vulnerable for research and has multiple outdated services which is perfect for real attack simulation.*

#### **Network Mode** (*used virtual box*)

- Both machines must have a shared network: `Bridged Adapter` or `Host-Only-Adapter`

## PHASE 1 -- Reconnaissance

Reconnaissance helps identify **open ports**, **running services**, and **potential vulnerabilities**.

This phase shows how real attackers gather intel before attcking.

1.1 **Discover Target IP**

`sudo netdiscover -r 192.168.1.0/24`

- This scans your local network range and discovers all the active hosts
- This will help identify the target machine

1.2 **Full Nmap Scan**

`nmap -A -T4 -p- 192.168.1.105 -oN nmap-scan.txt`

- **-A** --> Aggressive scan (OS detection, services, scripts)
- **-T4** --> Speed level
- **-p-** --> Scan all 65,535 ports
- **-oN** --> Save output to a file

***This gives a complete attack surface map***

