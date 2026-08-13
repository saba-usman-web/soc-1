# SOC Analyst Level 1 — Splunk Investigation Labs

## Overview

This repository contains a collection of **SOC Analyst Level 1 hands-on investigation labs** completed using **Splunk** and simulated security telemetry.

The goal of these projects was to practice the workflow a Tier 1 SOC analyst would use when responding to security alerts:

> **Alert → Triage → Search → Correlation → Investigation → IOC Identification → Verdict → Escalation / Response Recommendation**

The lab data includes Windows Security logs, Sysmon telemetry, PowerShell logs, cloud authentication events, DNS/network activity, web application telemetry, and endpoint file/process activity.

These projects were completed as practical exercises to build experience with **Splunk, SPL, log analysis, authentication investigations, endpoint investigations, IOC identification, and SOC ticket handling**.

---

## Skills Demonstrated

- Splunk log ingestion and investigation
- SPL-based threat hunting
- Windows Security Event Log analysis
- Windows Event ID 4624 / 4625 investigation
- Authentication failure analysis
- Brute-force detection
- Privileged/local administrator account investigation
- Successful-login correlation
- Impossible-travel analysis
- User login baseline comparison
- Source IP identification
- PowerShell investigation
- Base64/encoded command analysis
- Process and parent-process investigation
- Ransomware behavior investigation
- File rename activity analysis
- Web application attack investigation
- SQL injection investigation
- IOC extraction
- Event timeline reconstruction
- False-positive vs. true-positive assessment
- SOC escalation and response recommendations
- Basic MITRE ATT&CK technique mapping

---

# Lab Portfolio

| Lab | Investigation | Primary Focus | Outcome |
|---|---|---|---|
| Ticket 1 | Impossible Travel — Cloud Identity | Geolocation anomaly / identity investigation | False Positive |
| Ticket 2 | Brute Force — Local Admin | Failed authentication against privileged account | True Positive / Escalate |
| Ticket 3 | Suspicious Encoded PowerShell | Encoded command + IOC extraction | True Positive |
| Ticket 4 | Ransomware Activity | Bulk file rename behavior | True Positive |
| Ticket 5 | SQL Injection | Suspicious web requests + source identification | True Positive |
| Ticket 6 | Impossible Travel — Baseline Deviation | User baseline + suspicious source IP | True Positive |
| Ticket 7 | Ransomware Activity — Process Lineage | Process + parent-process correlation | True Positive |
| Ticket 8 | Brute Force Followed by Successful Login | Authentication sequence + post-authentication activity | Compromise investigation |

---

# 1. Impossible Travel — Cloud Identity

### Scenario

Cloud identity telemetry generated an impossible-travel alert for:

`j.smith@company.local`

The alert indicated geographically inconsistent sign-in activity occurring within a short period.

### Investigation Approach

The investigation focused on:

1. Reviewing the user's recent sign-in activity.
2. Comparing the geographic locations of authentication events.
3. Reviewing session continuity.
4. Determining whether the activity represented two physically impossible locations or a benign network-routing condition.
5. Assessing the alert as either a true positive or false positive.

### Finding

**Verdict: False Positive**

The available session evidence was more consistent with a benign network-path condition rather than two separate malicious sessions.

Possible benign explanations considered during the investigation included:

- VPN activity
- Proxy activity
- Mobile carrier routing
- Benign routed traffic

### SOC Lesson

Impossible-travel alerts should not automatically be treated as account compromise. A SOC analyst should correlate authentication events, session continuity, network routing, device information, and other available telemetry before escalating.

**Files:**

- `ticket1/ticket1.html`
- `ticket1/ticket-1-logs.json`

---

# 2. Brute Force — Local Administrator

### Scenario

Security telemetry showed repeated failed authentication attempts against a privileged local account on an endpoint.

### Investigation Approach

The investigation focused on Windows Security authentication events, particularly:

`Event ID 4625 — Failed Logon`

The investigation included:

