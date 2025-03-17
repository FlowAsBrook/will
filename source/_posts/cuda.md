---
title: nvidia cuda
date: 2025-03-12 15:59:26
tags: nvidia
categories: cuda
---

# version

```shell
GPU  	driver	cuda		vllm	 	torch
h100	550			12.4
h200	565			12.7		0.7.3		2.5.1+cu124
h200	570			12.8		0.7.3		2.5.1+cu124
```

# install

## CUDA Toolkit

> https://developer.nvidia.com/cuda-toolkit-archive

- local

  ```shell
  wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-ubuntu2204.pin
  
  sudo mv cuda-ubuntu2204.pin /etc/apt/preferences.d/cuda-repository-pin-600
  
  wget https://developer.download.nvidia.com/compute/cuda/12.8.1/local_installers/cuda-repo-ubuntu2204-12-8-local_12.8.1-570.124.06-1_amd64.deb
  
  sudo dpkg -i cuda-repo-ubuntu2204-12-8-local_12.8.1-570.124.06-1_amd64.deb
  
  sudo cp /var/cuda-repo-ubuntu2204-12-8-local/cuda-*-keyring.gpg /usr/share/keyrings/
  
  sudo apt-get update
  
  sudo apt-get -y install cuda-toolkit-12-8
  ```

- online

  ```shell
  wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-keyring_1.1-1_all.deb
  sudo dpkg -i cuda-keyring_1.1-1_all.deb
  sudo apt-get update
  sudo apt-get -y install cuda-toolkit-12-8
  ```

### post operate

```shell
# config env
export PATH=/usr/local/cuda/bin:$PATH

# NVIDIA persistence daemon
sudo systemctl start nvidia-persistenced
```

## driver

```shell
sudo apt-get install -y cuda-drivers	
```

## nvidia-fabricmanager

```shell
sudo apt install -y nvidia-fabricmanager-570

sudo systemctl start nvidia-fabricmanager
```

# ENV

> resolve issues: Error 802: system not yet initialized

```shell
# sort GPUs, by ordering their IDs with IDs on the PCIe bus.
export CUDA_DEVICE_ORDER="PCI_BUS_ID" 
# perform an availability check using NVML (NVIDIA Management Library). NVML is an API layer for obtaining data directly from the NVIDIA-smi utility.
export PYTORCH_NVML_BASED_CUDA_CHECK=1 
# force show the system the IDs of available GPUs.
export CUDA_VISIBLE_DEVICES=0,1,2,3,4,5,6,7
```

# cuda kernel model

- check by `lsmod | grep nvidia`

| **Module**     | **Description**                  |
| -------------- | -------------------------------- |
| nvidia_uvm     | NVIDIA’s Unified Memory driver   |
| nvidia_drm     | Direct Rendering Manager support |
| nvidia_modeset | Kernel mode-setting support      |
| nvidia         | Main NVIDIA driver module        |

# command

## nvidia-smi

- Enable Persistence Mode

  ```shell
  sudo nvidia-smi -pm 1
  ```
  
- check state

  ```shell
  nvidia-smi conf-compute -grs
  # Confidential Compute GPUs Ready state: not-ready
  # Confidential Compute GPUs Ready state: ready
  
  # if above state is not-ready, execute below cmd
  nvidia-smi conf-compute -srs 1
  ```

- cuda 12.8
```shell
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 570.124.06             Driver Version: 570.124.06     CUDA Version: 12.8     |
|-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  NVIDIA H200                    On  |   00000000:19:00.0 Off |                    0 |
| N/A   23C    P0             76W /  700W |       1MiB / 143771MiB |      0%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
|   1  NVIDIA H200                    On  |   00000000:3B:00.0 Off |                    0 |
| N/A   21C    P0             75W /  700W |       1MiB / 143771MiB |      0%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
|   2  NVIDIA H200                    On  |   00000000:4C:00.0 Off |                    0 |
| N/A   23C    P0             76W /  700W |       1MiB / 143771MiB |      0%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
|   3  NVIDIA H200                    On  |   00000000:5D:00.0 Off |                    0 |
| N/A   24C    P0             77W /  700W |       1MiB / 143771MiB |      0%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
|   4  NVIDIA H200                    On  |   00000000:9B:00.0 Off |                    0 |
| N/A   24C    P0             75W /  700W |       1MiB / 143771MiB |      0%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
|   5  NVIDIA H200                    On  |   00000000:BB:00.0 Off |                    0 |
| N/A   23C    P0             77W /  700W |       1MiB / 143771MiB |      0%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
|   6  NVIDIA H200                    On  |   00000000:CB:00.0 Off |                    0 |
| N/A   24C    P0             76W /  700W |       1MiB / 143771MiB |      0%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
|   7  NVIDIA H200                    On  |   00000000:DB:00.0 Off |                    0 |
| N/A   24C    P0             76W /  700W |       1MiB / 143771MiB |      0%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
                                                                                         
+-----------------------------------------------------------------------------------------+
| Processes:                                                                              |
|  GPU   GI   CI              PID   Type   Process name                        GPU Memory |
|        ID   ID                                                               Usage      |
|=========================================================================================|
|  No running processes found                                                             |
+-----------------------------------------------------------------------------------------+
```

