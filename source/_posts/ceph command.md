---
title: ceph command
date: 2024-09-09 14:52:09
tags: command
categories: ceph
---

# deploy

## cephadm

```shell
# step1 monitor
cephadm bootstrap --mon-ip *<mon-ip>*

# ceph shell
sudo ./cephadm shell
```

# service

```shell
# check all vaild service
ceph orch ls

# check all vaild/invaild service
ceph orch ps
```

# cluster

```shell
# method 1
ceph fsid

# method 2
cat /etc/ceph/ceph.conf | grep fsid
```

# User

- check userId

  ```shell
  ceph auth list
  ```

# health

```shell
ceph df
```

# host

```shell
# list host
ceph orch host ls --detail

# add host
ssh-copy-id -f -i /etc/ceph/ceph.pub root@*<new-host>*
ceph orch host add *<newhost>* [*<ip>*] [*<label1> ...*]
# e.g.  ceph orch host add host2 10.10.0.102 --labels _admin

# remove host
ceph orch host drain *<host>*
ceph orch ps <host>  # daemons on the host
ceph orch host rm <host> # remove the host from the cluster after all daemons have been removed

# add storage
ceph orch apply osd --all-available-devices
```
# osd

```shell
# list
ceph orch device ls

# Creating New OSDs
ceph orch apply osd --all-available-devices
# or
ceph orch daemon add osd *<host>*:*<device-path>*
# e.g.
ceph orch daemon add osd host1:/dev/sdb
ceph orch daemon add osd host1:data_devices=/dev/sda,/dev/sdb,db_devices=/dev/sdc,osds_per_device=2
```

# RGW

> radosgw

```shell
# enable rgw
ceph mgr module enable rgw

# config rgw name e.g. foo
ceph orch apply rgw foo

# lable host 
ceph orch host label add gwhost1 rgw  # the 'rgw' label can be anything
ceph orch host label add gwhost2 rgw

# apply
ceph orch apply rgw foo '--placement=label:rgw count-per-host:2' --port=8000
```

# monitor

```shell
ceph mon dump
# <mon-ip>:6789
```

# Ceph Object Gateway
