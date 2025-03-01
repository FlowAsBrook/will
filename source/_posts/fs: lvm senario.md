---
title: lvm senario
date: 2024-12-06 10:43:30
tags: lvm
categories: fs
---

# overview

<img src="/images/fs/lvm_overview-1.svg">

# create LVs 

>  If you want to create **three Logical Volumes (LVs)** of sizes 1TB, 1TB, and 5TB on a single Volume Group (VG) for each disk, here’s how you can do it step by step.

Assume:

**Disks**: /dev/nvme0n1, /dev/nvme1n1, /dev/nvme2n1

**Volume Groups**: vg_nvme0, vg_nvme1, vg_nvme2

**Logical Volumes (LVs)**: lv1, lv2, lv3 for each VG

**LV Sizes**: 1TB, 1TB, and 5TB

## 1. Create Physical Volumes (PVs)

```shell
# Initialize each disk as a PV:
sudo pvcreate /dev/nvme0n1
sudo pvcreate /dev/nvme1n1
sudo pvcreate /dev/nvme2n1
```

## 2. Create Volume Groups (VGs)

```shell
# Create a VG for each disk:
sudo vgcreate vg_nvme0 /dev/nvme0n1
sudo vgcreate vg_nvme1 /dev/nvme1n1
sudo vgcreate vg_nvme2 /dev/nvme2n1
```

## 3. Create Logical Volumes (LVs)

```shell
# Allocate 1TB, 1TB, and 5TB LVs from each VG.

# For vg_nvme0:
sudo lvcreate -L 1T -n lv1 vg_nvme0
sudo lvcreate -L 1T -n lv2 vg_nvme0
sudo lvcreate -L 5T -n lv3 vg_nvme0

# For vg_nvme1:
sudo lvcreate -L 1T -n lv1 vg_nvme1
sudo lvcreate -L 1T -n lv2 vg_nvme1
sudo lvcreate -L 5T -n lv3 vg_nvme1

# For vg_nvme2:
sudo lvcreate -L 1T -n lv1 vg_nvme2
sudo lvcreate -L 1T -n lv2 vg_nvme2
sudo lvcreate -L 5T -n lv3 vg_nvme2

# use whole vg
sudo lvcreate -l 100%FREE -n lv vg_xxx
```

## 4. Verify the Setup

```shell
# Check the LVs and their sizes:
sudo lvs
```

You should see something like:

```shell
LV   VG        Attr       LSize

lv1  vg_nvme0  -wi-a----- 1.00t                           
lv2  vg_nvme0  -wi-a----- 1.00t                           
lv3  vg_nvme0  -wi-a----- 5.00t                           
lv1  vg_nvme1  -wi-a----- 1.00t                           
lv2  vg_nvme1  -wi-a----- 1.00t                           
lv3  vg_nvme1  -wi-a----- 5.00t                           
lv1  vg_nvme2  -wi-a----- 1.00t                           
lv2  vg_nvme2  -wi-a----- 1.00t                           
lv3  vg_nvme2  -wi-a----- 5.00t
```

## 5. Format the LVs

```shell
# Use xfs or another filesystem to format the LVs:
sudo mkfs.xfs /dev/vg_nvme0/lv1
sudo mkfs.xfs /dev/vg_nvme0/lv2
sudo mkfs.xfs /dev/vg_nvme0/lv3

sudo mkfs.xfs /dev/vg_nvme1/lv1
sudo mkfs.xfs /dev/vg_nvme1/lv2
sudo mkfs.xfs /dev/vg_nvme1/lv3

sudo mkfs.xfs /dev/vg_nvme2/lv1
sudo mkfs.xfs /dev/vg_nvme2/lv2
sudo mkfs.xfs /dev/vg_nvme2/lv3
```

## 6. Mount the LVs