- cuda 12.7

```shell
Fri Mar 14 10:23:56 2025       
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 565.57.01              Driver Version: 565.57.01      CUDA Version: 12.7     |
|-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  NVIDIA H200                    On  |   00000000:19:00.0 Off |                    0 |
| N/A   26C    P0            111W /  700W |  134402MiB / 143771MiB |      0%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
|   1  NVIDIA H200                    On  |   00000000:3B:00.0 Off |                    0 |
| N/A   25C    P0            116W /  700W |  132320MiB / 143771MiB |      0%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
|   2  NVIDIA H200                    On  |   00000000:4C:00.0 Off |                    0 |
| N/A   25C    P0            112W /  700W |  132320MiB / 143771MiB |      0%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
|   3  NVIDIA H200                    On  |   00000000:5D:00.0 Off |                    0 |
| N/A   27C    P0            115W /  700W |  132320MiB / 143771MiB |      0%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
|   4  NVIDIA H200                    On  |   00000000:9B:00.0 Off |                    0 |
| N/A   27C    P0            115W /  700W |  132320MiB / 143771MiB |      0%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
|   5  NVIDIA H200                    On  |   00000000:BB:00.0 Off |                    0 |
| N/A   26C    P0            114W /  700W |  132320MiB / 143771MiB |      0%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
|   6  NVIDIA H200                    On  |   00000000:CB:00.0 Off |                    0 |
| N/A   26C    P0            113W /  700W |  132320MiB / 143771MiB |      0%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
|   7  NVIDIA H200                    On  |   00000000:DB:00.0 Off |                    0 |
| N/A   24C    P0            114W /  700W |  131840MiB / 143771MiB |      0%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
                                                                                         
+-----------------------------------------------------------------------------------------+
| Processes:                                                                              |
|  GPU   GI   CI        PID   Type   Process name                              GPU Memory |
|        ID   ID                                                               Usage      |
|=========================================================================================|
+-----------------------------------------------------------------------------------------+
```



# vllm

## install offline

On your local machine, create a virtual environment:

```
python3 -m venv vllm_env
source vllm_env/bin/activate
```

1️⃣ **On your local machine:**

```shell
pip download --dest=./vllm_deps vllm
```

2️⃣ **Transfer dependencies to the remote server:**

```shell
scp -r vllm_deps user@remote_server:/path/to/destination/
```

3️⃣ **On the remote server:**

```shell
cd /path/to/destination/vllm_deps
pip install --no-index --find-links=. vllm*
	•	--no-index tells pip not to use the internet.
	•	--find-links=./vllm_deps tells pip to look for packages in this directory.
	•	vllm* ensures pip finds the correct package in that folder.
```

## running time 

```shell
vllm serve /mnt/dingofs-test/DeepSeek-R1 --host 0.0.0.0 --port 8000 --served-model-name deepseek-r1 --tensor-parallel-size 8 --gpu-memory-utilization 0.85 --max-model-len 128000 --max-num-batched-tokens 32000 --max-num-seqs 1024 --trust-remote-code --enable-reasoning --reasoning-parser deepseek_r1
```

