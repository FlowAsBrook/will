---
title: nvidia cuda
date: 2025-03-12 15:59:26
tags: nvidia
categories: cuda
---

# install

## CUDA Toolkit

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

## driver

```shell
sudo apt-get install -y cuda-drivers
```