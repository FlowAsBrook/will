---
title: Kubernetes delploy
date: 2024-08-05 00:37:47
tags: deploy
categories: Kubernetes
---

# k8s deploy

## install kubelet kubeadm kubectl

- Step1

```shell
# Set SELinux in permissive mode (effectively disabling it)
sudo setenforce 0
sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
```

- Step2

```shell
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.ustc.edu.cn/kubernetes/core:/stable:/v1.30/rpm/
enabled=1
gpgcheck=1
gpgkey=https://mirrors.ustc.edu.cn/kubernetes/core:/stable:/v1.30/rpm/repodata/repomd.xml.key
exclude=kubelet kubeadm kubectl cri-tools kubernetes-cni
EOF
```

- step3

```shell
sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
sudo systemctl enable --now kubelet
```

## install Container Runtimes

> https://www.cloudraft.io/blog/container-runtimes

```
Runtime	Path to Unix domain socket
containerd	unix:///var/run/containerd/containerd.sock
CRI-O	unix:///var/run/crio/crio.sock
Docker Engine (using cri-dockerd)	unix:///var/run/cri-dockerd.sock
```

- Enable ipv4 packet forward

```shell
# sysctl params required by setup, params persist across reboots
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.ipv4.ip_forward = 1
EOF

# Apply sysctl params without reboot
sudo sysctl --system

# Verify that net.ipv4.ip_forward is set to 1
sysctl net.ipv4.ip_forward
```

- cgroup driver

To set `systemd` as the cgroup driver, edit the [`KubeletConfiguration`](https://kubernetes.io/docs/tasks/administer-cluster/kubelet-config-file/) option of `cgroupDriver` and set it to `systemd`. For example:

```yaml
apiVersion: kubelet.config.k8s.io/v1beta1
kind: KubeletConfiguration
...
cgroupDriver: systemd
```

### containerd

- stop docker service (is necessary ?) 

- init config.toml

  ```shell
  sudo containerd config default | sudo tee /etc/containerd/config.toml
  ```

- config 

Configuring the `systemd` cgroup driver

`sudo vim /etc/containerd/config.toml`

```toml
#disabled_plugins = ["cri"]
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
  [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
    SystemdCgroup = true
[plugins."io.containerd.grpc.v1.cri"]
  sandbox_image = "registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.9"
  [plugins."io.containerd.grpc.v1.cri".registry]	
    [plugins."io.containerd.grpc.v1.cri".registry.mirrors]
      [plugins."io.containerd.grpc.v1.cri".registry.mirrors."docker.io"]
        endpoint=["https://registry-1.docker.io", "https://xxx.mirror.aliyuncs.com"]
      [plugins."io.containerd.grpc.v1.cri".registry.mirrors."registry.k8s.io"]
        endpoint=["https://xxx.mirror.aliyuncs.com", "https://k8s.m.daocloud.io", "https://docker.mirrors.ustc.edu.cn","https://hub-mirror.c.163.com"]
```

`sudo systemctl restart containerd`

- check containerd version

  ```shell
  containerd --version
  # containerd containerd.io 1.7.19 
  ```

### CRI-O (optional)

- set repo

  ```shell
  cat <<EOF | sudo tee /etc/yum.repos.d/cri-o.repo
  [cri-o]
  name=CRI-O
  baseurl=https://mirrors.ustc.edu.cn/kubernetes/addons:/cri-o:/stable:/v1.30/rpm/
  enabled=1
  gpgcheck=1
  gpgkey=https://mirrors.ustc.edu.cn/kubernetes/addons:/cri-o:/stable:/v1.30/rpm/repodata/repomd.xml.key
  EOF
  ```

- install

  ```shell
  dnf install -y container-selinux
  dnf install -y cri-o
  systemctl start crio.service
  ```

- bootstrap cluster

  ```shell
  swapoff -a
  modprobe br_netfilter
  sysctl -w net.ipv4.ip_forward=1
  
  kubeadm init
  ```

### Docker Engine (optional)

- install docker (existing)

- install `cri-dockerd`

  ```shell
  # extract tgz
  tar -xzvf cri-dockerd-0.3.15.amd64.tgz
  
  sudo mv ./cri-dockerd /usr/local/bin/
  wget https://raw.githubusercontent.com/Mirantis/cri-dockerd/master/packaging/systemd/cri-docker.service
  wget https://raw.githubusercontent.com/Mirantis/cri-dockerd/master/packaging/systemd/cri-docker.socket
  sudo mv cri-docker.socket cri-docker.service /etc/systemd/system/
  sudo sed -i -e 's,/usr/bin/cri-dockerd,/usr/local/bin/cri-dockerd,' /etc/systemd/system/cri-docker.service
  
  # enable service
  sudo systemctl daemon-reload
  sudo systemctl enable cri-docker.service
  sudo systemctl enable --now cri-docker.socket
  sudo systemctl status cri-docker.socket
  ```

### crictl

- Config containerd `vim /etc/crictl.yaml`

```yaml
runtime-endpoint: unix:///run/containerd/containerd.sock
image-endpoint: unix:///run/containerd/containerd.sock
timeout: 2
debug: true
pull-image-on-create: false
```

- verify mirror

```shell
sudo crictl info
```

## kubeadm deploy cluster

### Initializing  control-plane node

```shell
# use flannel as Pod network add-on (CNI plugin)
sudo kubeadm init  --apiserver-advertise-address=10.220.32.16 --pod-network-cidr=10.244.0.0/16  

# apply flannel(optional) CNI plugin 
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml

# sudo kubeadm config images pull --config kubeadm.conf

# sudo kubeadm init --config kubeadm.conf
```

- (optional) resert (back to kubeadm init)

  ```shell
  sudo kubeadm reset --cri-socket unix:///var/run/containerd/containerd.sock
  ```

- Use kubectl

  ```shell
   mkdir -p $HOME/.kube
   sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   sudo chown $(id -u):$(id -g) $HOME/.kube/config
  ```

- check the CoreDNS Pod is `Running`

  ```shell
  kubectl get pods --all-namespaces
  
  NAMESPACE     NAME                                READY   STATUS    RESTARTS   AGE
  kube-system   coredns-7b5944fdcf-mgwq2            1/1     Running   0          4h22m
  kube-system   coredns-7b5944fdcf-sxfvt            1/1     Running   0          4h22m
  kube-system   etcd-dingo7232                      1/1     Running   1          4h22m
  kube-system   kube-apiserver-dingo7232            1/1     Running   1          4h22m
  kube-system   kube-controller-manager-dingo7232   1/1     Running   1          4h22m
  kube-system   kube-proxy-w2lfk                    1/1     Running   0          4h22m
  kube-system   kube-scheduler-dingo7232            1/1     Running   1          4h22m
  ```

### join node

```shell
sudo kubeadm join --token <token> <control-plane-host>:<control-plane-port> --discovery-token-ca-cert-hash sha256:<hash>
```

- Check on control-plane

```shell
kubectl get nodes
```

### optional

- other node use kubectl

```shell
scp root@<control-plane-host>:/etc/kubernetes/admin.conf .
kubectl --kubeconfig ./admin.conf get nodes

# or set kubectl default config
scp root@<control-plane-host>:/etc/kubernetes/admin.conf ~/.kube/config
kubectl get nodes
```

