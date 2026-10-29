# SOC Analyst Home Lab (Splunk)

A home lab I built to learn how a SOC analyst investigates security events in a SIEM. I used two physical PCs: one runs Splunk, the other is a Windows endpoint I watch. I create test activity on the endpoint, look for it in Splunk, and write a short incident report for each one.

> I'm new to security and built this to practice real skills: searching logs, spotting suspicious activity, and writing it up clearly.

---

## My Setup

| Role | Hostname | IP |
|---|---|---|
| Splunk server | `JayPC` | 192.168.50.142 |
| Windows endpoint | `WIN-ENDPOINT-01` | 192.168.50.215 |

- The endpoint runs **Sysmon** (logs detailed activity) and a **Splunk forwarder** that sends its logs to the Splunk server.
- The server collects the logs so I can search them.

```
 WIN-ENDPOINT-01  ──(sends logs)──▶  JayPC (Splunk)
 192.168.50.215                      192.168.50.142
```

---

## What I Investigated

For each one I made the activity happen on the endpoint, found it in Splunk, and wrote a report.

### 1. New Admin Account Created
A new user account was created and added to the Administrators group, then deleted. I found it using Windows Security events and wrote up what happened.
**MITRE ATT&CK:** T1136.001 – Create Account

### 2. Failed Login Attempts (Brute Force)
Several failed logins happened in a short time. I counted them in Splunk to spot the pattern.
**MITRE ATT&CK:** T1110 – Brute Force

### 3. DNS Lookups
I looked at which programs were making DNS lookups and to what domains, to practice spotting unusual ones.
**MITRE ATT&CK:** T1071 – Application Layer Protocol

---

## Searches I Used

Simple Splunk searches, one per investigation:

**New admin account:**
```
index=* host=WIN-ENDPOINT-01 (EventCode=4720 OR EventCode=4732 OR EventCode=4726)
```

**Failed logins:**
```
index=* host=WIN-ENDPOINT-01 EventCode=4625
| stats count by Account_Name
```

**DNS lookups:**
```
index=* host=WIN-ENDPOINT-01 source="*Sysmon*" "<EventID>22</EventID>" google.com
```

---

## Useful Event IDs I Learned

- **4624** – successful login
- **4625** – failed login
- **4720** – new user account created
- **4732** – user added to a group
- **4726** – user account deleted
- **Sysmon EventID 22** – a DNS lookup

---

## What's in This Repo

```
soc-analyst-home-lab/
├── README.md
├── 01-account-creation.md
├── 02-failed-logins.md
├── 03-dns-lookups.md
```

---

## What I Practiced

- Searching logs in Splunk
- Spotting suspicious activity (new admin accounts, failed logins, DNS lookups)
- Writing clear, simple incident reports
- Learning common Windows Event IDs and MITRE ATT&CK basics
