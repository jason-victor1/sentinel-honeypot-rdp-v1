# Security Policy

## Scope

This repository documents a lab environment for learning Microsoft Sentinel, Azure Monitor Agent, and Windows Security Event analysis. It is not a production deployment pattern.

## Safety requirements

This lab intentionally exposes a Windows VM to the public internet on TCP/3389. The following controls are mandatory:

- Do not open Any/Any inbound traffic.
- Do not disable Windows Defender Firewall.
- Do not commit secrets, passwords, API keys, `.rdp` files, exported logs, or credentials.
- Use a dedicated Azure resource group.
- Use budget alerts and VM auto-shutdown.
- Timebox the run.
- Delete the resource group after evidence capture.

## Sensitive data handling

Do not commit:

- Azure subscription IDs if you prefer to keep them private.
- Public IPs associated with personal or home networks.
- Screenshots showing passwords, usernames you do not want public, tenant IDs, or account identifiers.
- RDP files.
- Raw logs containing sensitive identifiers.

## Reporting issues

If a secret or sensitive artifact is committed:

1. Rotate the exposed credential immediately.
2. Remove it from the repository.
3. Purge it from Git history if needed.
4. Document the incident in `evidence/redactions/redaction-notes.md`.

## Production warning

Never expose RDP directly to the internet in production. Use private access patterns such as VPN, Azure Bastion, just-in-time access, Conditional Access, Defender for Cloud recommendations, or other enterprise access controls.
