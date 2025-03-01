---
title: linux tune
date: 2025-01-21 10:43:04
tags: tune
categories: linux
---

# cpu

## governor strategy

- Check Available CPU Governors

  ```shell
  # Run the following command to check available CPU governors:
  cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_available_governors
  
  # You should see something like below info:
  # conservative ondemand userspace powersave performance schedutil
  ```

- Check Current CPU Governor

  ```shell
  cat /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
  or
  cpupower frequency-info --policy # should install cpupower command by 'sudo dnf install kernel-tools -y'
  ```

- Set CPU Governor to Performance temporarily

  ```shell
  # To temporarily set the CPU governor to performance (until reboot):
  for cpu in /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor; do
    echo performance | sudo tee $cpu
  done
  ```

- Set CPU Governor Persistent

  ```shell
  # First, install cpupower:
  sudo dnf install kernel-tools -y
  
  # Then, set the governor:
  sudo cpupower frequency-set -g performance
  
  # To apply this setting at boot, enable the service:
  sudo systemctl enable --now cpupower.service
  ```

# nvme

## I/O scheduler

- check current scheduler pattern

  ```shell
  cat /sys/block/nvme[01]\*/queue/scheduler
  
  # console print below info
  none [mq-deadline] kyber bfq
  ```
  The **scheduler currently in use** is indicated by the square brackets [ ].

  **Common NVMe I/O Schedulers**

  1. **none** – Default for NVMe, provides minimal latency.

  2. **mq-deadline** – Multi-queue deadline scheduler, balances fairness and performance.

  3. **kyber** – Optimized for high-performance SSDs.

  4. **bfq** – Budget Fair Queueing, useful for low-latency workloads.

- Change the I/O Scheduler

  ```shell
  # To set the none scheduler for nvme0n1, use:
  echo none | sudo tee /sys/block/nvme0n1/queue/scheduler
  ```

  **When to Change the Scheduler?**

  - Use none **(default)** if latency is the priority (most NVMe drives handle queuing internally).

  - Use mq-deadline if fairness in I/O operations is needed.

  - Use kyber for workloads requiring fast response time.

  - Use bfq for interactive desktop workloads.

# ulimit

## open file size

> To configure the **maximum open file size** (ulimit) for a specific user 

- Check Current Limits

  ```shell
  # To see the current limits for a user:
  ulimit -n # Check open file limit
  ulimit -a # Check all limits
  
  # To check system-wide limits:
  cat /proc/sys/fs/file-max
  ```

- Set Open File Limits for a Specific User

  > The changes in limits.conf are only applied to new login sessions
  
  ```shell
  sudo vim /etc/security/limits.conf
  
  # Add the following lines (replace username with the actual user):
  username soft nofile 65535
  username hard nofile 65535
  ```

​	**soft**: The default limit the user gets.

​	**hard**: The maximum limit a user can set.

| Feature          | /etc/security/limits.conf  | /etc/security/limits.d/*.conf                                |
| ---------------- | -------------------------- | ------------------------------------------------------------ |
| **Default File** | Yes                        | No (Custom files in limits.d directory)                      |
| **Priority**     | Read first                 | Read after limits.conf                                       |
| **Organization** | All rules in one file      | Modular approach, one file per service or user               |
| **Example**      | `* soft nofile 65536`      | `dongw soft nofile 65536` (in `/etc/security/limits.d/dongw.conf`) |
| **Syntax**       | Same syntax for both files | Same syntax as limits.conf                                   |

# HP and THP

> Huge Pages (HP) and Transparent Huge Pages (THP)

Understanding Huge Pages (HP) and Transparent Huge Pages (THP)

**Huge Pages (HP)**: A manually configured fixed-size memory allocation system, beneficial for workloads that require large contiguous memory allocations.

**Transparent Huge Pages (THP)**: An automated memory management feature that dynamically allocates large memory pages based on usage patterns.

for performance-sensitive applications, THP can sometimes cause performance issues due to fragmentation and unexpected latency spikes. Thus, it’s often recommended to **disable THP** and manually configure HP.

- Checking Current Huge Pages Configuration

  ```shell
  cat /proc/meminfo | grep HugePages
  
  # Example output:
  AnonHugePages:  16850944 kB # Memory allocated via THP.
  ShmemHugePages:        0 kB
  FileHugePages:         0 kB
  HugePages_Total:       0 # Number of configured huge pages.
  HugePages_Free:        0 # Available huge pages.
  HugePages_Rsvd:        0
  HugePages_Surp:        0
  ```

- Configuring Static Huge Pages

  **Step 1: Set the Number of Huge Pages**

  ```shell
  # To allocate a specific number of 2MB huge pages, calculate based on your memory requirements. Example:
  echo 1024 | sudo tee /proc/sys/vm/nr_hugepages
  
  # Make it persistent:
  echo "vm.nr_hugepages=1024" | sudo tee -a /etc/sysctl.conf
  sysctl -p
  ```

  **Step 2: Allocate Huge Pages at Boot (Recommended)**

  ```shell
  sudo vim /etc/default/grub
  
  # Add hugepages kernel parameter:
  GRUB_CMDLINE_LINUX_DEFAULT="default_hugepagesz=2M hugepagesz=2M hugepages=1024"
  
  # Update GRUB:
  sudo grub2-mkconfig -o /boot/grub2/grub.cfg
  
  # Reboot the system:
  sudo reboot
  ```

  **Step 3: Mount Huge Pages File System (Optional)**

  ```shell
  # To enable shared access to huge pages:
  mkdir -p /mnt/huge
  mount -t hugetlbfs nodev /mnt/huge
  # To make this persistent, add to /etc/fstab:
  nodev /mnt/huge hugetlbfs defaults 0 0
  ```

- Disabling Transparent Huge Pages (THP)

  THP should be **disabled** for performance-sensitive applications

  **Step 1: Disable THP at Runtime**

  ```shell
  echo never | sudo tee /sys/kernel/mm/transparent_hugepage/enabled
  echo never | sudo tee /sys/kernel/mm/transparent_hugepage/defrag
  ```

  **Step 2: Make THP Disabled at Boot**

  ```shell
  sudo vim /etc/default/grub
  
  # Add:
  GRUB_CMDLINE_LINUX_DEFAULT="transparent_hugepage=never"
  
  # Update GRUB:
  sudo grub2-mkconfig -o /boot/grub2/grub.cfg
  
  # Reboot:
  sudo reboot
  ```

- Verification After Reboot

  ```shell
  # Check Huge Pages Allocation
  cat /proc/meminfo | grep HugePages
  
  # Check THP Status
  cat /sys/kernel/mm/transparent_hugepage/enabled
  # Expected output:
  [always] madvise never
  ```

Summary: Should You Configure HP & THP Together?

For performance-sensitive applications, it’s recommended to **disable THP** and **manually configure Huge Pages (HP)** for better performance.

THP can cause latency spikes and memory fragmentation, making it less ideal for high-performance file systems.

