# Lab management and system administration

This page guides you on how to manage the lab. Make sure you've gone through [Infrastructure](infra.md) first.

## Onboarding a new operator
Generate an SSH key (ed25519)
Upload your public key to the `labadmin` storage on the Synology NAS
Get access to the Ansible repository
Get access to the Synology NAS (credentials, share mounts)
Distribute your key to the workstations
Verify access by pinging the fleet with Ansible

## Equipment and Inventory

### Maintenance
System updates (when, how, which playbook)
Clearing `/data` on workstations
Checking free space on workstations and on the NAS
Replacing or reimaging a workstation

## Internet access
credentials and hotspots


## Set-up a new workstation

clonezilla instructions, hostname, accounts, ssh keys

Install Linux Mint (or Ubuntu 24.04)
Create the `labadmin` and `participant` accounts
Configure passwordless auto-login for `participant`
Create `/data` with correct cross-account permissions
Install and configure eduroam with an assigned `userN` credential set
Install SSH server and deploy authorized keys from the NAS
Add the workstation to the relevant inventory file(s)
Smoke test against the new host

## Managing data on the Synology NAS
Directory layout under `/datadump`
Accessing the NAS from a workstation vs. from a personal machine
Retention and archival expectations
Restoring or re-syncing data if a backup step failed


## Troubleshooting
Workstation unreachable via Ansible
`/data` permission errors
eduroam connection dropped
Auto-login not working after reboot
Backup to NAS failed or incomplete
