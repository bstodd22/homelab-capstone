# Azure Sentinel Setup — Hybrid Cloud SIEM Integration

**Date:** June 22, 2026  
**Status:** Complete  
**Phase:** 2 — Cloud Security Integration

---

## Overview

This document covers the deployment of Microsoft Sentinel as a cloud-native SIEM integrated with the on-premises homelab environment. The goal was to extend security visibility beyond Wazuh into Azure, creating a hybrid SOC architecture where on-premises endpoints feed directly into a cloud SIEM.

This mirrors how real enterprise environments operate — on-premises infrastructure managed alongside cloud services with unified security visibility across both planes.

---

## Architecture

```
Windows Server 2022 (WIN-N4GSIAT5P7D)  ─┐
                                          ├─→ Azure Arc → Azure Monitor Agent
Windows 11 Pro (DESKTOP-AJ9B6KI)       ─┘         │
                                                    ▼
                                      Log Analytics Workspace (homelab-laws)
                                                    │
                                                    ▼
                                         Microsoft Sentinel
                                                    │
                                                    ▼
                                    Microsoft Defender XDR Portal
```

---

## Prerequisites

- Azure for Students subscription ($100 credit, no credit card required)
- On-premises machines with internet access (T-Mobile 5G home internet via pfSense)
- Windows Server 2022 and Windows 11 Pro endpoints operational
- PowerShell access (Administrator) on both endpoints

---

## Step 1 — Azure Tenant and Entra ID Setup

### Tenant Details
- **Tenant name:** Default Directory
- **Primary domain:** braydentodd104gmail.onmicrosoft.com
- **Subscription:** Azure for Students
- **Global Administrator:** braydentodd104@gmail.com

### Entra ID Configuration
Created the following users to simulate an enterprise identity structure:

| Display Name    | Username                                              | Account Type     |
|-----------------|-------------------------------------------------------|------------------|
| John Smith      | john.smith@braydentodd104gmail.onmicrosoft.com        | Standard user    |
| SVC Backup      | svc.backup@braydentodd104gmail.onmicrosoft.com        | Service account  |
| Admin Helpdesk  | admin.helpdesk@braydentodd104gmail.onmicrosoft.com    | Admin account    |

### RBAC Role Assignment
- Assigned **Helpdesk Administrator** role to `admin.helpdesk` account
- Demonstrates least privilege — helpdesk can reset passwords but cannot modify security settings

### Security Group
- Created `SOC-Analysts` security group
- Added `john.smith` as a member
- Simulates a real SOC team group that would be granted read access to Sentinel

---

## Step 2 — Resource Group

Created a resource group to contain all homelab cloud resources:

| Setting          | Value         |
|------------------|---------------|
| Name             | homelab-rg    |
| Subscription     | Azure for Students |
| Region           | Central US    |

All subsequent resources deployed into `homelab-rg`.

---

## Step 3 — Log Analytics Workspace

Log Analytics is the underlying data store for Sentinel. All log data is ingested here and queried using KQL.

| Setting          | Value         |
|------------------|---------------|
| Name             | homelab-laws  |
| Resource group   | homelab-rg    |
| Region           | Central US    |
| Pricing tier     | Pay-as-you-go |

**Note:** East US was blocked by Azure for Students subscription policy. Central US was used instead.

---

## Step 4 — Microsoft Sentinel Deployment

Sentinel was deployed on top of the Log Analytics workspace.

1. Searched for Microsoft Sentinel in Azure portal
2. Clicked Create → selected `homelab-laws` workspace
3. Sentinel deployed successfully
4. Workspace connected to Microsoft Defender XDR portal (unified SOC console)

### Content Hub
Installed the **Windows Security Events** solution (v3.0.12) from Content Hub:
- 20 analytics rules
- 2 data connectors
- 50 hunting queries
- 2 workbooks

---

## Step 5 — Azure Arc Onboarding

Azure Arc extends Azure management to on-premises machines. Both homelab endpoints were onboarded as Arc-managed resources.

### Process
For each machine:
1. Azure Arc → Machines → Add a machine → Add a single server
2. Generated onboarding script (PowerShell)
3. Copied script to endpoint
4. Ran script as Administrator in PowerShell
5. Authenticated via browser popup (Microsoft login)
6. Machine registered in Azure Arc

### Registered Machines

