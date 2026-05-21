# 🚀 Deployment Guide — Sentinel Home Lab

This guide walks you through deploying the entire Sentinel home lab project to a free Azure account.

**Total time:** ~2-3 hours (mostly waiting for data ingestion)
**Cost:** $0 (Azure free tier + Sentinel 31-day trial)

---

## Prerequisites

- Azure account (free tier OK) — [Sign up](https://azure.microsoft.com/free/)
- Microsoft Teams account (for playbook target)
- VirusTotal account (free) — [Get API key](https://www.virustotal.com/gui/join-us)

---

## Phase 1 — Workspace Setup (15 minutes)

### Step 1.1: Create Resource Group
1. Azure Portal → Resource Groups → **Create**
2. Name: `rg-sentinel-lab`
3. Region: East US (or closest to you)

### Step 1.2: Create Log Analytics Workspace
1. Azure Portal → Log Analytics Workspaces → **Create**
2. Resource group: `rg-sentinel-lab`
3. Name: `law-sentinel-lab`
4. Pricing tier: **Pay-as-you-go** (free tier ingests 5 GB/month free)

### Step 1.3: Enable Microsoft Sentinel
1. Azure Portal → Microsoft Sentinel → **Create**
2. Select your `law-sentinel-lab` workspace
3. Click **Add Microsoft Sentinel**
4. Free trial activates automatically (31 days)

---

## Phase 2 — Connect Data Sources (30 minutes)

### Step 2.1: Azure Active Directory
1. Sentinel → Data Connectors → **Azure Active Directory**
2. Click **Open connector page**
3. Enable:
   - ✅ Sign-in logs
   - ✅ Audit logs
   - ✅ Non-interactive user sign-in logs
4. Click **Apply Changes**

> ⏱️ **Wait time:** Data starts flowing in 5-15 minutes

### Step 2.2: Azure Activity
1. Sentinel → Data Connectors → **Azure Activity**
2. Click **Open connector page**
3. Click **Launch Azure Policy Assignment Wizard**
4. Apply policy to your subscription
5. Click **Review + Create**

### Step 2.3: Microsoft Defender for Cloud (Optional)
1. Sentinel → Data Connectors → **Microsoft Defender for Cloud**
2. Click **Open connector page**
3. Enable bi-directional sync
4. Note: Free tier provides basic Defender features

### Step 2.4: Verify Data Ingestion
After ~30 minutes, run this query in Sentinel logs:
```kql
union *
| where TimeGenerated > ago(1h)
| summarize Count = count() by Type
| order by Count desc
```
You should see `SigninLogs`, `AuditLogs`, and `AzureActivity` populated.

---

## Phase 3 — Deploy Detection Rules (20 minutes)

### Step 3.1: Deploy Each KQL Rule
For each `.kql` file in `/detections/`:

1. Sentinel → **Analytics** → **+ Create** → **Scheduled query rule**
2. Configure:
   - **Name:** Use the rule name from the comment header
   - **Severity:** Use severity from the rule
   - **Tactics:** Select matching MITRE tactic
3. **Set rule logic:**
   - Paste the KQL query
   - Run frequency: 5 minutes (for testing) or 15 minutes (production)
   - Lookup period: 1 hour
4. **Incident settings:**
   - Enable incident creation
   - Group alerts triggered within 5 hours
5. **Review and create**

Repeat for all 5 detection rules:
- `01_brute_force_authentication.kql`
- `02_impossible_travel.kql`
- `03_suspicious_powershell.kql`
- `04_privilege_escalation.kql`
- `05_anomalous_signin.kql`

---

## Phase 4 — Deploy SOAR Playbook (30 minutes)

### Step 4.1: Get VirusTotal API Key
1. Sign up at virustotal.com
2. Profile → API Key (free tier: 4 lookups/min, 500/day)

### Step 4.2: Deploy Logic App Playbook
1. Open Azure Portal → **Deploy a custom template**
2. Click **Build your own template in the editor**
3. Paste contents of `playbooks/alert_enrichment_playbook.json`
4. Fill parameters:
   - `playbookName`: `Sentinel-Alert-Enrichment`
   - `teamsChannelId`: (get from Teams channel settings)
5. Click **Review + Create** → **Create**

### Step 4.3: Authorize Connections
After deployment:
1. Open the Logic App
2. Authorize the Sentinel connection (Run as Sentinel)
3. Authorize the Teams connection (sign in)
4. Save

### Step 4.4: Attach Playbook to Analytics Rule
1. Sentinel → Analytics → Open any rule
2. **Automated response** tab → **+ Add**
3. Select the deployed playbook
4. Save

---

## Phase 5 — Deploy Workbook Dashboard (10 minutes)

### Step 5.1: Import MITRE Coverage Workbook
1. Sentinel → **Workbooks** → **+ Add workbook**
2. Click **Advanced Editor** (top right)
3. Paste contents of `workbooks/mitre_coverage_dashboard.json`
4. Click **Apply**
5. Save as: `MITRE ATT&CK Coverage`

### Step 5.2: Save Threat Hunting Queries
1. Sentinel → **Hunting** → **Queries** tab
2. Click **+ New Query**
3. For each query in `workbooks/threat_hunting_queries.kql`:
   - Paste query
   - Add description and MITRE tactic
   - Save

---

## Phase 6 — Test End-to-End (15 minutes)

### Step 6.1: Trigger a Detection
The easiest way to test:
1. Sign out of Azure
2. Attempt to sign in with the wrong password 10+ times rapidly
3. Wait 5 minutes
4. Sentinel → Incidents — should see "Brute Force Authentication"

### Step 6.2: Verify Playbook Execution
1. Open the incident
2. Check **Logic Apps** tab — should show playbook run
3. Check Teams channel — should see enrichment message

### Step 6.3: Take Screenshots
For your GitHub README, capture:
- Sentinel overview page
- One analytics rule
- One triggered incident
- The Logic App execution
- The MITRE coverage workbook
- A hunting query result

Save them to `/screenshots/` in your repo.

---

## Phase 7 — Cleanup (When Done)

> ⚠️ **Important:** Sentinel free trial is 31 days. After that, costs ~$2.30/GB ingested.

To avoid charges:
1. Delete the resource group `rg-sentinel-lab`
2. This removes Sentinel, Log Analytics, Logic Apps — everything

```bash
az group delete --name rg-sentinel-lab --yes
```

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| No SigninLogs data | Wait 30-60 min after enabling connector |
| Playbook fails on Teams | Re-authorize Teams connection |
| KQL query syntax errors | Check Sentinel's KQL version — some operators differ |
| Empty workbook | Wait for sample data, or use historical lookback |

---

## Cost Optimization Tips

- ✅ Use 5 GB free tier on Log Analytics
- ✅ Set data retention to 30 days (default 90 days = more cost)
- ✅ Sentinel 31-day free trial = $0 for first month
- ✅ Disable connectors not needed for testing
- ❌ Avoid enabling all data sources (can blow through 5 GB quickly)

---

*Built by Sunny Bhardwaj — Security Engineer*
