---
title: podman snippet
date: 2025-02-10 16:06:03
tags: tool
categories: podman
---

- 不同用户下执行 `podman ps`，只能查看当前用户的运行容器（即使是root用户，也不能查看其他普通用户启用的容器信息）

# solution

## change default data dir

- **rootful mode** 

  > Default graphroot: /var/lib/containers/storage.

```shell
# check
podman info | grep 'GraphRoot'

# Edit Podman’s Storage Configuration
sudo mkdir -p /etc/containers
sudo vim /etc/containers/storage.conf 

# edit storage.conf
[storage]
driver = "overlay"
graphroot = "//mnt/podman-data"
```

- **rootless mode**

  > Default graphroot: ~/.local/share/containers/storage.

```shell
# check
podman info | grep 'GraphRoot'

# Edit Podman’s Storage Configuration
sudo mkdir -p ~/.config/containers
sudo vim ~/.config/containers/storage.conf  

# edit storge.conf
[storage]
driver = "overlay"
graphroot = "/mnt/podman-data"
```





## potentially insufficient UIDs or GIDs available in user namespace

>  If the requested UID/GID still falls outside or Podman needs more mappings, you can edit /etc/subuid and /etc/subgid (as root) to increase the range

```shell
# Increase UID/GID Range (Optional):
sudo vim /etc/subuid  # dingofs:60000:131072
sudo vim /etc/subgid  # dingofs:60000:131072

podman system migrate
```



