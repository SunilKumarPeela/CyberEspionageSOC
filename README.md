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

We queried the system to see if the suspicious game `DeTankWar` was installed on the device belonging to James Douglas (`UB9I-DESKTOP`).

```kql
FileCreationEvents
| where hostname == "UB9I-DESKTOP"
| where filename has "DeTankWar"
```
✅ Result: Malicious game file was found on the host.

SHA256 for this file found to be :

56554117d96d12bd3504ebef2a8f28e790dd1fe583c33ad58ccbf614313ead8c
---
