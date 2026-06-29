# Soc-Home-Lab
My personal SOC home lab enviroment for malware simulation and detection engineering.


# 🚀 SOC Home Lab: Malware Simulation & Detection Engineering

Welcome to my personal SOC Home Lab project! I started building this environment to get hands-on experience with how cyber attacks actually work from both sides—the attacker (Red Team) and the defender (Blue Team). It's one thing to study theories, but it's completely different when you build the infrastructure, configure the telemetry, and analyze the logs with your own hands.

---

## 📌 What is this project?
This lab is a fully controlled, enterprise-like environment designed to simulate real-world cyber attacks, monitor endpoint behavior under threat, and engineer custom detection rules inside a SIEM platform.

### 🏢 Network Layout & System Allocation
I set up the virtual machines using VirtualBox, ensuring balanced resource allocation to keep my host machine stable:
* SIEM / Central Management: Windows Server 2019 (6GB RAM) running Splunk Enterprise (acting as the brain of the lab).
* Target / Victim Endpoint: Windows 10 (2GB RAM) monitored via Microsoft Sysmon and shipping logs through Splunk Universal Forwarder.
* Attacker Machine: Kali Linux (2GB RAM) used to launch threat simulations.

---

## 🛠️ Phase 1: Infrastructure & Data Ingestion Setup (Day 1)
Today was all about building the foundation and making sure our "eyes and ears" are ready to record everything. Here is exactly what I did:

1. Deploying the SIEM: I set up Splunk Enterprise on the Windows Server 2019 machine. This will be the central dashboard where all event logs are collected, parsed, and searched.
2. Setting up Advanced Telemetry: To monitor what's happening deep inside the systems, I installed Microsoft Sysmon v15.21 on both the server and the Windows 10 endpoint. I utilized the well-known SwiftOnSecurity configuration file to make sure we capture high-fidelity events like process creations, network connections, and registry modifications.
3. Establishing the Log Pipeline: I installed the Splunk Universal Forwarder on the victim's Windows 10 machine. I configured it to establish a secure data shipping pipeline to send logs directly over to the Splunk Server on port 9997.
4. Testing & Verification: I verified that the core database is running properly by checking the internal audit logs (`index=_audit`). The environment is healthy and fully prepared to receive external log streams.

---

## Phase 2: Attack Simulation & Log Detection (SMB Brute Force)
---

  In this phase, I transitioned from setup to active simulation. I launched an authentication attack from the Kali Linux machine against the Windows 10 target and analyzed how the events were captured   inside Splunk Enterprise.
  
---

### 1. The Attack Scenario (Kali Linux)
  From the Kali Linux terminal, I used `smbclient` to simulate an unauthorized share enumeration/brute-force attempt using a non-existent account (`pito`):

```bash
smbclient -L //192.168.0.196 -U Pito
```


![image alt](https://github.com/Khalidsh3/Soc-Home-Lab/blob/72cf18754ad4da5740765ce32295e7c0162dbbcc/kali.png)

This generated an authentication failure on the target machine (NT_STATUS_LOGON_FAILURE).

---

### 2. The Defensive Challenge: Missing Logs (Troubleshooting)
  Initially, searching Splunk for index=* "pito" or EventCode=4625 yielded zero results.

  Root Cause Analysis: By default, standard Windows 10 installations do not have strict logon auditing enabled. The OS was throwing away the failure events instead of logging them.

  The Fix (Hardening the Endpoint):

  Opened Local Security Policy (secpol.msc) on the Windows 10 machine.

  Navigated to: Local Policies -> Audit Policy -> Audit logon events.

  Enabled auditing for Failure events.

---

### 3. Detection & Analysis (Splunk SIEM)
  After forcing Windows to audit logon failures, I re-ran the attack from Kali. Splunk immediately picked up the logs.

  Splunk Search Query used:
  Code snippet
  index=* source="WinEventLog:Security" EventCode=4625 "pito"
  Key Fields Captured & Analyzed:
  EventCode: 4625 (An account failed to log on).

  TargetUserName: pito (The brute-forced username).

  SourceNetworkAddress: [Kali_Linux_IP] (The explicit IP of the attacker machine).

  LogonType: 3 (Network logon, indicating the attack came over the network, standard for SMB/Shared services).
  ![image alt](https://github.com/Khalidsh3/Soc-Home-Lab/blob/e4058f167971acb5e56d3eeae824fda21fe3dfad/splunk.png)
  
---

### Phase 2 Takeaway 🧠
  As a SOC Analyst/Detection Engineer, visibility is everything. This phase proved that a SIEM is completely blind without proper Group Policies (GPOs) and Endpoint Auditing Configurations in place.     Hardening the infrastructure to log the right data is the first step of successful threat hunting.