```shell
Sat Mar 15 20:33:04 2025       
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 570.124.06             Driver Version: 570.124.06     CUDA Version: 12.8     |
|-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  NVIDIA H200                    On  |   00000000:19:00.0 Off |                    0 |
| N/A   26C    P0            115W /  700W |   84474MiB / 143771MiB |      1%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
|   1  NVIDIA H200                    On  |   00000000:3B:00.0 Off |                    0 |
| N/A   24C    P0            113W /  700W |   84522MiB / 143771MiB |      1%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
|   2  NVIDIA H200                    On  |   00000000:4C:00.0 Off |                    0 |
| N/A   26C    P0            114W /  700W |   84522MiB / 143771MiB |      1%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
|   3  NVIDIA H200                    On  |   00000000:5D:00.0 Off |                    0 |
| N/A   27C    P0            117W /  700W |   84522MiB / 143771MiB |      1%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
|   4  NVIDIA H200                    On  |   00000000:9B:00.0 Off |                    0 |
| N/A   27C    P0            114W /  700W |   84522MiB / 143771MiB |      1%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
|   5  NVIDIA H200                    On  |   00000000:BB:00.0 Off |                    0 |
| N/A   26C    P0            116W /  700W |   84522MiB / 143771MiB |      1%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
|   6  NVIDIA H200                    On  |   00000000:CB:00.0 Off |                    0 |
| N/A   27C    P0            115W /  700W |   84522MiB / 143771MiB |      1%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
|   7  NVIDIA H200                    On  |   00000000:DB:00.0 Off |                    0 |
| N/A   26C    P0            114W /  700W |   84282MiB / 143771MiB |      1%      Default |
|                                         |                        |             Disabled |
+-----------------------------------------+------------------------+----------------------+
                                                                                         
+-----------------------------------------------------------------------------------------+
| Processes:                                                                              |
|  GPU   GI   CI              PID   Type   Process name                        GPU Memory |
|        ID   ID                                                               Usage      |
|=========================================================================================|
|    0   N/A  N/A          915920      C   ...niconda3/envs/vllm/bin/python      84464MiB |
|    1   N/A  N/A          916338      C   ...niconda3/envs/vllm/bin/python      84512MiB |
|    2   N/A  N/A          916339      C   ...niconda3/envs/vllm/bin/python      84512MiB |
|    3   N/A  N/A          916340      C   ...niconda3/envs/vllm/bin/python      84512MiB |
|    4   N/A  N/A          916341      C   ...niconda3/envs/vllm/bin/python      84512MiB |
|    5   N/A  N/A          916342      C   ...niconda3/envs/vllm/bin/python      84512MiB |
|    6   N/A  N/A          916343      C   ...niconda3/envs/vllm/bin/python      84512MiB |
|    7   N/A  N/A          916344      C   ...niconda3/envs/vllm/bin/python      84272MiB |
+-----------------------------------------------------------------------------------------+
```

- log

  ```shell
  INFO 03-15 20:36:48 worker.py:267] Memory profiling takes 7.63 seconds
  INFO 03-15 20:36:48 worker.py:267] the current vLLM instance can use total_gpu_memory (139.81GiB) x gpu_memory_utilization (0.85) = 118.84GiB
  INFO 03-15 20:36:48 worker.py:267] model weights take 83.88GiB; non_torch_memory takes 7.16GiB; PyTorch activation peak memory takes 6.37GiB; the rest of the memory reserved 
  for KV Cache is 21.43GiB.
  INFO 03-15 20:36:48 executor_base.py:111] # cuda blocks: 18418, # CPU blocks: 3437
  INFO 03-15 20:36:48 executor_base.py:116] Maximum concurrency for 128000 tokens per request: 2.30x
  ```

# sglang

## install offline

```shell
# prepare env
python3 -m venv sglang_env
source sglang_env/bin/activate
# optional use uv
pip install --upgrade pip
#pip install uv
# download deps
mkdir -p ./sglang_deps
pip download "sglang[all]>=0.4.4.post1" --find-links https://flashinfer.ai/whl/cu124/torch2.5/flashinfer-python -d ./sglang_deps
# scp deps to remote
scp -r sglang_deps user@remote_server:/path/to/destination/
# install sglang on remote
cd /path/to/remote/sglang_deps
pip install --no-index --find-links=. "sglang[all]>=0.4.4.post1"
```

## runtime

```shell
python3 -m sglang.launch_server --model /mnt/3fs/DeepSeek-R1 --tp 8 --trust-remote-code --port 30000
```

# torch

## install offline

```shell
mkdir ~/torch_deps
pip download --dest=~/torch_deps torch==2.5.1 --extra-index-url https://download.pytorch.org/whl/nightly/cu128

scp -r ~/torch_deps user@remote_server:/path/to/remote/directory
cd /path/to/remote/directory
pip install --no-index --find-links=./ torch
```

- check

  ```shell
  python -c "import torch; print(torch.cuda.is_available()); print(torch.cuda.device_count())"
  ```

  or

  ```python
  import torch
  print(torch.cuda.is_available())  # print false
  print(torch.cuda.device_count())  # print 8
  print(torch.__version__)  # print 2.5.1+cu124
  print(torch.version.cuda) # print 12.4
  ```
