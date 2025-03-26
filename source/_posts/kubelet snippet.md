---
title: kubelet snippet
date: 2025-03-25 14:58:19
tags: kubelet
categories: kubernetes
---

# troubleshooting

## Version from runtime service failed

```shell
E0325 12:15:10.889620 3185179 remote_runtime.go:189] "Version from runtime service failed" err="rpc error: code = Unimplemented desc = unknown service runtime.v1alpha2.RuntimeService"
E0325 12:15:10.889663 3185179 kuberuntime_manager.go:226] "Get runtime version failed" err="get remote runtime typed version failed: rpc error: code = Unimplemented desc = unknown service runtime.v1alpha2.RuntimeService"
E0325 12:15:10.889683 3185179 run.go:74] "command failed" err="failed to run Kubelet: failed to create kubelet: get remote runtime typed version failed: rpc error: code = Unimplemented desc = unknown service runtime.v1alpha2.RuntimeService"
```

- resolve

  ```shell
  复制正常节点 /etc/containerd/config.toml 配置文件，然后重启 containerd
  sudo systemctl restart containerd
  
  kubelet服务会自动拉起
  ```

  