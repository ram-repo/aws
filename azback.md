# Azure VM Backup: Azure Portal Walkthrough

Step-by-step guide to configuring, running, and restoring Azure VM backups in the Azure portal.

> Portal menus and labels change over time. If a label differs slightly, look for the closest match.

---

## Overview of the Flow

| Step | Task | Where |
|---|---|---|
| 1 | Create a Recovery Services vault | Recovery Services vaults |
| 2 | Set storage redundancy | Vault > Properties |
| 3 | Create a backup policy | Vault > Backup policies |
| 4 | Enable backup on the VM | VM > Backup, or Vault > Backup |
| 5 | Run an on-demand backup | Vault > Backup items |
| 6 | Monitor jobs | Vault > Backup jobs |
| 7 | Restore | Backup item > Restore VM / File Recovery |
| 8 | Secure and stop | Vault > Properties, Backup item |

---

## Step 1: Create a Recovery Services Vault

1. Sign in to the [Azure portal](https://portal.azure.com).
2. Search for **Recovery Services vaults** and open it.
3. Select **Create**.
4. Fill in the Basics tab:

| Field | Guidance |
|---|---|
| Subscription | Same subscription as the VMs (or the one you manage backups from) |
| Resource group | Existing or new, for example `rg-backup-demo` |
| Vault name | For example `rsv-demo` |
| Region | **Same region as the VMs you will protect** |

5. Select **Review + create**, then **Create**.

> The vault must be in the same region as the VMs it protects.

---

## Step 2: Set Storage Redundancy (Before the First Backup)

1. Open the vault.
2. Go to **Settings > Properties**.
3. Under **Backup Configuration**, select **Update**.
4. Choose **Locally-redundant**, **Zone-redundant**, or **Geo-redundant**.
5. If you chose geo-redundant, optionally turn on **Cross Region Restore**.
6. Select **Save**.

| Option | Use when |
|---|---|
| LRS | Lowest cost, single-datacenter protection is enough |
| ZRS | Need protection from a zone outage |
| GRS | Need a copy in the paired region |
| GRS + Cross Region Restore | Need to restore in the paired region during an outage |

> Redundancy cannot be changed after items are protected.

---

## Step 3: Create a Backup Policy

1. In the vault, go to **Manage > Backup policies**.
2. Select **Add**.
3. Choose **Azure Virtual Machine** as the policy type.
4. Choose a **Policy sub type**: **Standard** or **Enhanced**.
5. Configure the policy:

| Setting | What to set |
|---|---|
| Policy name | For example `policy-daily-30d` |
| Backup schedule | Frequency, time, and time zone (Enhanced allows multiple per day) |
| Instant restore | Days to keep snapshots (Standard 1 to 5, Enhanced 1 to 30) |
| Retention of daily backup points | For example 30 days |
| Retention of weekly, monthly, yearly points | Optional, enable as needed |

6. Select **Create**.

---

## Step 4: Enable Backup on a VM

### Option A: From the VM

1. Open the VM.
2. Under **Operations**, select **Backup**.
3. Choose the **Recovery Services vault** (existing or create new).
4. Choose the **Policy sub type** and select a policy.
5. Select **Enable backup**.

### Option B: From the Vault

1. Open the vault and select **Backup** (or **+ Backup**).
2. **Where is your workload running?** Choose **Azure**.
3. **What do you want to back up?** Choose **Virtual machine**.
4. Select **Backup**.
5. Pick the **Policy**.
6. Select **Add** under Virtual Machines, tick the VMs, and select **OK**.
7. Select **Enable backup**.

> Option B is useful for protecting many VMs at once.

---

## Step 5: Run an On-Demand Backup

1. Open the vault.
2. Go to **Protected items > Backup items**.
3. Select **Azure Virtual Machine**.
4. Select the VM name.
5. Select **Backup now**.
6. Set **Retain Backup Till** and select **OK**.

You can also do this from the VM: **VM > Backup > Backup now**.

---

## Step 6: Monitor Backup Jobs

1. Open the vault.
2. Go to **Monitoring > Backup jobs**.
3. Review status, duration, and errors.

| Status | Meaning |
|---|---|
| In progress | Snapshot or data transfer running |
| Completed | Recovery point created |
| Completed with warnings | Recovery point created, review the warning |
| Failed | Open the job for the error code and fix the cause |

Other places to check health: the vault **Overview** page, **Backup items**, and **Business Continuity Center** for a cross-vault view.

---

## Step 7: Restore

### Restore a VM

1. Open the vault, go to **Protected items > Backup items > Azure Virtual Machine**, and select the VM.
2. Select **Restore VM**.
3. Select a **restore point**.
4. Choose **Restore configuration**:

| Option | What you provide |
|---|---|
| Create new | New VM name, resource group, virtual network, subnet, and a staging storage account |
| Replace existing | Staging storage account; disks on the current VM are swapped |
| Restore disks | Target resource group and staging storage account; you build the VM afterward |

5. Select **Restore** and monitor in **Backup jobs**.

> For a Cross Region Restore, switch to the **Secondary Region** view on the Backup items page and restore from there.

### Recover Individual Files

1. Open the backup item and select **File Recovery**.
2. Choose the **recovery point**.
3. Select **Download Executable** (Windows) or **Download Script** (Linux).
4. Run it on a machine that can reach the recovery point; the disks mount as drives.
5. Copy the files you need.
6. When finished, select **Unmount Disks** in the portal.

---

## Step 8: Secure and Manage

### Vault Security Settings

1. Open the vault, then **Settings > Properties**.
2. Under **Security Settings**, review:

| Setting | Purpose |
|---|---|
| Soft delete | Keep deleted backup data for recovery (14 days by default) |
| Immutable vault | Prevent changes that weaken retention or delete points |
| Multi-user authorization | Require a second approval via a Resource Guard |

### Stop Protection

1. Open the backup item and select **Stop backup**.
2. Choose:

| Choice | Result |
|---|---|
| Retain Backup Data | Stop future backups, keep existing recovery points |
| Delete Backup Data | Remove all recovery points (goes to soft delete first if enabled) |

---

## Quick Checklist

- [ ] Vault is in the same region as the VMs
- [ ] Redundancy set before the first backup
- [ ] Policy matches the retention requirement
- [ ] Backup enabled and first job completed
- [ ] Soft delete and immutability reviewed
- [ ] A test restore completed successfully

---

## Classroom Discussion

1. Why must the vault be in the same region as the VM? What does that mean for a multi-region design?
2. When would you enable backup from the vault instead of from each VM?
3. Which restore option would you pick after a bad patch broke a VM, and why?
4. What is the risk of choosing "Delete Backup Data" when stopping protection?
5. How would you prove to an auditor that restores actually work?

## Hands-on Lab Ideas

- Create a vault and a policy, protect a test VM, and run Backup now.
- Use File Recovery to restore one deleted file, then unmount the disks.
- Restore the VM using Create new, then compare it to the original.
- Turn on soft delete, delete a recovery point, and recover it.

## Related

- `azure-vm-backup.md` for concepts, policy types, tiers, and the CLI walkthrough