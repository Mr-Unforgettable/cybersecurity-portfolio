# 👋 Hi, I'm Abhinav Pratap Singh

### Cybersecurity | Security Investigation | Software Engineering

> **Building cybersecurity capability through hands-on investigation, experimentation, and real technical evidence.**

I'm a Computer Science graduate with a cybersecurity specialization and a background in backend/software engineering.

I'm currently making a deliberate transition deeper into cybersecurity by working through hands-on security labs, SOC investigations, network forensics, Linux analysis, and security-focused projects.

I'm less interested in collecting certificates and more interested in being able to answer:

**"Can I actually investigate this problem and explain what I found?"**

---

## 🧭 What I'm Building

I'm building a practical cybersecurity portfolio around four areas:

```text
             CYBERSECURITY
                   │
       ┌───────────┼───────────┐
       │           │           │
    INVESTIGATE   DETECT     UNDERSTAND
       │           │           │
       ▼           ▼           ▼
   Log Analysis   SIEM      Attack Analysis
   Forensics      Rules     MITRE ATT&CK
   Timelines      Alerts    Incident Response
       │           │           │
       └───────────┴───────────┘
                   │
                   ▼
            SECURITY PROJECTS
```

##🔍 Cybersecurity Investigations

## 🖥️ Brutus — Linux SSH Attack Investigation
Focus: Linux Log Analysis · SSH · Incident Investigation · utmp/wtmp · Timeline Reconstruction
Investigated a simulated SSH compromise by analyzing authentication logs and Linux session artifacts.
What I investigated
- SSH brute-force activity
- Suspicious source IP identification
- Successful root authentication
- utmp/wtmp session artifacts
- Authentication vs. terminal-session timestamps
- Post-compromise account creation
- Sudo privilege assignment
- Privileged activity
- MITRE ATT&CK mapping

### Investigation path
```
Raw authentication logs
        ↓
SSH activity filtering
        ↓
Brute-force identification
        ↓
Attacker IP correlation
        ↓
Successful root authentication
        ↓
utmp/wtmp analysis
        ↓
Session correlation
        ↓
Attack timeline
        ↓
Post-compromise analysis
```

📂 [View the Brutus Investigation →](./soc-investigations/brutus/)

## 🌐 HawkEye — Network Forensics
Focus: Network Forensics · Wireshark · PCAP Analysis · IOC Extraction
Hands-on investigation of a simulated HawkEye keylogger data-exfiltration incident using network traffic analysis.
Currently exploring
- PCAP analysis
- DNS investigation
- HTTP traffic
- Network endpoints
- Malicious file extraction
- Hash identification
- Credential / exfiltration analysis
- Indicators of compromise

📂 [View the HawkEye Investigation →](./soc-investigations/hawkeye/)

## 🛡️ SOC & Detection Engineering
I'm currently extending these investigations into a broader SOC-focused project involving:

### SIEM
- ELK
- Security Onion
- Log collection
- Event correlation
- Detection rules
- Security dashboards
- 
### Detection
Working toward detection logic for:
```
SSH brute force
Credential attacks
DNS tunneling
PowerShell abuse
Privilege escalation
Data exfiltration
Incident Response
```

### Exploring the workflow:
```
Detect
  ↓
Investigate
  ↓
Correlate
  ↓
Scope
  ↓
Identify IOCs
  ↓
Build Timeline
  ↓
Respond
  ↓
Document
```

## 🧪 Hands-On Learning
I learn best by investigating problems rather than simply consuming tutorials.
Current practice includes:
| Area | Hands-on Work |
|---|---|
| 🐧 Linux | Permissions, SUID/SGID, processes, logs |
| 🔎 Log Analysis | SSH/authentication investigation |
| 🌐 Network Forensics | PCAP analysis with Wireshark |
| 🛡️ SOC | SIEM, detection rules, dashboards |
| 🚨 Incident Response | Timeline and attacker-behavior analysis |
| 🧠 Threat Analysis | MITRE ATT&CK mapping |
| 💻 Secure Development | Authentication & access-control analysis |
| ☁️ Cloud Security | AWS security fundamentals |


