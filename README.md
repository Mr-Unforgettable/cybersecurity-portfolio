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
