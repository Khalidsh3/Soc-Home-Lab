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

## 🎯 What's Next? (Phase 2)
Now that the lab infrastructure is stable, tomorrow the exciting part begins:
* Editing the forwarder configurations (`inputs.conf`) to target specific Sysmon event streams.
* Launching live attack simulations from Kali Linux against the Windows 10 target.
* Hunting down those malicious traces in Splunk using SPL (Splunk Processing Language) and building custom detection rules!
