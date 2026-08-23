# ADOpsKit

[![PSScriptAnalyzer](https://github.com/Karanth1992/ADOpsKit/actions/workflows/pssa.yml/badge.svg)](https://github.com/Karanth1992/ADOpsKit/actions/workflows/pssa.yml)
![PowerShell](https://img.shields.io/badge/PowerShell-5.1%2B-blue?logo=powershell)
![Platform](https://img.shields.io/badge/platform-Windows%20Server-lightgrey)
[![PSGallery](https://img.shields.io/powershellgallery/v/ADOpsKit?label=PSGallery&logo=powershell)](https://www.powershellgallery.com/packages/ADOpsKit)
[![PSGallery Downloads](https://img.shields.io/powershellgallery/dt/ADOpsKit?label=Downloads)](https://www.powershellgallery.com/packages/ADOpsKit)

PowerShell module for Active Directory administration, Hybrid Identity (Entra Connect), GPO management, DC health checks, and security auditing — published to the PowerShell Gallery.

Detailed write-ups and walkthroughs are published at **[karanth.ovh](https://karanth.ovh)**.

Looking for other scripts? Standalone (non-module) AD scripts live in [powershell-scripts](https://github.com/Karanth1992/powershell-scripts). Server provisioning tooling lives in [ADSetupKit](https://github.com/Karanth1992/ADSetupKit).

---

## Install

```powershell
Install-Module ADOpsKit
```

## Functions

| Function | Purpose |
|----------|---------|
| `Get-ADReplicationTopologyDiagram` | Self-contained HTML topology diagram — DCs, sites, replication links. No ADWS required. |
| `Get-ADArchitectureAssessment` | Broad AD inventory — domains, DCs, users, computers, OUs, sites, replication, GPOs. |
| `Get-ADForestHealth` | Forest-wide DC health report — DCDiag, disk, CPU, memory, uptime. |
| `Test-DCPortHealth` | Tests critical AD TCP ports across all domain controllers. |
| `Enable-DCPerformanceBaseline` | Deploys a `logman` performance Data Collector Set on each DC. |
| `Get-AccountLockoutReport` | Reports locked accounts and traces lockout source via Event ID 4740. |
| `Get-InsecureLDAPBinds` | Detects unsigned and simple LDAP binds via Event ID 2889. |
| `Get-GPOInventoryWithSettings` | GPO inventory with links, permissions, status, WMI filters, and configured settings from `Get-GPOReport` XML. |
| `Get-EntraConnectSyncStatus` | Entra Connect health — sync cycle/result, per-connector export/import activity, pending exports, password sync, staging mode. Must run locally on the Entra Connect server. |
| `Register-ADOpsKitScheduledTasks` | Interactive wizard to schedule any ADOpsKit function as a Windows Scheduled Task. |
| `Test-ADDCDiagHealth` | Runs the full dcdiag test suite against every DC and emails an alert only on status change or a persistent-failure reminder — a near-real-time monitoring check. |
| `Register-ADDCDiagHealthMonitor` | Registers `Test-ADDCDiagHealth` as a Scheduled Task on a short repeating interval (default 5 min), turning it into a real-time DC monitoring agent. |
| `Invoke-ADRealtimeHeartbeat` | Lightweight per-DC heartbeat (TCP ports, SYSVOL/NETLOGON, required services) designed for 30-60 second polling; alerts via email/Slack only on state change. Complements `Test-ADDCDiagHealth`'s heavier, less-frequent full dcdiag sweep. |
| `Get-DCDecommissionReadiness` | Pre-decommission scan of a single DC — FSMO roles, replication health, logon activity, DNS/SYSVOL/DFSR, sites/subnets, trusts, Global Catalog status, and a readiness checklist. |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| **1.5.1** | 2026-08-23 | Consistency/reliability follow-up. Converted every remaining `New-Item`/`Out-File` call from `-Path` to `-LiteralPath` across 14 files, completing the consistency pass started in 1.5.0. No functional changes. Full details: [CHANGELOG.md](CHANGELOG.md#151--2026-08-23) |
| **1.5.0** | 2026-07-21 | Security/reliability release. Fixed `Get-EntraConnectSyncStatus` reading four properties that don't exist on the real ADSync object types — its sync-error detection never actually worked; rewritten to the real schema and its hidden WinRM dependency removed. Removed a hidden WinRM dependency in `Get-ADForestHealth`. Fixed `Get-DCDecommissionReadiness` crashing under strict checking and silently reporting "OK" for checks that failed to run. Added HTML-encoding of AD-sourced values in report output (stored-injection risk). Fixed XPath injection in `Get-AccountLockoutReport`. Rewrote `Enable-DCPerformanceBaseline`'s broken `logman` deployment. Fixed a leaked credential handle and non-fatal secrets-folder ACL failure in `Register-ADDCDiagHealthMonitor`. Fixed `Register-ADOpsKitScheduledTasks` performing live AD authentication under `-WhatIf`. Added atomic state-file writes to `Invoke-ADRealtimeHeartbeat`/`Test-ADDCDiagHealth`. Added `Set-StrictMode -Version Latest` and consistent `-LiteralPath` usage across every script. Full details: [CHANGELOG.md](CHANGELOG.md#150--2026-07-21) |
| **1.4.0** | 2026-07-11 | Added `Invoke-ADRealtimeHeartbeat` (30-60s per-DC heartbeat, state-diffed email/Slack alerting) and `Get-DCDecommissionReadiness` (pre-decommission readiness scan). Removed `Get-GPOInventory` (superseded by `Get-GPOInventoryWithSettings`). Fixed `Get-AccountLockoutReport` producing no output with default params, and two `Register-ADOpsKitScheduledTasks` bugs (unqualified account names, single-day weekly schedules) |
| **1.3.0** | 2026-07-09 | Added `Test-ADDCDiagHealth` (full dcdiag suite, state-diffed alerting) and `Register-ADDCDiagHealthMonitor` (5-minute repeating Scheduled Task) for near-real-time DC monitoring with email alerts on status change |
| **1.2.0** | 2026-07-05 | `Register-ADOpsKitScheduledTasks` overhaul — upfront credential validation (batch logon check), gMSA support, `-ConfigPath` setup replay, `-RetentionDays` report cleanup, Scripts folder ACL restriction, task exit codes, log rotation, `-WhatIf` support, quoting fixes |
| **1.1.6** | 2026-06-29 | Fix `Register-ADOpsKitScheduledTasks` — scripts written to `.ps1` files; dated filenames now expand correctly at runtime |
| **1.1.5** | 2026-06-29 | Fix `Get-AccountLockoutReport` — Int32 overflow on default lookback value; fix Copy-Item errors when no lockouts exist |
| **1.1.4** | 2026-06-29 | `Register-ADOpsKitScheduledTasks` — per-task email recipients; each function can send its report to a different address |
| **1.1.3** | 2026-06-29 | `Register-ADOpsKitScheduledTasks` — email reports as attachments after each run (SMTP, SSL, auth support) |
| **1.1.2** | 2026-06-28 | Fix `Register-ADOpsKitScheduledTasks` — ambiguous parameter set error when registering tasks (`-Principal` and `-Password` conflict on `Register-ScheduledTask`) |
| **1.1.1** | 2026-06-28 | Fix `Test-DCPortHealth` — service names showing as blank due to integer key lookup on ordered hashtable |
| **1.1.0** | 2026-06-28 | All functions default output to `C:\ADOpsKit\Reports\<FunctionName>\` with dated filenames. Added `Register-ADOpsKitScheduledTasks` interactive scheduler wizard. Added `Get-Help about_ADOpsKit` help file |
| **1.0.1** | 2026-06-27 | Initial PSGallery publish — 10 functions, PSScriptAnalyzer CI, `Get-ADReplicationTopologyDiagram` added |

Full changelog: [CHANGELOG.md](CHANGELOG.md)

---

## License

MIT — see [LICENSE](LICENSE).