1. Searching for failed authentication events.
2. Reviewing the targeted account.
3. Identifying whether the account was privileged.
4. Reviewing the authentication pattern.
5. Determining whether the activity required escalation.

### Finding

**Targeted Account: `Administrator`**

The repeated failed authentication attempts against a privileged local account were treated as suspicious and appropriate for escalation.

### SOC Lesson

Repeated authentication failures against privileged accounts deserve increased scrutiny because successful credential guessing could provide elevated access.

**Files:**

- `ticket2/ticket2.html`
- `ticket2/ticket-2-logs.json`

---

# 3. Suspicious Encoded PowerShell

### Scenario

Endpoint telemetry indicated that a browser process spawned PowerShell, followed shortly afterward by execution of an encoded command.

### Investigation Approach

The investigation focused on:

- PowerShell Event ID 4104
- Process execution
- Encoded command content
- Base64 decoding
- Suspicious destination information
- IOC extraction

The encoded PowerShell command was decoded using Unicode/Base64 decoding.

### Finding

**Extracted IOC:**

`198.51.100.55`

The activity was treated as suspicious and consistent with malicious PowerShell execution.

### SOC Lesson

Encoded PowerShell commands can hide malicious instructions from simple string-based detection. A SOC analyst should pivot from the alert to the underlying PowerShell event, decode suspicious content, and extract actionable IOCs.

**Files:**

- `ticket3/ticket3.html`
- `ticket3/ticket-3-logs.json`

---

# 4. Ransomware Activity — Bulk File Renames

### Scenario

Endpoint telemetry detected suspicious bulk file rename behavior consistent with ransomware activity.

### Investigation Approach

The investigation focused on:

1. Reviewing endpoint file activity.
2. Comparing original and renamed file paths.
3. Identifying repeated naming patterns.
4. Determining whether a common extension was appended.
5. Assessing the event as malicious or benign.

### Finding

**Observed appended extension:**

`.lockedx`

**Verdict: True Positive**

The repeated file-renaming behavior was consistent with ransomware-style activity.

### SOC Lesson

Mass file modification or rename activity across many documents in a short time period can be a strong ransomware indicator. Analysts should correlate file activity with process execution and endpoint telemetry to identify the responsible process and determine scope.

**Files:**

- `ticket4/ticket4.html`
- `ticket4/ticket-4-logs.json`

---

# 5. SQL Injection Investigation

### Scenario

Application telemetry detected suspicious request patterns and related server-side errors against a public-facing web application.

### Investigation Approach

The investigation focused on:

- Suspicious HTTP requests
- Repeated parameter manipulation
- SQL injection indicators
- URL-encoded characters
- Application/server errors
- Source IP correlation

Examples of characters/patterns considered during the investigation included URL-encoded values such as:

`%27`

`%20`

`%3D`

### Finding

**Responsible Source IP:**

`203.0.113.57`

**Verdict: True Positive**

The request pattern was consistent with SQL injection activity against the application.

### SOC Lesson

Web application investigations require correlation between request parameters, source IPs, HTTP behavior, and application errors. URL encoding can hide attack strings, so analysts should inspect both raw and encoded representations.

**Files:**

- `ticket5/ticket5.html`
- `ticket5/ticket-5-logs.json`

---

# 6. Impossible Travel — Baseline Deviation

### Scenario

Cloud identity telemetry detected a high-risk impossible-travel pattern for:

`a.vance@company.local`

The investigation required establishing the user's normal login behavior and identifying the suspicious source involved in the alert.

### Investigation Approach

The investigation focused on:

1. Reviewing recent sign-in history.
2. Establishing the user's normal authentication pattern.
3. Comparing locations and timestamps.
4. Reviewing device information.
5. Checking MFA results and risk indicators.
6. Identifying the source IP responsible for the suspicious activity.

### Finding

**Suspicious Source IP:**

`86.105.18.42`

The event represented a significant deviation from the user's normal authentication pattern and was treated as a high-risk identity event.

### SOC Lesson

An impossible-travel alert becomes much more useful when combined with a user baseline. Analysts should ask:

