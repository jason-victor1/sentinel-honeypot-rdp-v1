# ADR-0002: Use Safe-Minimum Exposure and Timeboxing

## Status

Accepted

## Context

The lab intentionally exposes a Windows VM to the internet so that real-world RDP brute-force attempts can generate Windows Security Event ID 4625 telemetry.

The original tutorial maximized exposure by allowing Any/Any inbound traffic and disabling Windows Defender Firewall. That approach increases discoverability but also increases unnecessary attack surface and weakens the project’s security posture.

The purpose of this repo is not merely to recreate an old tutorial. The purpose is to modernize it into a defensible cloud security lab.

## Decision

The lab will use a safe-minimum exposure model:

- expose only TCP/3389 inbound;
- do not allow Any/Any inbound;
- keep Windows Defender Firewall enabled;
- isolate the VM in a dedicated resource group and network;
- timebox the lab run;
- enable budget alerts and VM auto-shutdown;
- delete the resource group after evidence capture.

## Rationale

This keeps the learning objective intact while reducing avoidable risk.

The lab still receives real RDP brute-force attempts, but the VM is not exposed across every protocol and port. This better reflects a security engineering mindset: define the objective, expose only what is required, monitor it, collect evidence, and clean up.

## Alternatives considered

### Any/Any inbound

Rejected.

It increases telemetry volume but creates unnecessary risk and is harder to defend as a best-practice cloud security project.

### Disable Windows Defender Firewall

Rejected.

Host firewall should remain enabled as part of defense-in-depth.

### Fully private lab with synthetic failed logons only

Rejected for V1.

Synthetic-only data would be safer, but it would not demonstrate opportunistic internet scanning and brute-force behavior.

## Consequences

### Positive

- Lower attack surface.
- Stronger interview narrative.
- Better alignment with cloud security guardrails.
- Easier cleanup and blast-radius management.

### Tradeoffs

- The VM may receive fewer attempts than a fully exposed Any/Any honeypot.
- The run may need to stay active long enough to collect meaningful telemetry.

## References

- Azure Monitor Logs cost guidance: https://learn.microsoft.com/en-us/azure/azure-monitor/logs/cost-logs
- Azure Cost Management budgets and alerts: https://learn.microsoft.com/en-us/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending
