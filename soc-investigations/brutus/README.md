# Brutus — Linux SSH Attack Investigation

> Hands-on cybersecurity investigation focused on SSH authentication analysis, Linux log analysis, session artifact correlation, and post-compromise activity.

![Cybersecurity](https://img.shields.io/badge/Focus-Cybersecurity-red)
![Linux](https://img.shields.io/badge/Platform-Linux-black)
![Log%20Analysis](https://img.shields.io/badge/Skill-Log%20Analysis-blue)
![Incident%20Investigation](https://img.shields.io/badge/Skill-Incident%20Investigation-orange)
![Hack%20The%20Box](https://img.shields.io/badge/Lab-Hack%20The%20Box-green)

---

## 📌 Overview

This project documents my investigation of the **Brutus** security lab from Hack The Box.

The objective was to analyze Linux authentication and session artifacts, identify suspicious SSH activity, determine the source of the attack, correlate authentication events with terminal-session artifacts, and reconstruct the attacker's activity after gaining access.

Rather than relying on a single log source, I investigated the incident by progressively correlating:

```text
auth.log
   ↓
SSH authentication activity
   ↓
Attacker source IP
   ↓
Successful authentication
   ↓
utmp / wtmp session artifacts
   ↓
Post-compromise account activity
   ↓
Attack timeline
   ↓
MITRE ATT&CK analysis
```
## 🎯 Objectives
The investigation focused on:
- Identifying suspicious SSH authentication activity
- Detecting repeated failed authentication attempts
- Identifying the attacking source IP
- Determining which account was successfully compromised
- Distinguishing authentication time from terminal-session time
- Analyzing `utmp/wtmp` session artifacts
- Reconstructing the attack timeline
- Investigating post-compromise activity
- Identifying account creation and privilege assignment
- Mapping observed behavior to relevant MITRE ATT&CK technique
- Documenting the investigation as a professional security report

## 🧰 Environment & Tools
### Platform
- Hack The Box — Brutus Lab
- Linux

### Tools/Techniques
- Linux authentication logs
- grep
- awk
- less
- utmp
- wtmp
- Python
- MITRE ATT&CK
- Timeline analysis

# 🔎 Investigation
## 1. Initial Log Examination
The investigation started with the Linux authentication log:

```
auth.log
```
The initial goal was to reduce the amount of log data and focus specifically on SSH-related authentication events.

I used:
```
grep "sshd" auth.log
```

I also searched for common indicators of failed SSH authentication:
```
grep "Failed password" auth.log
```

Other useful indicators included:
```
Failed password
Invalid user
authentication failure
sshd
```

Filtering the log made it easier to identify repeated authentication attempts instead of manually reading the entire file.

## 2. Identifying Brute-Force Activity
The filtered authentication logs showed repeated failed SSH authentication attempts.

One of the most significant observations was the repeated activity originating from:
```
65.2.161.68
```

The attacker attempted authentication against multiple accounts, including:
```
admin
backup
server_adm
svc_account
root
```

Example investigation command:
```
grep "Failed password" auth.log
```

The repeated failed attempts against multiple accounts, followed by a later successful authentication from the same source, were consistent with SSH credential-guessing/brute-force activity.

## 3. Extracting Authentication Information
To investigate successful authentication events, I filtered for:
```
grep "Accepted" auth.log
```

I also used `awk` to extract fields from the structured log entries.

Example:
```
greap "Failed password" auth.log | awk '{print ${NF-3)}'
```
This helped identify the username associated with the failed authentication event.

### Understanding `awk`
Linux authentication logs are whitespace-separated, so `awk` can treat each section of a log entry as a field.

For example:
```
Mar 6 06:31:31 host sshd[1234]: Failed password for invalid user admin from 65.2.161.68 port 1234 ssh2
```

`awk` represents these components as fields:
```
$1
$2
$3
...
$NF
```

Where:
```
NF = number of fields
```

Using:
```
$(NF-3)
```

means selecting the field three positions before the final field.
This was useful for extracting information without relying on a fixed field number.

## 🔐 4. Identifying the Successful Compromise
After identifying the suspicious source IP, I investigated authentication events associated with it.

```
grep "65.2.161.68" auth.log
```

I then narrowed the results to successful and failed password events:
```
grep "65.2.161.68" auth.log | grep -E "Accepted|Failed password"
```

A successful root authentication was observed:
```
Mar 6 06:32:44 ... Accepted password for root from 65.2.161.68 ...
```

This was significant because the same IP had generated numerous failed authentication attempts shortly beforehand.

### Finding
The evidence indicates that:

```
Attacker IP: 65.2.161.68
Successful account: root
Authentication time: 2024-03-06 06:32:44 UTC
```

## 🕒 5. Authentication Time vs Session Time
One of the more important parts of the investigation was understanding that the authentication timestamps and the actual terminal-session timestamp were not identical.

The authentication log showed:
```
2024-03-06 06:32:44 UTC
```

for the successful root authentication.
However, the `utmp/wtmp` session artifact recorded the terminal session at:
```
2024-03-06 06:32:45 UTC
```

This distinction mattered because the lab question specifically asked for the time at which the attacker manually logged in and established a terminal session.

### Session Finding
```
Account:      root
Source IP:    65.2.161.68
Terminal:     pts/1
Session type: USER
Session time: 2024-03-06 06:32:45 UTC
```

### Final Answer
```
2024-03-06 06:32:45 UTC
```

## 🧩 6. Analyzing the utmp Artifact
The `utmp` artifact was binary rather than plain text.
Because it could not simply be read using normal text utilities, I investigated the supplied Python parser:
```
utmp.py
```

The parser reads the binary file in fixed-size records and extracts fields such as:
- Record type
- Process ID
- Terminal line
- User
- Host
- Timestamp
- IP address

The parser reads records in 384-byte blocks:
```
offset += 384
```

It then converts the binary values into readable information and writes the results as CSV.
This allowed the binary session artifact to be analyzed in structured format.

<img width="786" height="597" alt="Screenshot 2026-09-12 204251" src="https://github.com/user-attachments/assets/31862990-fe1e-4846-87d3-0f4b41392848" />

## 7. Correlating Authentication and Session Evidence
The investigation demonstrated the value of correlating multiple artifacts.

The authentication log showed:
```
06:32:44 — Accepted password for root
```

The session artifact showed:
```
06:32:45 — USER session for root on pts/1
```

This provided a stronger understanding of the attack than relying only on the authentication log.

The sequence was:
```
SSH authentication
       ↓
Successful root authentication
       ↓
Terminal session established
       ↓
Post-compromise activity
```

## 👤 8. Post-Compromise Account Activity
After obtaining root access, the attacker performed additional account-management activity.

The logs showed creation of:
```
cyberjunkie
```

The account was subsequently granted `sudo` privileges.

The relevant sequence was:
```
Root access
     ↓
Create cyberjunkie
     ↓
Grant sudo privileges
     ↓
Root session closes
     ↓
cyberjunkie logs in
     ↓
cyberjunkie uses sudo
```

The root session closed at approximately:
```
06:37:24 UTC
```

Shortly afterwards, the new account successfully authenticated:
```
06:37:34 UTC
```

from the same source IP:
```
65.2.161.68
```

## 🔑 9. Privileged Activity
After logging in as `cyberjunkie`, the account used `sudo` to execute privileged commands.

One observed command accessed:
```
/etc/shadow
```

The relevant behavior can be represented as:
```
cyberjunkie
     ↓
sudo
     ↓
root
     ↓
cat /etc/shadow
```

The investigation also observed a privileged `curl` command retriving:
```
https://raw.githubusercontent.com/montysecurity/linper/main/linper.sh
```
### Important Evidence Qualification
The investigation observed the retrieval and execution context of the script, but did not independently establish the contents or malicious nature of the script.

Therefore, I do not classify it as malware, spyware, keylogger, or an info. stealer based solely on this observation.

This distinction is important when documenting security findings:
> **Observed evidence should be separated from hypotheses about attacker intent.**

### 📅 10. Reconstructed Attack Timeline
| Time (UTC) | Event |
|---|---|
| 06:31:31 | Repeated SSH authentication activity begins from `65.2.161.68` |
| 06:31–06:32 | Multiple failed authentication attempts against several accounts |
| 06:32:44 | Successful authentication as `root` |
| 06:32:45 | Root terminal session recorded in `utmp/wtmp` |
| 06:34:18 | `cyberjunkie` account created |
| 06:35:15 | `cyberjunkie` added to `sudo` |
| 06:37:24 | Original root session disconnects |
| 06:37:34 | `cyberjunkie` successfully logs in |
| 06:37:57 | `cyberjunkie` executes privileged `cat /etc/shadow` |
| 06:39:38 | `cyberjunkie` executes privileged `curl` command |

## 🧠 11. Attack Analysis
Based on the available evidence, the attack sequence can be reconstructed as:

```
                  SSH ATTACK
                      │
                      ▼
          Repeated authentication
               attempts
                      │
                      ▼
             65.2.161.68
                      │
                      ▼
             Successful root
              authentication
                      │
                      ▼
             Root terminal
                session
                      │
                      ▼
          Create cyberjunkie
                      │
                      ▼
            Grant sudo access
                      │
                      ▼
           Root session closes
                      │
                      ▼
         cyberjunkie logs in
                      │
                      ▼
            sudo privileges
                      │
                      ▼
          Privileged activity
```

The strongest persistence-related observation is the creation of a secondary account followed by privilege assignment and subsequent successful use of that account after the original root session ended.

However, the investigation avoids claiming attacker intent beyond what the evidence supports.

## 🛡️ 12. MITRE ATT&CK Mapping
The following mappings are based on the observed behavior.
### [T1110 — Brute Force](https://attack.mitre.org/techniques/T1110/)
Repeated failed authentication attempts against multiple accounts, followed by successful authentication, are consistent with credential-guessing activity.

### [T1136.001 — Create Account: Local Account](https://attack.mitre.org/techniques/T1136/)
The attacker created a new local account:
```
cyberjunkie
```

This behavior is directly observable in the authentication logs.

### Account Manipulation
The attacker modified the privileges associated with the newly created account by adding it to the `sudo` group.

This increased the account's ability to perform privileged operations.

### Valid Accounts
The attacker subsequently authenticated using the newly created `cyberjunkie` account.

This account was then used to perform privileged actions through `sudo`.

## 📊 13. Key Findings
### Finding 1 — Suspicious SSH activity
Repeated SSH authentication attempts were observed from:
```
65.2.161.68
```

### Finding 2 — Successful root authentication
The suspicious source successfully authenticated as:
```
root
```

at:
```
2024-03-06 06:32:44 UTC
```

### Finding 3 — Terminal session established
The corresponding terminal session was recorded at:
```
2024-03-06 06:32:45 UTC
```

### Finding 4 — Secondary privileged account created
The attacker created:
```
cyberjunkie
```

and subsequently granted the account sudo privileges.

### Finding 5 — Secondary account used after root session ended
After the root session closed, cyberjunkie successfully logged in from
the same source IP.

### Finding 6 — Privileged activity observed
The cyberjunkie account used sudo to access:
```
/etc/shadow
```
and later retrieved a shell script using curl.

## 🔧 14. Defensive Recommendations
Based on the observed attack pattern, a defender could consider:
SSH Hardening
- Disable password-based SSH authentication where practical.
- Prefer SSH keys.
- Disable direct root SSH login where appropriate.
- Restrict SSH access to trusted networks or VPNs.
- Implement rate limiting or controls against repeated authentication attempts.

### Account Monitoring
Monitor for:
```
useradd
adduser
usermod
groupadd
```

and changes involving privileged groups such as:
```
sudo
```

### Authentication Monitoring
Alert on:
- High volumes of failed SSH authentication
- Multiple invalid usernames from one source
- Successful login following numerous failures
- Root authentication from unusual sources
- New privileged account creation

### Privilege Monitoring
Monitor:
- Changes to sudo privileges
- New members of administrative groups
- Suspicious use of `sudo`
- Access to sensitive files such as `/etc/shadow`

### Log Correlation
A stronger SOC workflow should correlate:

```
Authentication logs
        +
Session artifacts
        +
Account-management events
        +
Privilege events
```

rather than analyzing each artifact independently.

## 🧪 15. Investigation Techniques Demonstrated
Through this investigation I practiced:
Linux
- Authentication log analysis
- Linux user/account activity
- Privilege and sudo activity
- `utmp/wtmp` artifacts

### Command Line
```
grep
awk
less
```

### Digital Forensics
- Binary artifact analysis
- Timestamp correlation
- Session reconstruction
- Evidence-based timeline creation

### Security Analysis
- SSH attack identification
- Brute-force analysis
- Compromise identification
- Post-compromise activity analysis
- MITRE ATT&CK mapping

### Documentation
- Investigation methodology
- Evidence-based findings
- Timeline reconstruction
- Defensive recommendations

## 📁 Evidence & Documentation
The detailed investigation has been documented separately as a PDF report.
The report contains:
- Investigation methodology
- Relevant commands
- Log analysis
- Session artifact analysis
- Evidence
- Timeline
- Attack analysis
- MITRE ATT&CK mapping
- Defensive recommendations
Full Report: `report.pdf`

## 💡 What This Investigation Taught Me
The most valuable part of this lab was not simply finding the final
answer.
The investigation required progressively reducing noisy data,
understanding Linux log structure, forming hypotheses, testing those
hypotheses against evidence, and correlating information from different
artifacts.
One particularly useful lesson was understanding the difference between:

```
Authentication time
        ≠
Terminal session time
```

The investigation also reinforced the importance of distinguishing:
```
Observed evidence
        from
Interpretation
        from
Hypothesis
```

For example, the logs establish that a new account was created, given
sudo privileges, and subsequently used. Calling that behavior a
"backdoor" would require additional evidence about the attacker's
intent and mechanism.

## 🎯 Career Evidence
This project is part of my ongoing hands-on cybersecurity learning and
career development.
Rather than treating individual labs as isolated achievements, I am
using them to build evidence of progressively developing capability in:

```
Linux
  ↓
Security Investigation
  ↓
Log Analysis
  ↓
Network / Host Forensics
  ↓
SOC Analysis
  ↓
Incident Response
  ↓
Threat Detection
```

The goal is to build practical capability through repeated
investigation, documentation, experimentation, and evidence-based
analysis.

## 📌 Project Status
- [X] **Status**
- [X] **Investigation**
- [X] **Detailed Report**
- [X] **Case Study**
- [X] **Portfolio Evidence**

## Disclaimer
This investigation was performed against an authorized Hack The Box
training environment.
All techniques and analysis documented here are intended for
educational, defensive, and authorized security testing purposes.