- Where does the user normally log in?
- What devices do they normally use?
- What IP ranges are normal?
- Did MFA succeed?
- Is the device trusted?
- Does the new event materially deviate from the baseline?

**Files:**

- `ticket6/ticket6.html`
- `ticket6/ticket-6-logs.json`

---

# 7. Ransomware Activity — Process Lineage

### Scenario

Endpoint telemetry indicated ransomware-like activity on a monitored workstation.

The investigation required correlating process and file activity to determine the malicious process and its parent process.

### Investigation Approach

The investigation focused on:

- Process creation events
- File activity
- Process lineage
- Parent/child relationships
- Suspicious executable names
- Correlation between execution and file modification

### Finding

**Malicious Process:**

`invoice_viewer.exe`

**Parent Process:**

`explorer.exe`

**Verdict: True Positive**

The process and file activity formed a coherent ransomware-like execution chain.

### SOC Lesson

Process lineage is an important part of endpoint investigation. Identifying only a suspicious executable is not enough; analysts should determine:

> **Who launched it? → What did it execute? → What did it modify? → What happened afterward?**

**Files:**

- `ticket7/ticket7.html`
- `ticket7/ticket-4-logs.json`
- `ticket7/note-for-logs.txt`

---

# 8. Brute Force Followed by Successful Login

### Scenario

**Incident ID:** `INC-20260810-0147`

A SOC correlation rule generated an alert for excessive failed logons against a Windows host.

**Severity:** High

**Affected Host:** `WIN-DC01`

**Affected User:** `jdoe`

**Investigation Window:**

`2026-08-10 15:12:00–15:25:00 +08:00`

### Mission

The objective was to determine whether the activity represented:

- Normal user behavior
- Authentication failure noise
- Password guessing/brute force
- A successful account compromise

The investigation also required checking what happened **after** the successful authentication.

### Investigation Approach

The investigation followed a realistic SOC correlation workflow:

1. Start with the excessive-failed-logon alert.
2. Search Windows Security logs.
3. Identify repeated failed authentication events.
4. Pivot by username.
5. Pivot by source IP.
6. Identify the affected host.
7. Look for a successful authentication following the failures.
8. Correlate the successful login with network/share activity.
9. Determine whether the account was subsequently used to access another resource.
10. Assess whether the sequence was consistent with credential compromise.

### MITRE ATT&CK Concepts Practiced

The lab covers concepts associated with:

- **T1110 — Brute Force**
- **T1110.001 — Password Guessing**
- **T1078 — Valid Accounts**
- SMB/network share activity
- Authentication correlation

### SOC Lesson

A series of failed logons should not always be closed as harmless authentication noise.

A stronger detection sequence is:

```text
Multiple Failed Logons
        ↓
Same User / Source
        ↓
Successful Authentication
        ↓
Post-Authentication Activity
        ↓
Possible Account Compromise
```

This is an important SOC L1 investigation pattern because the successful authentication can materially change the severity of the incident.

**Files:**

- `ticket8-ai/SOC_L1_Brute_Force_Successful_Login.html`
- `ticket8-ai/SOC_L1_Brute_Force_Successful_Login.jsonl`
- `ticket8-ai/SOC_L1_Brute_Force_Successful_Login_manifest.txt`

---

# Splunk Investigation Methodology

Across these labs, I practiced a repeatable investigation methodology rather than relying on a single search.

## 1. Start Broad

Begin with the relevant index and time range.

Example:

```spl
index=soc
```

Then narrow the search based on the alert.

## 2. Identify Relevant Event Types

For Windows authentication investigations:

```spl
index=soc EventCode=4625
```

Failed authentication:

```spl
index=soc EventCode=4625 action=failure
```

Successful authentication:

```spl
index=soc EventCode=4624
```

## 3. Pivot on Important Fields

Common investigation pivots included:

```text
user
src_ip
host
EventCode
action
process
parent_process
CommandLine
Image
ParentImage
```

## 4. Build a Timeline

Instead of analyzing events individually, correlate them chronologically:

