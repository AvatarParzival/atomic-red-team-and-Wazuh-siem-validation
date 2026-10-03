# Atomic Red Team & Wazuh SIEM Validation

[![GRC](https://img.shields.io/badge/Domain-GRC-243B53)](https://en.wikipedia.org/wiki/Governance,_risk_management,_and_compliance)
[![MITRE ATT&CK](https://img.shields.io/badge/MITRE%20ATT%26CK-Enterprise-005A9C)](https://attack.mitre.org/)
[![Wazuh](https://img.shields.io/badge/SIEM-Wazuh%204.7.5-3AAAFF)](https://wazuh.com/)
[![Atomic Red Team](https://img.shields.io/badge/Testing-Atomic%20Red%20Team-FF0000)](https://github.com/redcanaryco/atomic-red-team)
[![Windows 11](https://img.shields.io/badge/Target-Windows%2011%20Enterprise-0078D4)](https://www.microsoft.com/en-us/windows/windows-11)

A structured detection validation assessment that executes **6 MITRE ATT&CK techniques** via Atomic Red Team against a live Windows 11 Enterprise host and measures what **Wazuh 4.7.5** actually produces — revealing a **17% overall detection rate** and a critical SIEM coverage gap across an assumed-complete logging stack.

> **Core principle:** detection capability was not assumed from configuration — it was measured from evidence.

## Final Report

📄 **[Read the complete Detection Gap Assessment Report](ZDR-W5-GRC-24GB_Abdullah_Zubair_v2.pdf)**

- **Task ID:** ZDR-W5-GRC-24GB
- **Assessment type:** Detection Gap Assessment — Windows 11 Target vs. Wazuh SIEM
- **Report date:** 22 September 2026
- **Prepared by:** Abdullah Zubair

## Project Objectives

1. Build a live lab with Windows 11 Enterprise, Sysmon, and Wazuh 4.7.5.
2. Select 6 MITRE ATT&CK techniques relevant to prior GRC weeks' risk register.
3. Document pre-execution detection assumptions via tabletop exercise.
4. Execute each technique using Atomic Red Team and capture raw Wazuh telemetry.
5. Measure actual detection results against pre-defined assumptions.
6. Identify and quantify detection gaps with operational impact analysis.
7. Issue prioritised remediation recommendations to close identified gaps.

## Scope and Environment

| Component | Detail |
|---|---|
| Target host | win11-01 — Windows 11 Enterprise (`192.168.136.23`) |
| SIEM platform | Wazuh 4.7.5 on Parzival (`192.168.136.50`) |
| Detection config | Sysmon with SwiftOnSecurity configuration |
| Testing tool | Atomic Red Team |
| Framework | MITRE ATT&CK for Enterprise |
| Network | Isolated lab — `192.168.136.0/24` |

## Key Results

| Metric | Result |
|---|---:|
| Techniques executed | 6 |
| Fully detected | 1 (17%) |
| Partial detection | 1 (17%) |
| No detection | 4 (66%) |
| **Overall detection rate** | **17%** |
| Most critical gap | T1003.001 — LSASS Credential Dump (DREAD 9.4, zero alerts) |
| NC-02 status | **Empirically confirmed** — C2 beacon produced no alert |

## MITRE ATT&CK Techniques Tested

| Technique | Title | Tactic | Detection Result |
|---|---|---|---|
| T1059.001 | PowerShell Execution | Execution | ⚠️ Partial |
| T1053.005 | Scheduled Task Persistence | Persistence | ❌ Not detected |
| T1078.003 | Valid Accounts — Local | Defence Evasion | ❌ Not detected |
| T1003.001 | LSASS Credential Dump | Credential Access | ❌ Not detected |
| T1071.001 | HTTP C2 Beacon | Command & Control | ❌ Not detected |
| T1547.001 | Registry Run Key Persistence | Persistence | ✅ Detected |

An attacker executing a standard **Initial Access → Execution → Persistence → Credential Access → C2** chain against this environment would complete the entire sequence undetected by the current SIEM configuration.

## Prioritised Recommendations

| ID | Priority | Recommendation |
|---|---|---|
| REC-01 | 🔴 Critical | Enable LSASS process access detection |
| REC-02 | 🔴 Critical | Enable Sysmon network connection forwarding (closes NC-02) |
| REC-03 | 🟠 High | Enable registry Run key monitoring |
| REC-04 | 🟠 High | Forward and decode Windows Security EID 4698 |
| REC-05 | 🟡 Medium | Add PowerShell execution detection rule |

REC-02 directly remediates the centralised logging control failure (NC-02) identified in the Week 3 Eramba GRC audit, closing a known risk that had previously been accepted without technical validation.

## Repository Structure

```text
.
├── README.md
├── ZDR-W5-GRC-24GB_Abdullah_Zubair_v2.pdf
└── evidence/
```

## Programme Context

This assessment is Week 5 of the ZeroDay Reapers GRC internship. Prior weeks built the artefact chain being validated here:

| Week | Deliverable |
|---|---|
| Week 1 | Asset inventory and risk register |
| Week 2 | CIS-hardened Windows 11 baseline (62% compliance) |
| Week 3 | Eramba GRC platform — 14 controls, 11 risks, 6 nonconformities |
| Week 4 | STRIDE threat model of OWASP Juice Shop |
| **Week 5** | **Live detection validation of assumptions made in Weeks 1–4** |

## Limitations

- Assessment was conducted entirely within an isolated lab and does not reflect a production SOC environment.
- Atomic Red Team tests represent known-good technique implementations; custom or obfuscated variants may behave differently.
- Wazuh telemetry was measured at the alert level only; raw log ingestion was not independently verified per event.
- Detection results are point-in-time and reflect the specific Sysmon and Wazuh rule configuration active during testing.
- Only 6 of the ATT&CK techniques present in the prior weeks' risk register were selected for execution.

## Lessons Learned

- A configured SIEM is not the same as a working SIEM — only execution reveals the difference.
- Sysmon without network connection forwarding cannot detect C2 traffic regardless of rule quality.
- Tabletop assumptions overestimated detection capability across five of six techniques.
- Partial detection through side-effects is not equivalent to a tuned, reliable alert.
- Closing a nonconformity requires technical evidence of remediation, not just a configuration change.

## Author

**Abdullah Zubair**  
Cybersecurity | GRC | Security Automation
- GitHub: [@AvatarParzival](https://github.com/AvatarParzival)
- LinkedIn: [Abdullah Zubair](https://www.linkedin.com/in/abdullahzubairr)
- Email: [abdullah69zubair@gmail.com](mailto:abdullah69zubair@gmail.com)

## Responsible Use

This repository is intended for educational, defensive-security and professional portfolio purposes. All techniques were executed under full authorisation within an isolated lab network. Do not reproduce these tests against systems without explicit written authorisation.
