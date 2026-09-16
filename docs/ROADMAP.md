# Project Roadmap

This roadmap separates confirmed accomplishments from future work.
Only work actually performed is marked complete.

## Phase 1 — Core On-Premises Infrastructure
Status: Core deployment complete

Completed:
- [x] Deploy Proxmox.
- [x] Deploy Windows Server and Linux virtual machines.
- [x] Configure pfSense firewall rules.
- [x] Configure VLAN segmentation.
- [x] Deploy and configure Wazuh.
- [x] Configure Windows Server and Linux log forwarding to Wazuh.

## Phase 2 — Hybrid Cloud Log Collection
Status: Initial integration complete

Completed:
- [x] Extend the environment to Azure through Azure Arc.
- [x] Configure Windows Security Event forwarding to Microsoft Sentinel.
- [x] Establish incoming Windows security logs in Sentinel.
- [x] Integrate Sentinel with the Microsoft Defender portal.

## Phase 3 — Log Analysis and Detection Engineering
Status: Planned — not yet performed

- [ ] Learn KQL and query collected security events.
- [ ] Document ingestion validation for each endpoint.
- [ ] Examine successful and failed authentication events.
- [ ] Create and test a scheduled Sentinel analytics rule.
- [ ] Explore custom Wazuh detection rules.
- [ ] Evaluate Sysmon and PowerShell script block logging.
- [ ] Build a basic monitoring workbook.
- [ ] Document detection logic, test results, and limitations.
- [ ] Map tested detections to relevant MITRE ATT&CK techniques.

## Phase 4 — Controlled Attack Simulation and Investigation
Status: Planned — not yet performed

All simulations will be limited to owned or explicitly authorized
lab systems.

- [ ] Generate controlled failed-authentication activity.
- [ ] Examine resulting logs and alerts.
- [ ] Document a lab investigation and response recommendations.
- [ ] Explore Active Directory attack paths using BloodHound.
- [ ] Practice controlled lateral-movement and privilege-escalation scenarios.
- [ ] Validate network segmentation during controlled testing.
- [ ] Document observed behavior and detection gaps.

## Phase 5 — Cloud Security Testing
Status: Future learning goals

- [ ] Establish an isolated AWS lab with cost controls.
- [ ] Evaluate CloudGoat for authorized cloud security exercises.
- [ ] Practice identifying IAM and storage misconfigurations.
- [ ] Explore CloudTrail and GuardDuty telemetry.
- [ ] Compare cloud and on-premises security visibility.

## Phase 6 — Lab Hardening and Response Documentation
Status: Future learning goals

- [ ] Review and document lab Group Policy settings.
- [ ] Configure and test account lockout policies.
- [ ] Review RDP restrictions and Network Level Authentication.
- [ ] Evaluate Conditional Access, subject to licensing.
- [ ] Perform authorized vulnerability scanning.
- [ ] Draft and test lab incident response playbooks.

- [ ] Assess selected configurations against CIS Benchmarks.

## Documentation Standards

- Mark tasks complete only after performing them.
- Separate setup instructions from evidence of completed work.
- Label example queries as untested until executed.
- Do not equate collected events with validated attack detection.
- Publish sanitized evidence where practical.
- Keep resume claims aligned with completed project work.