```text
Alert
 ↓
Initial Event
 ↓
Related Events
 ↓
Authentication / Process Activity
 ↓
Post-Event Activity
 ↓
IOC
 ↓
Final Assessment
```

## 5. Determine the Verdict

The final assessment should be evidence-based:

- True Positive
- False Positive
- Benign / Expected Activity
- Requires Escalation
- Potential Compromise

---

# IOC Examples Identified During the Labs

| Investigation | IOC / Key Finding |
|---|---|
| Impossible Travel — Baseline | `86.105.18.42` |
| Encoded PowerShell | `198.51.100.55` |
| SQL Injection | `203.0.113.57` |
| Ransomware File Activity | `.lockedx` |
| Ransomware Process Lineage | `invoice_viewer.exe` |
| Ransomware Parent Process | `explorer.exe` |
| Local Admin Brute Force | `Administrator` |

> The IP addresses in these labs are simulated/documentation values and should not be interpreted as evidence of activity against real systems.

---

# Repository Structure

```text
.
├── ticket1/
│   ├── ticket1.html
│   └── ticket-1-logs.json
│
├── ticket2/
│   ├── ticket2.html
│   └── ticket-2-logs.json
│
├── ticket3/
│   ├── ticket3.html
│   └── ticket-3-logs.json
│
├── ticket4/
│   ├── ticket4.html
│   └── ticket-4-logs.json
│
├── ticket5/
│   ├── ticket5.html
│   └── ticket-5-logs.json
│
├── ticket6/
│   ├── ticket6.html
│   └── ticket-6-logs.json
│
├── ticket7/
│   ├── ticket7.html
│   ├── ticket-4-logs.json
│   └── note-for-logs.txt
│
└── ticket8-ai/
    ├── SOC_L1_Brute_Force_Successful_Login.html
    ├── SOC_L1_Brute_Force_Successful_Login.jsonl
    └── SOC_L1_Brute_Force_Successful_Login_manifest.txt
```

---

# What I Practiced as a SOC Analyst

These labs helped me practice the complete lifecycle of a Tier 1 SOC investigation:

### Alert Triage
Understanding the alert, severity, affected user/host, and investigation window.

### Log Analysis
Reading Windows Security, Sysmon, PowerShell, cloud authentication, DNS, endpoint, and application telemetry.

### Threat Hunting
Using Splunk searches to pivot through relevant fields and identify related events.

### Correlation
Connecting authentication, process, file, network, and application events to reconstruct an attack sequence.

### IOC Identification
Extracting suspicious IP addresses, usernames, file extensions, processes, and other indicators.

### False Positive Analysis
Understanding that alerts such as impossible travel require contextual investigation before escalation.

### Incident Escalation
Determining when activity should be escalated based on privilege level, authentication behavior, successful access, and post-authentication activity.

---

# Key Takeaway

The main objective of these projects was not simply to find a matching value in a log.

The focus was on developing the mindset of a SOC analyst:

> **Don't investigate events in isolation. Build the story.**

A strong SOC investigation connects:

```text
WHO
 ↓
WHAT
 ↓
WHEN
 ↓
WHERE
 ↓
HOW
 ↓
WHAT HAPPENED NEXT
 ↓
IS IT MALICIOUS?
 ↓
WHAT SHOULD THE SOC DO?
```

These projects demonstrate practical experience with **Splunk-based alert investigation, Windows telemetry, authentication analysis, endpoint investigation, cloud identity monitoring, web attack analysis, IOC extraction, and evidence-based SOC decision making.**

---

## Disclaimer

These are **educational/lab environments** created for cybersecurity training and SOC Analyst practice. The logs and indicators are simulated and should not be interpreted as evidence of real-world malicious activity.

---

## Author

**SOC Analyst Level 1 Portfolio**

Hands-on practice focused on:

`Splunk` • `SPL` • `Windows Security Logs` • `Sysmon` • `PowerShell` • `Authentication Monitoring` • `Threat Hunting` • `IOC Analysis` • `Incident Triage` • `MITRE ATT&CK`
