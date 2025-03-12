---
title: podman snippet
date: 2025-02-10 16:06:03
tags: tool
categories: podman
---

- 不同用户下执行 `podman ps`，只能查看当前用户的运行容器（即使是root用户，也不能查看其他普通用户启用的容器信息）

# solution

## change default data dir

```shell
# check
podman info | grep 'GraphRoot'
```