```shell
# Create directories for each LV and mount them:
sudo mkdir -p /mnt/nvme0/lv1 /mnt/nvme0/lv2 /mnt/nvme0/lv3
sudo mkdir -p /mnt/nvme1/lv1 /mnt/nvme1/lv2 /mnt/nvme1/lv3
sudo mkdir -p /mnt/nvme2/lv1 /mnt/nvme2/lv2 /mnt/nvme2/lv3

sudo mount /dev/vg_nvme0/lv1 /mnt/nvme0/lv1
sudo mount /dev/vg_nvme0/lv2 /mnt/nvme0/lv2
sudo mount /dev/vg_nvme0/lv3 /mnt/nvme0/lv3

sudo mount /dev/vg_nvme1/lv1 /mnt/nvme1/lv1
sudo mount /dev/vg_nvme1/lv2 /mnt/nvme1/lv2
sudo mount /dev/vg_nvme1/lv3 /mnt/nvme1/lv3

sudo mount /dev/vg_nvme2/lv1 /mnt/nvme2/lv1
sudo mount /dev/vg_nvme2/lv2 /mnt/nvme2/lv2
sudo mount /dev/vg_nvme2/lv3 /mnt/nvme2/lv3
```

## 7. Persist the Mounts

```shell
# Add the mounts to /etc/fstab for automatic remounting on boot:
echo '/dev/vg_nvme0/lv1 /mnt/nvme0/lv1 xfs defaults 0 0' | sudo tee -a /etc/fstab
echo '/dev/vg_nvme0/lv2 /mnt/nvme0/lv2 xfs defaults 0 0' | sudo tee -a /etc/fstab
echo '/dev/vg_nvme0/lv3 /mnt/nvme0/lv3 xfs defaults 0 0' | sudo tee -a /etc/fstab

echo '/dev/vg_nvme1/lv1 /mnt/nvme1/lv1 xfs defaults 0 0' | sudo tee -a /etc/fstab
echo '/dev/vg_nvme1/lv2 /mnt/nvme1/lv2 xfs defaults 0 0' | sudo tee -a /etc/fstab
echo '/dev/vg_nvme1/lv3 /mnt/nvme1/lv3 xfs defaults 0 0' | sudo tee -a /etc/fstab

echo '/dev/vg_nvme2/lv1 /mnt/nvme2/lv1 xfs defaults 0 0' | sudo tee -a /etc/fstab
echo '/dev/vg_nvme2/lv2 /mnt/nvme2/lv2 xfs defaults 0 0' | sudo tee -a /etc/fstab
echo '/dev/vg_nvme2/lv3 /mnt/nvme2/lv3 xfs defaults 0 0' | sudo tee -a /etc/fstab
```

```shell
# **Final Directory Layout**
# **Disk**	**VG Name**	**LV Name**	**Size**	**Mount Point**
/dev/nvme0n1	vg_nvme0	lv1	1T	/mnt/nvme0/lv1
		lv2	1T	/mnt/nvme0/lv2
		lv3	5T	/mnt/nvme0/lv3

/dev/nvme1n1	vg_nvme1	lv1	1T	/mnt/nvme1/lv1
		lv2	1T	/mnt/nvme1/lv2
		lv3	5T	/mnt/nvme1/lv3

/dev/nvme2n1	vg_nvme2	lv1	1T	/mnt/nvme2/lv1
		lv2	1T	/mnt/nvme2/lv2
		lv3	5T	/mnt/nvme2/lv3
```

> defaults 0 x
>
> The difference is in the last number (0 vs 2) in the fstab entry, which represents the filesystem check (fsck) pass number. Here's what these numbers mean:
>
> Last field (6th field) in fstab:
>
> - `0` = No filesystem check will be done at boot time
> - `1` = Filesystem will be checked first (typically used for root filesystem /)
> - `2` = Filesystem will be checked after pass 1 filesystems (typically used for other filesystems)
>
> So:
>
> - `defaults 0 0` means the filesystem will never be automatically checked during boot
> - `defaults 0 2` means the filesystem will be checked during boot, but after the root filesystem
>
> Best practices:
>
> - Use `0 1` for the root filesystem (/)
> - Use `0 2` for other important filesystems that should be checked
> - Use `0 0` for pseudo-filesystems (like proc, sysfs) or filesystems that don't need checking (like swap)

# wipe disk and create lvm

> assum dev is /dev/nvme2n1

To erase all partitions on the device /dev/nvme2n1 and create multiple logical volumes (LVs) using the LVM framework, follow these steps:

## 1. Verify Device and Backup Data

```shell
# Ensure you are working on the correct device. Erasing partitions will delete all data on the device.
sudo lsblk -o NAME,SIZE,TYPE,MOUNTPOINT /dev/nvme2n1
```

