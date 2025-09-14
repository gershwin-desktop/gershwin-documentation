# Gershwin ZFS Dataset Configuration

This guide explains how to configure ZFS datasets for Gershwin during manual installations, including `/System`, `/Local`, and user home datasets under `/Local/Users`.

---

## `/System` Dataset (Per Boot Environment)

`/System` should live **inside the active boot environment dataset** so changes can be rolled back when switching boot environments.

Determine the current boot environment dataset dynamically:

```sh
# Get the currently mounted boot environment dataset
BE_ROOT=$(zfs list -H -o name,mounted,mountpoint | awk '$2=="yes" && $3=="/"{print $1; exit}')
echo "Current BE dataset: $BE_ROOT"
```

Create the `/System` dataset inside it:

```sh
sudo zfs create -o mountpoint=/System ${BE_ROOT}/System
```

Verify:

```sh
zfs list | grep '/System'
```

This will inherit all ZFS properties from the boot environment dataset and be included automatically when creating or cloning boot environments.

---

## `/Local` Dataset (Shared Across Boot Environments)

`/Local` should be **shared across all boot environments** so locally installed software is persistent across rollbacks.

```sh
# Create /Local at the pool root
sudo zfs create -o mountpoint=/Local zroot/Local

# Verify
zfs list | grep '/Local'
```

---

## Migrating `/home` to `/Local/Users`

To match the Gershwin domain model, move all user datasets from `/home` to `/Local/Users`.  All users must be logged out and this must be done by logging in directly as root user to work.

### 1. Rename `/home` Dataset Recursively

This moves `/home` and its children into `/Local/Users` without copying data:

```sh
zfs rename -u zroot/home zroot/Local/Users
```

### 3. Adjust Mountpoints

Update mountpoints so users live directly under `/Local/Users`:

```sh
zfs set mountpoint=/Local/Users zroot/Local/Users/home
zfs set mountpoint=/Local/Users/jmaloney zroot/Local/Users/jmaloney
```

### 4. Update User Accounts

Point users to the new home directory path:

```sh
pw usermod jmaloney -d /Local/Users/jmaloney
```

### 5. Verify Migration

```sh
zfs list | grep Users
getent passwd jmaloney
```

Confirm that:
- Datasets are mounted under `/Local/Users`
- `getent passwd` shows `/Local/Users/jmaloney`
- You can log in and access your files