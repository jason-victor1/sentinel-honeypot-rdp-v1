# Threat Model

## Scope

This threat model covers the V1 Sentinel Honeypot RDP lab.

The lab intentionally exposes a Windows VM to the internet on TCP/3389 to generate failed RDP logon events. Those events are collected into Microsoft Sentinel and visualized in a workbook map.

## Assets

| Asset | Why it matters |
|---|---|
| Azure subscription | Could incur cost or abuse if the VM is compromised |
| Windows VM | Publicly reachable honeypot system |
| Public IP | Internet-facing attack surface |
| Log Analytics workspace | Stores collected security telemetry |
| Microsoft Sentinel workspace | Used for detection, query, and visualization |
| Local admin credential | Must not be leaked or reused |
| Evidence screenshots | May contain identifiers that require redaction |

## Trust boundaries

| Boundary | Description |
|---|---|
| Internet to NSG | Public traffic reaches the Azure network boundary |
| NSG to VM | Only TCP/3389 should be allowed inbound |
| VM to Azure Monitor Agent | Local security events are collected from the host |
| AMA to Log Analytics | Telemetry is sent into the workspace |
| Log Analytics to Sentinel | Sentinel queries and visualizes the data |

## Threats and controls

### T1: RDP brute-force compromise

**Threat:** An attacker guesses or brute-forces valid credentials and gains access to the VM.

**Preventive controls:**

- Strong local admin password.
- Non-default local admin username.
- TCP/3389 only, no Any/Any inbound.
- Windows Defender Firewall remains enabled.
- Short lab run window.

**Detective controls:**

- Monitor Event ID 4625.
- Query top source IPs.
- Query top attempted usernames.
- Build Sentinel workbook map.

**Response controls:**

- Stop VM immediately if compromise is suspected.
- Delete the resource group.
- Rotate any credential that may have been exposed.

### T2: VM used for outbound abuse

**Threat:** If compromised, the VM could be used for scanning, crypto-mining, malware staging, or other abuse.

**Preventive controls:**

- Dedicated lab resource group.
- No peering to trusted networks.
- No secrets stored on VM.
- Timeboxed runtime.
- Auto-shutdown.

**Detective controls:**

- Cost alerts.
- Azure Activity Log review if needed.
- Optional future NSG flow log collection.

**Response controls:**

- Stop VM.
- Delete resource group.
- Review subscription for unexpected resources.

### T3: Cost overrun

**Threat:** Log ingestion, VM runtime, and retention could create unexpected costs.

**Preventive controls:**

- Budget alerts.
- Minimal event collection where possible.
- Short retention.
- Timebox the run.
- Delete resource group.

**Detective controls:**

- Monitor Azure Cost Management.
- Monitor Log Analytics usage.

**Response controls:**

- Stop VM.
- Delete resource group.
- Lower retention if workspace is retained.

### T4: Sensitive data exposure in repo

**Threat:** Screenshots, logs, or config files could reveal subscription IDs, tenant IDs, home IPs, usernames, or secrets.

**Preventive controls:**

- `.gitignore` blocks logs, RDP files, and secret-like files.
- Redact screenshots before committing.
- Do not commit raw exported logs.

**Detective controls:**

- Review `git diff` before commit.
- Optional secret scanning before push.

**Response controls:**

- Rotate exposed secrets.
- Remove sensitive files.
- Purge Git history if necessary.

### T5: Unsafe tutorial drift

**Threat:** The lab unintentionally copies unsafe legacy tutorial steps.

**Preventive controls:**

- Architecture docs explicitly reject Any/Any inbound.
- Runbook requires TCP/3389 only.
- Windows Defender Firewall remains enabled.
- ADRs explain design decisions.

## Risk summary

| Threat | Likelihood | Impact | Residual risk |
|---|---:|---:|---:|
| RDP brute-force compromise | Medium | High | Medium |
| Outbound abuse from VM | Low/Medium | High | Medium |
| Cost overrun | Medium | Medium | Low/Medium |
| Sensitive data in repo | Medium | Medium | Low |
| Unsafe tutorial drift | Medium | Medium | Low |

## Future improvements

- Add Sentinel analytics rule for brute-force bursts.
- Add incident response mini-runbook.
- Add NSG flow logs.
- Add Azure Activity Log monitoring.
- Add Defender for Cloud recommendations review.
