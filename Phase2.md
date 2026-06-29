## Phase 2: Attack Simulation & Log Detection (SMB Brute Force)

In this phase, I transitioned from setup to active simulation. I launched an authentication attack from the Kali Linux machine against the Windows 10 target and analyzed how the events were captured inside Splunk Enterprise.

### 1. The Attack Scenario (Kali Linux)
From the Kali Linux terminal, I used `smbclient` to simulate an unauthorized share enumeration/brute-force attempt using a non-existent account (`pito`):

```bash
smbclient -L //192.168.0.196 -U Pito
```

This generated an authentication failure on the target machine (NT_STATUS_LOGON_FAILURE).

2. The Defensive Challenge: Missing Logs (Troubleshooting)
Initially, searching Splunk for index=* "pito" or EventCode=4625 yielded zero results.

Root Cause Analysis: By default, standard Windows 10 installations do not have strict logon auditing enabled. The OS was throwing away the failure events instead of logging them.

The Fix (Hardening the Endpoint):

Opened Local Security Policy (secpol.msc) on the Windows 10 machine.

Navigated to: Local Policies -> Audit Policy -> Audit logon events.

Enabled auditing for Failure events.

3. Detection & Analysis (Splunk SIEM)
After forcing Windows to audit logon failures, I re-ran the attack from Kali. Splunk immediately picked up the logs.

Splunk Search Query used:
Code snippet
index=* source="WinEventLog:Security" EventCode=4625 "pito"
Key Fields Captured & Analyzed:
EventCode: 4625 (An account failed to log on).

TargetUserName: pito (The brute-forced username).

SourceNetworkAddress: [Kali_Linux_IP] (The explicit IP of the attacker machine).

LogonType: 3 (Network logon, indicating the attack came over the network, standard for SMB/Shared services).

Phase 2 Takeaway 🧠
As a SOC Analyst/Detection Engineer, visibility is everything. This phase proved that a SIEM is completely blind without proper Group Policies (GPOs) and Endpoint Auditing Configurations in place. Hardening the infrastructure to log the right data is the first step of successful threat hunting.
