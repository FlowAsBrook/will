---
title: Kubernetes pkg
date: 2024-12-17 10:47:36
tags: pkg
categories: Kubernetes
---

# klog

## default log level

default use `klog.Info` is equivalent to `klog.V(0).Info`.

```shell
# by default klog uses level 0, so logs with log.V(1) won't appear unless you set --v=1 or a higher verbosity.

containers:
  - name: your-container
    image: your-image
    args:
      - "--v=1" # if not config this, default klog uses level 0
```



- For more detailed debug or trace messages, use klog.V(level).Info, where level is greater than 0 (e.g., klog.V(2).Info).

