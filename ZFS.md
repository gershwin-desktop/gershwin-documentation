# Gershwin ZFS Dataset Configuration

This guide explains how to configure ZFS datasets for Gershwin during manual installations, including `/System`, `/Local`, and `/Network`.  

It is important to configure datasets on ZFS on root installations.  Otherwise data in /Local and /Network as folders would get lost between boot environments.  For this reason we only create `/System` in the default boot environment to be cloned to other boot environments.  The `/Local` and `/Network` datasets are then shared between boot environments.

---

## `/System` Dataset

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

## `/Local` Dataset

`/Local` should be **shared across all boot environments** so locally installed software is persistent across rollbacks.

```sh
# Create /Local at the pool root
sudo zfs create -o mountpoint=/Local zroot/Local

# Verify
zfs list | grep '/Local'
```

## `/Network` Dataset

`/Network` should also be **shared across all boot environments** so installed software is persistent across rollbacks.

```sh
sudo zfs create -o mountpoint=/Network zroot/Network
```