# Project Roadmap

---

## Phase 1 — On-Premises Infrastructure ✅ Complete

- Deploy Proxmox hypervisor
- Deploy pfSense firewall/router as VM
- Configure VLAN segmentation (VLAN 10, VLAN 30)
- Configure inter-VLAN routing and firewall rules
- Deploy Windows Server 2022 as Domain Controller
- Configure Active Directory Domain Services
- Join Windows 11 endpoint to domain
- Create domain users, service accounts, and admin accounts
- Deploy Wazuh SIEM on Ubuntu Server (Docker)
- Onboard Windows Server and Windows 11 agents to Wazuh
- Simulate failed authentication attacks and investigate in Wazuh
- Configure remote access via Twingate
- Deploy internal web application (Node.js helpdesk portal)

---

## Phase 2 — Cloud Security Integration ✅ Complete

- Create Azure tenant (Azure for Students)
- Configure Microsoft Entra ID with users, groups, and RBAC roles
- Deploy Log Analytics Workspace in Azure
- Deploy Microsoft Sentinel as cloud-native SIEM
- Install Windows Security Events content hub solution
- Onboard Windows Server 2022 to Azure Arc
- Onboard Windows 11 Pro to Azure Arc
- Create Data Collection Rule via Azure Monitor Agent
- Validate log ingestion via KQL in Defender Advanced Hunting
- Connect Sentinel workspace to Microsoft Defender XDR portal

---

## Phase 3 — Detection Engineering 🔄 In Progress

- Write custom KQL detection rules in Microsoft Sentinel
- Build scheduled analytics rules for brute force detection
- Deploy Sysmon on Windows endpoints for enhanced telemetry
- Enable PowerShell script block logging
- Simulate Kerberoasting attack and validate detection
- Build threat hunting queries in Sentinel
- Create Sentinel Workbook dashboards for SOC visibility
- Write custom Wazuh detection rules
- Map all detections to MITRE ATT&CK framework

---

## Phase 4 — Attack Simulation ⬜ Planned

- Simulate lateral movement across VLANs
- Simulate privilege escalation via AD misconfigurations
- Run BloodHound for Active Directory attack path analysis
- Simulate PsExec-based remote execution
- Test pass-the-hash and pass-the-ticket techniques
- Validate firewall segmentation under active attack scenarios
- Document attacker TTPs and blue team response for each simulation

---

## Phase 5 — Cloud Penetration Testing ⬜ Planned

- Set up AWS account for cloud attack lab
- Deploy CloudGoat (vulnerable-by-design AWS environment)
- Practice IAM privilege escalation scenarios
- Practice S3 bucket misconfiguration exploitation
- Practice cloud credential abuse techniques
- Detect cloud attacks using AWS CloudTrail and GuardDuty
- Compare on-prem vs cloud attack surfaces

---

## Phase 6 — Hardening and Compliance ⬜ Planned

- Implement Group Policy hardening on Active Directory
- Configure account lockout policies
- Harden RDP access (NLA, restricted admin mode)
- Implement Azure Conditional Access policies (requires Entra ID P1)
- Harden web application (authentication, IDOR, upload controls)
- Conduct vulnerability scanning with OpenVAS or Nessus Essentials
- Write incident response playbooks
- Document security controls against CIS Benchmarks

---

## Certifications Aligned to This Project

| Certification     | Relevant Phases         |
|-------------------|-------------------------|
| CompTIA CySA+     | Phase 1, 2, 3, 4        |
| CompTIA PenTest+  | Phase 4, 5              |
| ISC2 SSCP         | Phase 1, 2, 3, 6        |
| ISC2 CCSP         | Phase 2, 5              |
| AZ-500            | Phase 2, 3              |
| LPI Linux Essentials | Phase 1              |