## 🧰 Technical Toolkit
Security
Linux Wireshark SIEM Network Forensics
Vulnerability Assessment Security Analysis
MITRE ATT&CK Incident Investigation
Development
TypeScript JavaScript Python
Node.js Express REST APIs
Databases
MySQL MongoDB MSSQL SingleStore
Infrastructure & Tools
Git Docker VMware AWS
JMeter NeoLoad

## 💻 Software Engineering Background
Before focusing more deeply on cybersecurity, I worked in backend/software engineering.
Programmer Analyst — Cognizant
Worked on backend performance analysis and enterprise production systems.
Some of the work involved:
- Performance testing
- JMeter
- NeoLoad
- High-concurrency simulation
- Root-cause analysis
- Backend systems
This background influences how I approach security:
Understand the system first. Then investigate how it can fail or be abused.

## 🔐 Security Experience
Cybersecurity Internship — Virtually Testing Foundation
Hands-on vulnerability analysis across security labs and isolated VMware/AWS environments.
Experience included:
- Vulnerability analysis
- Security lab investigation
- Isolated environments
- Findings documentation

## 📚 Certifications & Learning
- AWS Educate — Security
- Virtually Testing Foundation — Cybersecurity Internship
- Data Structures & Algorithms
- Ongoing hands-on security labs

## 📂 Portfolio Structure
This repository is intentionally organized around evidence of practical work, rather than simply listing technologies.
```
.
├── soc-investigations/
│   ├── brutus/
│   │   ├── README.md
│   │   └── report.pdf
│   │
│   └── hawkeye/
│       ├── README.md
│       └── report.pdf
│
├── siem/
│   ├── detection-rules/
│   └── dashboards/
│
└── projects/
    └── ...
Each investigation aims to contain:
Investigation
     +
Evidence
     +
Reasoning
     +
Report
     +
Lessons Learned
```

## 🎯 Career Direction
I'm currently exploring cybersecurity roles where I can combine my software-engineering background with security investigation and defensive security work.
Areas I'm particularly interested in:
- SOC / Security Operations
- Security Analysis
- Detection Engineering
- Incident Response
- Vulnerability Assessment
- Application Security
- Cloud Security
I'm deliberately using hands-on work to discover which of these areas I want to specialize in rather than choosing a specialization purely from job titles.

## 📈 Current Focus
- [████████░░] Cybersecurity Foundations
- [██████░░░░] SOC / Detection
- [██████░░░░] Network Forensics
- [████░░░░░░] Incident Response
- [████░░░░░░] SIEM Engineering
- [████░░░░░░] Cloud Security
These bars represent my current learning focus, not claimed proficiency.

## 🧠 My Learning Philosophy
I don't want to optimize for looking knowledgeable.
I want to optimize for becoming capable.
When I don't know something, the goal is:
"I don't know."
      ↓
"My current hypothesis is..."
      ↓
"How can I test it?"
      ↓
"What's the evidence?"
      ↓
"Does the evidence support my hypothesis?"
      ↓
"Update my understanding."
That mindset is becoming the foundation of how I approach cybersecurity.

## 🚀 What's Next
The current roadmap is intentionally hands-on:
```
✅ Linux / Security Fundamentals
        ↓
✅ Brutus — SSH Investigation
        ↓
🔄 HawkEye — Network Forensics
        ↓
🔄 SIEM Correlation Rules
        ↓
🔄 SOC Dashboard
        ↓
🔄 APT Simulation
        ↓
🔄 Incident Response & Threat Intelligence
        ↓
🎯 Build a stronger cybersecurity portfolio
```

The objective isn't to rush through the list.
The objective is to turn each stage into real technical evidence.

## 📫 Connect
LinkedIn: Abhinav Pratap Singh
GitHub: Mr-Unforgettable
<p align="center">

Building. Investigating. Learning. Improving.
One security problem at a time.
</p>
```