| Machine Name       | OS              | Arc Status | Resource Group |
|--------------------|-----------------|------------|----------------|
| WIN-N4GSIAT5P7D    | Windows Server  | Connected  | homelab-rg     |
| DESKTOP-AJ9B6KI    | Windows 11 Pro  | Connected  | homelab-rg     |

### PowerShell Commands Used
```powershell
# Set execution policy for script run
Set-ExecutionPolicy -ExecutionPolicy Bypass -Scope Process

# Run Arc onboarding script
.\arc-install.ps1

# Verify Arc agent service
Get-Service -Name himds
```

### Verification
```
Installation of azcmagent completed successfully
INFO    Connecting machine to Azure...
INFO    Cloud: AzureCloud
INFO    Creating resource in Azure...
INFO    Resource ID=/subscriptions/.../resourceGroups/homelab-rg/providers/Microsoft.HybridCompute/machines/WIN-N4GSIAT5P7D
```

---

## Step 6 — Data Collection Rule

Created a Data Collection Rule (DCR) to define what logs to collect and where to send them.

| Setting        | Value                    |
|----------------|--------------------------|
| Rule name      | homelab-windows-dcr      |
| Resource group | homelab-rg               |
| Log type       | All Security Events      |
| Agent          | Azure Monitor Agent (AMA)|

Both Arc-connected machines added as resources under this rule. The Azure Monitor Agent was automatically deployed to each machine upon DCR assignment.

---

## Step 7 — Log Ingestion Validation

Validated end-to-end log flow using KQL in Microsoft Defender Advanced Hunting.

### Query
```kql
SecurityEvent
| where TimeGenerated > ago(1h)
| summarize count() by Computer, EventID
| order by count_ desc
```

### Results
| Computer                        | EventID | Count | Description           |
|---------------------------------|---------|-------|-----------------------|
| DESKTOP-AJ9B6KI.homelab.local   | 4624    | 1     | Successful logon      |
| DESKTOP-AJ9B6KI.homelab.local   | 4672    | 1     | Privileged logon      |

**Pipeline confirmed operational.** On-premises Windows 11 endpoint successfully streaming security events into Microsoft Sentinel via Azure Arc and Azure Monitor Agent.

---

## Key Event IDs Being Collected

| Event ID | Description                        | Security Relevance                          |
|----------|------------------------------------|---------------------------------------------|
| 4624     | Successful logon                   | Baseline authentication activity            |
| 4625     | Failed logon                       | Brute force detection                       |
| 4672     | Special privileges assigned        | Privileged account usage                    |
| 4648     | Logon using explicit credentials   | Pass-the-hash / lateral movement indicator  |
| 4768     | Kerberos ticket requested (TGT)    | Kerberoasting detection                     |
| 4769     | Kerberos service ticket requested  | Kerberoasting detection                     |

---

## KQL Detection Queries

### Failed logon volume (brute force detection)
```kql
SecurityEvent
| where EventID == 4625
| summarize FailedLogins = count() by Account
| order by FailedLogins desc
```

### Privileged account logon activity
```kql
SecurityEvent
| where EventID == 4672
| project TimeGenerated, Computer, Account, LogonType
| order by TimeGenerated desc
```

### Authentication activity by machine
```kql
SecurityEvent
| where TimeGenerated > ago(24h)
| summarize count() by Computer, EventID
| order by count_ desc
```

---

## Cost Considerations

Azure for Students provides $100 credit. Estimated monthly cost for this setup:

| Resource                  | Estimated Cost     |
|---------------------------|--------------------|
| Log Analytics (low volume)| ~$2-5/month        |
| Azure Arc (machines)      | Free for servers   |
| Microsoft Sentinel        | Free up to 10GB/day|
| Azure Monitor Agent       | Free               |

At homelab log volumes, total cost is minimal and well within student credit limits.

---

## MITRE ATT&CK Coverage

| Technique        | ID        | Detection                        |
|------------------|-----------|----------------------------------|
| Brute Force      | T1110     | EID 4625 — failed logon volume   |
| Valid Accounts   | T1078     | EID 4624 — logon monitoring      |
| Privileged Logon | T1078.002 | EID 4672 — special privileges    |

---

## Next Steps

- Write scheduled analytics rules in Sentinel for automated alerting
- Simulate brute force attack and validate Sentinel alert fires
- Deploy Sysmon on endpoints for enhanced process and network telemetry
- Onboard Windows Server to DCR (currently Windows 11 only confirmed)
- Explore Sentinel Workbooks for dashboard visualization
- Investigate Defender for Endpoint integration via Azure Arc extension
