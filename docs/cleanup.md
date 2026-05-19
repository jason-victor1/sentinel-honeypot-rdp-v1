
Cleanup
Purpose

Cleanup is mandatory because this lab creates an internet-facing VM and a log ingestion pipeline.

The project is not complete until resources are removed and cleanup evidence is captured.

Cleanup target

Delete the dedicated resource group:

rg-sentinel-honeypot-v1

Deleting the resource group should remove:

Windows VM
Public IP
NIC
NSG
VNet/subnet
Log Analytics workspace
Sentinel configuration attached to the workspace
Associated disks and dependent resources
Cleanup checklist

Before deleting:

Capture workbook screenshot.
Capture KQL query evidence.
Capture budget/guardrail screenshot.
Export no raw logs unless intentionally needed and reviewed.

Delete:

Open Azure Portal.
Go to Resource Groups.
Select the lab resource group.
Choose Delete resource group.
Confirm the resource group name.
Wait for deletion to complete.

Verify:

Resource group no longer appears.
VM no longer appears.
Public IP no longer appears.
No unexpected disks remain.
No unexpected Log Analytics workspace remains.
No continuing cost alert is triggered.
Evidence

Save cleanup proof as:

evidence/screenshots/06_resource_group_deleted.png
If workspace must be retained temporarily

If you intentionally retain the workspace for screenshots or later analysis:

Delete the VM and public IP immediately.
Stop ingestion.
Reduce retention.
Document why the workspace was retained.
Set a follow-up cleanup date.
Failure modes
Failure mode	Action
Resource group deletion fails	Review dependent resources and retry
Public IP remains	Delete it manually
Disk remains	Delete it manually after confirming it is lab-only
Workspace continues ingesting	Remove DCR/connector association
Cost alert fires after cleanup	Review Cost Management and remaining resources
Final project completion criteria

The lab is complete only when:

evidence screenshots are captured;
after-action notes are written;
resource group is deleted;
GitHub repo is updated;
no lab resources remain running.
