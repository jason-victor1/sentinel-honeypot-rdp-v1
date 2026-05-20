# ADR-0003: Build Platform Guardrails Before Workload Deployment

## Status

Accepted

## Date

2026-05-20

## Context

This project is an Azure Microsoft Sentinel honeypot lab designed to collect and analyze failed RDP logon telemetry from a deliberately exposed Windows Server VM.

The original tutorial sequence created the VM early and then configured logging and security controls afterward. For this updated V1 lab, the build order is intentionally changed to follow a safer cloud infrastructure pattern:

1. Create resource organization and cost guardrails.
2. Create monitoring and logging foundation.
3. Create network foundation.
4. Create network security controls.
5. Deploy the workload into the pre-secured environment.
6. Validate telemetry.
7. Capture evidence.
8. Clean up resources.

This avoids relying on portal-generated defaults and reduces the risk of creating a VM before the intended security boundary exists.

## Decision

For V1, platform guardrails must be created before workload deployment.

The following resources and controls must exist before the Windows honeypot VM is created:

- Dedicated resource group
- Required resource tags
- Azure budget alert
- Log Analytics workspace
- Microsoft Sentinel enabled on the workspace
- Log Analytics daily ingestion cap
- Virtual network
- Dedicated subnet
- Network security group
- Explicit inbound TCP/3389 rule
- No broad Any/Any inbound rule
- No production network peering
- Timeboxed lab run plan
- Cleanup plan

The VM must be deployed into the pre-created subnet protected by the pre-created NSG. The VM creation wizard should not be allowed to create unmanaged or duplicate network security rules.

## Rationale

Creating the security boundary before the workload ensures that the VM is protected by the intended network controls as soon as it exists.

This supports several cloud security principles:

- Platform before workload
- Guardrails before exposure
- Least privilege network access
- Explicit control ownership
- Reduced configuration drift
- Clear evidence trail
- Safer teardown and cleanup

For this lab, RDP exposure is intentional, but it must still be controlled, documented, timeboxed, and monitored.

## Subnet NSG vs NIC NSG

This lab uses a subnet-associated NSG as the primary control boundary.

Reasoning:

- A subnet-level NSG creates a consistent security baseline for resources deployed into that subnet.
- It avoids per-VM rule sprawl.
- It prevents the VM wizard from creating unmanaged NIC-level security rules.
- It makes the architecture easier to explain, audit, and reproduce.

NIC-level NSGs should be treated as exception controls, not the default pattern for this lab.

## Consequences

Positive outcomes:

- Cleaner architecture
- Lower chance of accidental exposure
- Easier interview explanation
- Easier evidence capture
- Better alignment with landing-zone style thinking
- More repeatable V1 build process

Tradeoffs:

- More setup is required before VM deployment.
- Portal VM creation can be slower because existing network resources must be selected carefully.
- Regional VM SKU issues may delay workload deployment even after the foundation is complete.

## Implementation Notes

Current V1 build order:

```text
Resource group + tags
→ cost guardrails
→ Log Analytics workspace
→ Microsoft Sentinel
→ VNet
→ subnet
→ subnet-associated NSG
→ explicit TCP/3389 inbound rule
→ Windows Server VM
→ AMA / Windows Security Events
→ Event ID 4625 validation
→ Sentinel workbook
→ evidence capture
→ cleanup
```

The VM should not be created until the network and security controls are already in place.

## Related Files

- `docs/architecture.md`
- `docs/runbook.md`
- `docs/threat-model.md`
- `docs/cost-guardrails.md`
- `docs/adr/adr-0001-windows-security-events-via-ama.md`
- `docs/adr/adr-0002-safe-minimum-exposure-timebox.md`
