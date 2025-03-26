---
title: ceph fs
date: 2025-03-26 16:45:16
tags: fs
categories: ceph
---


# Prerequisites
Before mounting CephFS, ensure that
1. the client host (where CephFS has to be mounted and used) has a copy of the Ceph configuration file (i.e. ceph.conf)
2. a keyring of the CephX user that has permission to access the MDS.
both of these files must already be present on the host where the Ceph MON resides.

- Generate a minimal conf file for the client host and place it at a standard location:
```shell
# on client host
mkdir -p -m 755 /etc/ceph
ssh {user}@{mon-host} "sudo ceph config generate-minimal-conf" | sudo tee /etc/ceph/ceph.conf
chmod 644 /etc/ceph/ceph.conf
```

- Create a CephX user and get its secret key:
```shell
ssh {user}@{mon-host} "sudo ceph fs authorize cephfs-1 client.dongwei / rw" | sudo tee /etc/ceph/ceph.client.dongwei.keyring
```
> In above command, replace cephfs with the name of your CephFS, foo by the name you want for your CephX user and / by the path within your CephFS for which you want to allow access to the client host and rw stands for both read and write permissions.

- Ensure that the keyring has appropriate permissions:
```shell
chmod 600 /etc/ceph/ceph.client.dongwei.keyring
```


# Create Pool/FS

- Creating pools

  > A Ceph file system requires at least two RADOS pools, one for data and one for metadata.

  ```shell
  ceph osd pool create cephfs_data
  ceph osd pool create cephfs_metadata
  ```

- Creating the file system

  ```shell
  ceph fs new cephfs-1 cephfs_metadata cephfs_data
  ```

- Check the status of the file system

  ```shell
  ceph fs status
  ```

- check mds status

  ```shell
  ceph mds stat
  ```

- Using Erasure Coded pools with CephFS（optional）

  ```shell
  ceph osd pool set my_ec_pool allow_ec_overwrites true
  ```

# Mount CephFS using Kernel Driver

```shell
# mount -t ceph {device-string}={path-to-mounted} {mount-point} -o {key-value-args} {other-args}
# mount -t ceph <name>@<fsid>.<fs_name>=/ /mnt/mycephfs
# fsid is the FSID of the Ceph cluster, which can be found using the `ceph fsid` command.

mkdir /mnt/mycephfs

# not use mount.helper
mount -t ceph dongwei@b3acfc0d-575f-41d3-9c91-0e7ed3dbb3fa.cephfs-1=/ -o mon_addr=192.168.0.1:6789,secret=AQATSKdNGBnwLhAAnNDKnH65FmVKpXZJVasUeQ==

# use mount.helper
mount -t ceph dongwei@.cephfs-1=/ /mnt/cephfs -o secret=AQATSKdNGBnwLhAAnNDKnH65FmVKpXZJVasUeQ==

# use secret file and Multiple monitor hosts
mount -t ceph cephuser@.cephfs=/ /mnt/mycephfs -o
mon_addr=192.168.0.1:6789/192.168.0.2:6789,secretfile=/etc/ceph/cephuser.secret
```

- check

  ```shell
  dongwei@6a65c746-e532-11ef-8ac2-fa7c097efb00.cephfs-1=/  190G     0  190G   0% /mnt/cephfs
  ```

# Mount CephFS using FUSE

- install ceph-fuse

  ```shell
  # Install the Ceph release RPM (adjust 'reef' as needed)
  sudo rpm -Uvh https://download.ceph.com/rpm-reef/el9/noarch/ceph-release-1-1.el9.noarch.rpm
  
  # Import the Ceph GPG key
  sudo rpm --import 'https://download.ceph.com/keys/release.asc'
  
  sudo dnf install -y ceph-fuse
  ```
  
- mount

  ```shell
  # ceph-fuse {mount point} {options}
  mkdir /mnt/mycephfs
  ceph-fuse --id dongwei /mnt/mycephfs  # mount default fs
  
  ceph-fuse --id dongwei --client_fs cephfs-1 /mnt/mycephfs # mount specify fs
  ```

- check

  ```shell
  ceph-fuse	190G     0  190G   0% /mnt/cephfs2
  ```

  

