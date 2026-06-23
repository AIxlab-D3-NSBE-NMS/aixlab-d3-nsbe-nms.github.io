# Lab management and system administration

This page guides you on how to manage the lab. Make sure you've gone through [Infrastructure](infra.md) first.

## Onboarding a new AI X Lab employee
- [ ] Generate an SSH key (`ed25519`)
- [ ] Upload your public key to the workstations
- [ ] Verify access by pinging the fleet with Ansible
- [ ] Get access to the Synology NAS (credentials, share mounts)

## Equipment and Inventory

If you have internal access, equipment inventory is listed [here](https://novasbe365.sharepoint.com/:x:/r/sites/D3Institute/Shared%20Documents/01.%20AI%20Experimentation%20Lab/09.%20Equipment/D3_AIXLab_Inventory_2026.xlsx?d=w12fc23e8e89b419ba38152fd8f22bcc2&csf=1&web=1&e=SZdLSl).


## Internet access

There are 35 credentials attributed to the AI X Lab for eduroam access but WITHOUT Outlook access. These should be used for lab equipment and can be found in the storage server under `labadmin/home`.


## Set-up a new workstation

### System images via Clonezilla

Workstations are provisioned and backed up using PXE boot and Clonezilla. Restoring an image prepares a workstation from an existing baseline; saving an image captures a configured workstation as a reusable baseline on the Synology NAS.

=== "Restoring an image"

    Use this workflow when provisioning a workstation from an existing image. The process usually takes 10–30 minutes depending on image size.

    **Step 1 — Boot via PXE**

    Power on the workstation and open the boot menu immediately. The exact key depends on the BIOS, but it is usually one of ++f12++, ++f2++, ++esc++, or ++del++.

    ```text
    Boot device selection:

    ❯ PXE / Network Boot
      Internal SSD
      USB Storage
      BIOS setup
    ```

    The workstation should connect to the Synology PXE server and boot into Clonezilla Live.

    **Step 2 — Start Clonezilla**

    Select the default language and keyboard options unless the workstation requires a different layout.

    ```text
    Clonezilla main menu:

    ❯ Start Clonezilla
      Enter_shell
      Reboot
      Poweroff
    ```

    Then select image-based restore mode.

    ```text
    Clonezilla mode:

    ❯ device-image    work with disks or partitions using images
      device-device   work directly from a disk or partition to another disk or partition
    ```

    **Step 3 — Mount the image repository**

    Choose the storage backend that exposes the Synology `images` share. Use the protocol configured for the current PXE image repository.

    ```text
    Mount Clonezilla image directory:

    ❯ nfs_server      use NFS server
      ssh_server      use SSH server
      samba_server    use Samba server
      local_dev       use local device
    ```

    Use the Synology NAS as the image server.

    ```text
    Image repository:

    Server: 192.168.10.201
    Path:   /images
    ```

    Authenticate if prompted.

    **Step 4 — Select the workstation image**

    Browse the available images and select the standard workstation baseline for the experiment.

    ```text
    Available images:

    ❯ workstation-baseline
      linux-mint-22-baseline
      ubuntu-24-04-baseline
      windows-11-admin
    ```

    Before continuing, confirm the selected image details: expected OS, creation date, size, and included partitions.

    **Step 5 — Configure restore options**

    Use full-disk restore for normal workstation provisioning. Partition restore should only be used when you intentionally want to preserve or replace specific partitions.

    ```text
    Restore target:

    ❯ restoredisk       restore an image to local disk
      restoreparts      restore an image to local partitions
    ```

    Select the target disk, usually the internal drive.

    ```text
    Target disk:

    ❯ /dev/sda
      /dev/nvme0n1
    ```

    Optionally run the image integrity check before restoring.

    **Step 6 — Verify and execute**

    Review the restore summary carefully before confirming.

    ```text
    Restore summary:

    Source image: workstation-baseline
    Target disk:  /dev/sda
    Action:       overwrite target disk

    ❯ Continue
      Cancel
    ```

    !!! warning
        Confirm the target disk before continuing. The restore operation overwrites all data on the selected disk.

    Start the restore and wait until Clonezilla reports completion.

    **Step 7 — Reboot**

    When the restore is complete, reboot the workstation and remove any temporary boot override if needed.

    ```text
    Clonezilla finished:

    ❯ Reboot
      Poweroff
      Enter shell
    ```

    **Step 8 — Post-restore configuration**

    After the system image has been restored:

    - Set the hostname, e.g. `workstation-01`.
    - Create the `labadmin` and `participant` accounts.
    - Configure passwordless auto-login for `participant`.
    - Create `/data` with correct cross-account permissions.
    - Install and configure eduroam with an assigned `userN` credential set.
    - Install SSH server and deploy authorized keys from the NAS.
    - Add the workstation to the relevant inventory file(s).
    - Smoke test against the new host.

=== "Saving an image"

    Use this workflow after a workstation has been configured and tested. The saved image becomes a reusable baseline on the Synology NAS.

    !!! warning
        Before saving an image, remove experiment data from `/data`, clear temporary files, and make sure no participant data or personal credentials are stored on the workstation.

    **Step 1 — Prepare the source workstation**

    Boot into the installed operating system and verify that the machine is ready to become the reference image.

    ```text
    Pre-image checklist:

    ❯ System boots normally
      labadmin account works
      participant auto-login works
      /data exists with correct permissions
      SSH access works
      eduroam is configured
      No experiment data remains in /data
    ```

    Shut the workstation down after the checklist is complete.

    **Step 2 — Boot via PXE**

    Power on the workstation and open the boot menu immediately. Select network boot so the machine starts Clonezilla from the Synology PXE server. The boot menu key is usually one of ++f12++, ++f2++, ++esc++, or ++del++.

    ```text
    Boot device selection:

    ❯ PXE / Network Boot
      Internal SSD
      USB Storage
      BIOS setup
    ```

    The workstation should boot into Clonezilla Live.

    **Step 3 — Start Clonezilla**

    Select the default language and keyboard options unless the workstation requires a different layout.

    ```text
    Clonezilla main menu:

    ❯ Start Clonezilla
      Enter_shell
      Reboot
      Poweroff
    ```

    Then select image mode.

    ```text
    Clonezilla mode:

    ❯ device-image    work with disks or partitions using images
      device-device   work directly from a disk or partition to another disk or partition
    ```

    **Step 4 — Mount the image repository**

    Mount the Synology `images` share as the Clonezilla image directory.

    ```text
    Mount Clonezilla image directory:

    ❯ nfs_server      use NFS server
      ssh_server      use SSH server
      samba_server    use Samba server
      local_dev       use local device
    ```

    Use the Synology NAS as the image server.

    ```text
    Image repository:

    Server: 192.168.10.201
    Path:   /images
    ```

    Authenticate if prompted.

    **Step 5 — Choose save mode**

    Select whether to save the full disk or only selected partitions. For standard workstation images, prefer saving the full disk.

    ```text
    Clonezilla action:

    ❯ savedisk       save local disk as an image
      saveparts      save local partitions as an image
      restoredisk    restore an image to local disk
      restoreparts   restore an image to local partitions
    ```

    **Step 6 — Name the image**

    Choose a descriptive image name that includes the operating system, purpose, and date.

    ```text
    Image name:

    ❯ linux-mint-22-workstation-baseline-2026-06
      ubuntu-24-04-workstation-baseline-2026-06
      experiment-name-workstation-image-2026-06
    ```

    Use lowercase names with hyphens so images are easy to identify and script against.

    **Step 7 — Select the source disk**

    Select the internal disk that contains the configured workstation system.

    ```text
    Source disk:

    ❯ /dev/sda
      /dev/nvme0n1
    ```

    If both `/dev/sda` and `/dev/nvme0n1` are listed, confirm which one is the workstation's internal system disk before continuing.

    **Step 8 — Configure save options**

    Use Clonezilla's default compression and filesystem check options unless there is a specific reason to change them.

    ```text
    Save options:

    ❯ Use default compression
      Check and repair filesystem before saving
      Skip checking saved image
      Encrypt image
    ```

    For baseline images, enable image verification after saving if time allows.

    ```text
    After image is saved:

    ❯ Check the saved image
      Skip checking the saved image
    ```

    **Step 9 — Verify and execute**

    Review the save summary carefully before confirming.

    ```text
    Save summary:

    Source disk: /dev/sda
    Image path:  192.168.10.201:/images
    Image name:  linux-mint-22-workstation-baseline-2026-06
    Action:      save disk image

    ❯ Continue
      Cancel
    ```

    Start the image creation and wait until Clonezilla reports completion. This may take a long time depending on disk size and network speed.

    **Step 10 — Reboot and document the image**

    When the save operation is complete, reboot the workstation.

    ```text
    Clonezilla finished:

    ❯ Reboot
      Poweroff
      Enter shell
    ```

    After reboot, record the image name, source workstation, operating system, creation date, and intended use so future lab admins know which image to restore.

## Accessing and managing the Synology NAS
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
