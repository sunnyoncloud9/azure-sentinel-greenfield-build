# 🛡️ Sentinel Home Lab — Greenfield SIEM Build

[![Microsoft Sentinel](https://img.shields.io/badge/Microsoft-Sentinel-blue.svg)](https://azure.microsoft.com/en-us/products/microsoft-sentinel)
[![KQL](https://img.shields.io/badge/Language-KQL-purple.svg)](https://learn.microsoft.com/en-us/kusto/query/)
[![MITRE ATT&CK](https://img.shields.io/badge/MITRE-ATT%26CK-red.svg)](https://attack.mitre.org/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

> A production-grade Microsoft Sentinel deployment built from the ground up — detection engineering, SOAR automation, threat hunting, and MITRE ATT&CK coverage in a fully documented home lab.

---

## 📌 Overview

This project demonstrates a **greenfield Sentinel build** — from data ingestion through detection engineering to automated incident response. Every component is built with production patterns and deployed in a real Azure environment, not just theory.

It directly mirrors what enterprise security teams build when standing up a new SIEM platform.

---

## 🎯 What's Included

| Component | Description |
|-----------|-------------|
| **5 Analytics Rules** | KQL detection rules mapped to MITRE ATT&CK |
| **SOAR Playbook** | Logic App auto-enriches alerts with VirusTotal + posts to Teams |
| **Threat Hunting Queries** | 5 hypothesis-driven hunting queries for proactive detection |
| **MITRE Coverage Workbook** | Visual dashboard of detection coverage |
| **Custom Log Parser** | Normalizes auth events across Azure AD, Windows, AWS |
| **Deployment Guide** | Step-by-step Azure setup with screenshots |

---

## 🗺️ Architecture

```
┌──────────────────────────────────────────────────────────┐
│                  DATA INGESTION LAYER                    │
│  Azure AD  •  Azure Activity  •  Defender for Cloud     │
└──────────────────────────────────────────────────────────┘
                          │
                          ▼
┌──────────────────────────────────────────────────────────┐
│                LOG NORMALIZATION (ASIM)                  │
│  Custom KQL parser → Common auth schema                  │
└──────────────────────────────────────────────────────────┘
                          │
                          ▼
┌──────────────────────────────────────────────────────────┐
│              DETECTION ENGINEERING LAYER                 │
│  5 KQL Analytics Rules → Mapped to MITRE ATT&CK         │
└──────────────────────────────────────────────────────────┘
                          │
                          ▼
┌──────────────────────────────────────────────────────────┐
│               INCIDENT TRIAGE & SOAR                     │
│  Logic App → VirusTotal enrichment → Teams notification │
└──────────────────────────────────────────────────────────┘
                          │
                          ▼
┌──────────────────────────────────────────────────────────┐
│              THREAT HUNTING & VISUALIZATION              │
│  Custom workbooks → MITRE coverage → Hunting queries    │
└──────────────────────────────────────────────────────────┘
```

---

## 🔍 Detection Rules

All rules are written in KQL, follow detection-as-code principles, and are mapped to MITRE ATT&CK.

| Rule | MITRE Technique | Severity |
|------|----------------|----------|
| **Brute Force Authentication** | T1110.001 — Password Guessing | High → Critical |
| **Impossible Travel** | T1078.004 — Cloud Accounts | High |
| **Suspicious PowerShell Execution** | T1059.001 — PowerShell | High → Critical |
| **Azure AD Privilege Escalation** | T1098.003 — Additional Cloud Roles | Critical |
| **Anomalous Sign-In Patterns** | T1078 — Valid Accounts | Medium → High |

### Detection Engineering Principles Applied
- ✅ False positive suppression built-in
- ✅ Severity scoring based on context
- ✅ Tuning notes documented per rule
- ✅ MITRE ATT&CK mapping in metadata
- ✅ Recommended actions per finding

---

## 🤖 SOAR Playbook — Automated Alert Enrichment

When an incident fires, the Logic App automatically:

1. **Extracts entities** (IPs, users, hosts) from the alert
2. **Looks up each IP** in VirusTotal
3. **Updates incident severity** if malicious indicators found
4. **Posts a summary** to a Microsoft Teams channel with deep link

This eliminates ~5-10 minutes of manual analyst work per alert.

---

## 🔬 Threat Hunting Queries

Hypothesis-driven hunts for proactive threat discovery:

| Hunt | Hypothesis | MITRE Technique |
|------|-----------|----------------|
| **Lateral Movement** | Single account hitting multiple hosts rapidly | T1550 |
| **DNS Exfiltration** | Unusually long subdomain queries indicate tunneling | T1048 |
| **Malicious OAuth Consent** | Apps requesting high-risk permissions | T1528 |
| **Office Spawning Shells** | Document macros launching scripting interpreters | T1566.001 |
| **Mass Data Download** | Pre-exfiltration staging behavior | T1074 |

---

## 📊 MITRE ATT&CK Coverage Dashboard

Custom workbook that visualizes:
- Active detection rules per tactic
- Severity distribution across detections
- Coverage gaps with recommended next detections
- Recent rule triggers and trends

---

## 🚀 Deployment

Full step-by-step deployment guide in [`docs/DEPLOYMENT_GUIDE.md`](docs/DEPLOYMENT_GUIDE.md).

**Quick start:**
1. Spin up Azure free tier
2. Enable Microsoft Sentinel (31-day free trial)
3. Connect 3 data sources
4. Deploy the 5 analytics rules
5. Deploy the Logic App playbook
6. Import the workbook

**Total deployment time:** ~2-3 hours
**Cost:** $0 (Azure free tier + Sentinel trial)

---

## 📁 Project Structure

```
sentinel-home-lab/
├── detections/                    # Analytics rules (KQL)
│   ├── 01_brute_force_authentication.kql
│   ├── 02_impossible_travel.kql
│   ├── 03_suspicious_powershell.kql
│   ├── 04_privilege_escalation.kql
│   └── 05_anomalous_signin.kql
├── playbooks/                     # SOAR automation
│   └── alert_enrichment_playbook.json
├── workbooks/                     # Dashboards & hunting
│   ├── mitre_coverage_dashboard.json
│   └── threat_hunting_queries.kql
├── parsers/                       # Log normalization
│   └── normalized_auth_events.kql
├── docs/
│   └── DEPLOYMENT_GUIDE.md
├── screenshots/                   # Visual proof of deployment
└── README.md
```

---

## 💡 Skills Demonstrated

This project showcases the full breadth of modern security engineering:

| Skill | How |
|-------|-----|
| **Detection Engineering** | Custom KQL analytics rules with MITRE mapping |
| **SIEM Architecture** | Greenfield Sentinel deployment design |
| **SOAR Automation** | Logic App playbook with multi-API integration |
| **Threat Hunting** | Hypothesis-driven KQL hunting queries |
| **Log Normalization** | ASIM-aligned custom parser |
| **MITRE ATT&CK** | Full tactic and technique mapping |
| **Cloud Security** | Azure-native architecture and IAM |
| **Documentation** | Deployment guide, tuning notes, runbooks |

---

## 🎯 Why This Project Matters

Most security engineers can *use* a SIEM. Few have *built* one from scratch. This project demonstrates:

- **Architectural ownership** — designing data flow, detection logic, and response workflows
- **Detection engineering maturity** — writing rules that minimize false positives and maximize signal
- **Automation mindset** — every alert auto-enriched, every analyst minute saved
- **Threat hunting capability** — proactive, not just reactive
- **Documentation discipline** — production-quality runbooks

---

## 👤 Author

**Sunny Bhardwaj** — Security Engineer
- 🔗 [LinkedIn](https://www.linkedin.com/in/bhardwajsunny/)
- 💻 [GitHub](https://github.com/sunnyoncloud9)
- 📧 sunnyoncloud09@gmail.com
- 🏅 CySA+ | Security+ | AZ-500 | AWS SAA-C03

---

## 📄 License

MIT License — see [LICENSE](LICENSE) for details.

---

*Built to demonstrate enterprise-grade detection engineering and SIEM architecture. All detections are tested in a real Azure Sentinel environment.*