## 2. Erase Existing Partitions

```shell
# Clear the Partition Table, To wipe the partition table completely:
sudo wipefs -a /dev/nvme2n1

# Verify the Disk is Clean, Check that no partitions remain
sudo lsblk /dev/nvme2n1
```

## 3. Create Physical Volume (PV)

```shell
# Convert the entire disk into an LVM physical volume:
sudo pvcreate /dev/nvme2n1

# Verify the PV
sudo pvdisplay
```

## 4. Create Volume Group (VG)

```shell
# Create a volume group that spans the entire disk:
sudo vgcreate vg_nvme2n1 /dev/nvme2n1

# Verify the VG:
sudo vgdisplay
```

## 5. Create Logical Volumes (LVs)

Example: Create Three LVs

**LV1**: 1TB

**LV2**: 1TB

**LV3**: Remaining space

```shell
sudo lvcreate -L 1T -n lv1 vg_nvme2n1
sudo lvcreate -L 1T -n lv2 vg_nvme2n1
sudo lvcreate -l 100%FREE -n lv3 vg_nvme2n1 --wipesignatures y
# 注意如果在lvcreate的时候一直提示 warning wipe offset xxx,那执行 sudo lvcreate xxx -y (加-y参数)

# Verify the LVs:
sudo lvdisplay
```

## 6. Format Logical Volumes

```shell
# Format each logical volume with your desired file system (e.g., XFS):
sudo mkfs.xfs /dev/vg_nvme2n1/lv1
sudo mkfs.xfs /dev/vg_nvme2n1/lv2
sudo mkfs.xfs /dev/vg_nvme2n1/lv3
```

## 7. Mount Logical Volumes

```shell
# Create mount points and mount the LVs:
sudo mkdir -p /mnt/nvme2n1/lv1 /mnt/nvme2n1/lv2 /mnt/nvme2n1/lv3

sudo mount /dev/vg_nvme2n1/lv1 /mnt/nvme2n1/lv1
sudo mount /dev/vg_nvme2n1/lv2 /mnt/nvme2n1/lv2
sudo mount /dev/vg_nvme2n1/lv3 /mnt/nvme2n1/lv3
# Verify the mounts:
df -h
```

## 8. Make the Mounts Persistent

```shell
# Add entries to /etc/fstab to ensure the LVs are mounted on reboot:
echo '/dev/vg_nvme2n1/lv1 /mnt/nvme2n1/lv1 xfs defaults 0 2' | sudo tee -a /etc/fstab
echo '/dev/vg_nvme2n1/lv2 /mnt/nvme2n1/lv2 xfs defaults 0 2' | sudo tee -a /etc/fstab
echo '/dev/vg_nvme2n1/lv3 /mnt/nvme2n1/lv3 xfs defaults 0 2' | sudo tee -a /etc/fstab
```

# wipe existed lv

## 1. Check What’s Using the LV

First, identify what is still using the LV:

```
sudo lsof | grep /dev/mapper/<lv-name>
```

Also, check active processes using the device:

```
sudo fuser -m /dev/mapper/<lv-name>
```

If any process is using the LV, stop it:

```
sudo kill -9 <PID>
```

## 2. Unmount If Mounted

Check if the LV is mounted:

```
mount | grep /dev/mapper/<lv-name>
```

If it is mounted, unmount it:

```
sudo umount -l /dev/mapper/<lv-name>
```

Use -l (lazy unmount) to force unmount if needed.

## 3. Disable the LV

Before removing the LV, **deactivate it**:

```shell
sudo lvchange -an /dev/<vg-name>/<lv-name>
```

Now try to remove it:

```shell
sudo dmsetup remove /dev/mapper/<lv-name>
```

## 4. Forcefully Remove LV, VG, and PV

If the above steps don’t work, forcefully remove everything:

```
sudo lvremove -f /dev/<vg-name>/<lv-name>
sudo vgremove -f <vg-name>
sudo pvremove -f /dev/<device-name>
```

Check again with:

```
lsblk
sudo vgs
sudo lvs
```

## 5. If Everything Fails

If none of the above works, the safest option is to **reboot**:

```
sudo wipefs --all --force /dev/<device-name>
sudo reboot
```
