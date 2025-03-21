---
title: kubectl command
date: 2024-09-15 15:45:47
tags: command
categories: kubectl
---

# common

##  version

```shell
kubectl version
kubectl api-versions
```

## health

```shell
# check kubernetes inner ip 
kubectl get svc kubernetes -n default

# check api server
kubectl get componentstatuses

# check crd
kubectl get crd | grep cert-manager

# check all pods
kubectl get pods -A
```

## namespace

```bash
kubectl get namespaces

# delete all resource by specify namespace
kubectl delete all --all -n <namespace>
```

# node

## resource 

```shell
# method 1: Check Node Resource Availability
kubectl describe node <node-name>  # check Capacity, Allocatable, Allocated resources
	•	CPU available = Allocatable CPU - CPU Requests
	•	Memory available = Allocatable Memory - Memory Requests

# method 2: Get Detailed Resource Requests & Limits for All Pods
kubectl get pods -A -o=custom-columns="NAMESPACE:.metadata.namespace,NAME:.metadata.name,CPU_REQUESTS:.spec.containers[*].resources.requests.cpu,MEM_REQUESTS:.spec.containers[*].resources.requests.memory,CPU_LIMITS:.spec.containers[*].resources.limits.cpu,MEM_LIMITS:.spec.containers[*].resources.limits.memory"

# method 3:  Output for Advanced Parsing json
kubectl get nodes -o json | jq '.items[] | {name: .metadata.name, allocatable: .status.allocatable}'
```



## label

```shell
# show all labels
kubectl get nodes --show-labels

# add label on node
kubectl label node <node-name> <label-key>=<label-value>

# remove label from node
kubectl label node <node-name> <label-key>-
# kubectl label nodes sd-shangdi-ceph17 dingofs-csi-node-
```

## taint

```shell
# check node is taint or not
kubectl describe node <nodeName> | grep -i taint

# taint node print
Taints: nodepool=fault:NoSchedule

# normal node print
Taints: <none>

# tag taint
kubectl taint nodes <node> nodepool=fault:NoSchedule

# remove taint
kubectl taint nodes <node-name> <key>:<value>-
```

# All

```shell
# delete all resource
kubectl delete all --all -n <namespace>
```

# pod

## basic

```shell
# list namespace's pod
kubectl get pod -n {namespace}

# describe
kubectl describe pod {podName}
```

## log

> -c <container-name>: Specify which container to retrieve logs from.
>
> -f: Stream the logs in real-time.
>
> --previous: Show logs from the last terminated container.
>
> --since=<duration>: Return logs for the last period (e.g., 1h, 30m).
>
> --tail=<lines>: Limit the number of log lines returned.
>
> --all-containers=true: Get logs from all containers in the pod.

```shell
# pod
kubectl logs -f {podId} -n {namespace}

# pod's container
kubectl logs <pod-name> -c <container-name> -n <namespace>
```

## config

```shell
kubectl get pod <容器id> --kubeconfig=/path/to/configfile -o yaml > env-vq48.yaml
# kubectl get -o yaml 这样的参数，会将指定的 Pod API 对象以 YAML 的方式展示出来。
# expose
kubectl get pod <pod-name> -n <namespace> -o yaml > pod-config.yaml
```

## exec

without kubeconfig

```shell
# enter pod on specify container
kubectl exec -it {pod_id} -n {namespace} -c {container_id} -- sh

# execute specify command on pod
kubectl exec -it {pod_id} -n {namespace} -c {container_id} -- <shell>
e.g. kubectl exec -it csi-node-1 -n dingofs -- cat /etc/resolv.conf
```

## copy

```shell
kubectl cp 命令空间/容器id:/path/to/source_file ./path/to/local_file
```

## delete

```shell
# method 1
kubectl delete pod {pod_name} --grace-period=0 --force -n {namespace}

# method 2
kubectl patch pod <pod-name> -n <target-namespace> -p '{"metadata":{"finalizers":null}}' --type=merge

# method 3
step1 : kubectl edit pod <pod-name> -n <namespace>
step2 : delete the finalizers array under metadata, save the file, and exit
```

| **Aspect**      | **`kubectl patch`**                  | **`kubectl edit`**                        |
| --------------- | ------------------------------------ | ----------------------------------------- |
| **Interaction** | Non-interactive (scriptable).        | Interactive (manual editing).             |
| **Precision**   | Targets only the `finalizers` field. | Requires manually deleting `finalizers`.  |
| **Use Case**    | Automation (e.g., CI/CD pipelines).  | Debugging or ad-hoc fixes.                |
| **Safety**      | Risk of typos in JSON syntax.        | Risk of accidental edits to other fields. |

# statefulset

```shell
# restart
kubectl rollout restart StatefulSet/dingofs-csi-controller -n dingofs
```

# daemonsets

```shell
kubectl get daemonsets --all-namespaces

# restart 
kubectl rollout restart daemonset/dingofs-csi-node -n dingofs
```

# service

```shell
kubectl get svc -o wide -n <namespace>
```

# storage

## CSIDriver

```shell
kubectl get csidrivers
```

## storageclass

```shell
kubectl get storageclass

# delete 
kubectl delete storageclass <sc-name>
```

## pvc

```shell
kubectl get pvc
```

## pv

```shell
# list pv
kubectl get pv

# delete pv
## step1: change stragety
kubectl patch pv PV_NAME -p '{"spec":{"persistentVolumeReclaimPolicy":"Delete"}}'

## step2: delete
kubectl delete pv PV_NAME

# delete multiply pv
kubectl get pv --no-headers | grep <NAME> | awk '{print $1}' | xargs kubectl delete pv

# delete released status pvc
kubectl get pv -o json | jq -r '.items[] | select(.status.phase=="Released") | .metadata.name' | xargs kubectl delete pv
```

# RBAC

```shell
# list role
kubectl get role -n <namespace>  # -o yaml

# list clusterrole
kubectl get clusterrole

# list RoleBinding
kubectl get rolebinding -n <namespace>

# list clusterrolebinding
kubectl get clusterrolebinding

# Check if the User Can List Pods in Namespace
kubectl auth can-i list pods --as=<userName> -n <namespace>

# Check If the ServiceAccount Has Permissions to Get DaemonSets
kubectl auth can-i get daemonsets --as=system:serviceaccount:<namespace>:<serviceAccount> -n <namespace>
```

