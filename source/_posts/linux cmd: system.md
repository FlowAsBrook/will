---
title: linux system command
date: 2016-10-16 12:01:30
tags: command
categories: linux
---

# bash

[Bash Scripting Tutorial for Beginners](https://linuxconfig.org/bash-scripting-tutorial-for-beginners)

### Bash Shell Scripting Definition

- Bash:Bourne-Again SHell
  Bash is a command language interpreter. 
  
  ## conclusion
  
  Do not be afraid to break things as that is perfectly normal. Troubleshooting and fixing code is perhaps the best booster for you to enhance your understanding of bash scripting and to improve your ability.

[Bash scripting Tutorial](https://linuxconfig.org/bash-scripting-tutorial#h24-stdout-to-screen)

### session

- kill seesion `screen -X -S [session # you want to kill] quit`
- 新建screen会话           screen -S xxx
- 恢复指定会话               screen -r xxx
- 查看所有会话                screen -ls
- 删除指定会话                screen -S xxx -X quit
- 回到终端                        Ctrl-a d

### firewall

- check status : `sudo ufw status`

- enable firewall: 
  
  ```bash
  $ sudo ufw enable
  Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
  Firewall is active and enabled on system startup
  ```

- disable firewall
  
  ```bash
  $ sudo ufw disable
  Firewall stopped and disabled on system startup
  ```

### 磁盘相关

- 查看系统磁盘占用情况    ` df -h`

- 查看目录下文件大小        `du -sh`

- 查看当前目录下一级子文件和子目录占用的磁盘容量: `du -h --max-depth=1 `
  
  ```
  查看当前目录下user目录的大小，并不想看其他目录以及其子目录：
  du -sh user
  -s表示总结的意思，即只列出一个总结的值
  du -h --max-depth=0 user
  --max-depth=n表示只深入到第n层目录，此处设置为0，即表示不深入到子目录。
  ```

- 总结du常用命令
  
  **du -h --max-depth=1 |grep 'G' |sort   #查看上G目录并排序**
  
  du -h --max-depth=1 |grep [TG] |sort   #查找上G和T的目录并排序
  du -sh --max-depth=1  #查看当前目录下所有一级子目录文件夹大小

- 清理指定目录下的文件
  
  例如需要根据时间删除这个目录下的文件，/tmp，清理掉20天之前的无效数据。可以使用下面一条命令去完成：
  
  ```shell
  find /tmp -mtime +21 -name "*" -exec rm -Rf {} \;
  - /tmp ：准备要进行清理的任意目录
  - -mtime：标准语句写法
  - ＋10：查找10天前的文件，这里用数字代表天数，＋30表示查找30天前的文件
  - "*"：希望查找的数据类型，".jpg"表示查找扩展名为jpg的所有文件，""表示查找所有文件
  - -exec：固定写法
  - rm -Rf：强制删除文件，包括目录(注意R大写，f小写)
  -  {} \; 固定写法，一对大括号+空格+\+; 
  ```

### history

查看历史命令，支持 grep过滤操作

### swap

```shell
# temp
sudo swapoff -a
# persistent
sudo sed -i '/[[:space:]]swap[[:space:]]/s/^/#/' /etc/fstab
```

# Memory

## OOM

### 积分机制

> OOM（Out-Of-Memory）积分机制是 Linux 内核在系统内存耗尽时，通过评估进程的优先级和内存使用情况，选择一个或多个进程终止以释放内存的一种机制。其核心目标是在内存不足时，最小化系统崩溃风险并优先保留关键进程。

**OOM 积分机制的工作原理**

1. **`oom_score` 计算**
   每个进程的 `oom_score`（位于 `/proc/<pid>/oom_score`）决定了它被 OOM Killer 选中的概率。分数越高，进程越可能被终止。计算依据包括：
   - **内存使用量**：进程占用的物理内存和交换分区（Swap）的总和。
   - **运行时间**：长时间运行的进程可能被适当保护（通过 `oom_score_adj` 调整）。
   - **进程优先级**：低优先级（高 `nice` 值）的进程更易被终止。
   - **子进程内存**：父进程的子进程内存可能计入父进程的评分。
   - **用户权限**：特权进程（如 root 用户进程）可能被保护。
2. **`oom_score_adj` 调整因子**
   用户可通过 `/proc/<pid>/oom_score_adj` 文件（范围：`-1000` 到 `1000`）手动干预评分：
   - **负值**：降低被终止概率（如 `-1000` 表示免疫 OOM Killer）。
   - **正值**：增加被终止概率。

**调整 OOM 参数的常见方法**

- **保护关键进程**：
  通过修改 `oom_score_adj` 降低其被终止的概率：

  ```shell
  echo -1000 > /proc/<pid>/oom_score_adj  # 使进程免疫 OOM Killer
  ```

- **标记进程为高优先级目标**：

  ```shell
  echo 500 > /proc/<pid>/oom_score_adj     # 增大终止概率
  ```

# operate

### hostname

```shell
sudo hostnamectl set-hostname <new-hostname>
sudo hostnamectl status
```

### user

```shell
# sudo权限用户切换成root
sudo -i
sudo su -

# add user
sudo useradd -m -s /bin/bash <username>

dingofs ALL=(ALL) NOPASSWD:ALL
#	-m: Creates a home directory for the user.
#	-s /bin/bash: Sets the default shell to /bin/bash.

# config password
passwd <username>

# add user to sudo group
# opitional-1 ubuntu/debian
usermod -aG sudo <username>

# opitional-2 edit visudo
visudo
<username> ALL=(ALL) NOPASSWD:ALL

# delete user
userdel <username>


# set uid:65535 to nobody base on gid: nobody 65534 
sudo useradd -u 65535 -g 65534 -d /nonexistent -s /usr/sbin/nologin nobody65535
```

### ssh

```shell
# step1: generate ssh-key in all nodes
ssh-keygen -t rsa
# step2: in first node
cat id_rsa.pub >> authorized_keys
# step3: in other node
ssh-copy-id -i id_rsa.pub 用户@<first_node>
# step4: in first node
scp authorized_keys 用户@<other_node>:~/.ssh/
```

### process

```shell
# check specify process limit info
cat /proc/<pid>/limits

# Check total-vm / anon-rss
cat /proc/<pid>/status
```

### 卸载安装的软件

```shell
浏览已安装的程序    dpkg --list
卸载程序和所有配置文件    sudo apt-get --purge remove <programname>
只卸载程序    sudo apt-get remove <programname>
```

### 安装deb文件出错时

使用`apt-get -f -y install`修复之后，再进行安装

```
1.执行命令sudo dpkg　-i　XXX.deb　返回依赖关系错误提示
2.执行sudo apt-get -f install　这条命令将自动安装需要的依赖包．
3.再次执行命令sudo dpkg　-i　XXX.deb　安装成功
```

### centos安装离线依赖

```shell
rpm -ivh name.rpm
```

### 光标

```
Ctrl+a：光标回到命令行首。 （a：ahead）
Ctrl+e：光标回到命令行尾。 （e：end）
Ctrl+b：光标向行首移动一个字符。 （b：backwards）
Ctrl+f：光标向行尾移动一个字符。 （f：forwards）
Ctrl+w: 删除光标处到行首的字符。
Ctrl+k：删除光标处到行尾的字符。
Ctrl+u：删除整个命令行文本字符。
Ctrl+h：向行首删除一个字符。
Ctrl+d：向行尾删除一个字符。

Ctrl + xx ：在命令行尾和光标之间移动
```

### 时区

[CentOS 7 时区设置](https://www.cnblogs.com/zhangeamon/p/5500744.html)

- UTC（Coordinated Universal Time ）是国际时间标准，通常与GMT相同，但在某些情况下，UTC可能会有闰秒的调整。

  - UTC is the global standard for timekeeping.

  - It does not observe Daylight Saving Time (DST).
  - Used as a reference for time zones worldwide.

  - Offset: UTC is the baseline, so its offset is **0 hours**.

- GMT（Greenwich Mean Time）是格林尼治时间，没有夏令时调整。
  - GMT was historically the global time standard but has largely been replaced by UTC.
- UTC+N or GMT+N
  - In most practical cases, **UTC+N** and **GMT+N** are equivalent.
  - Refers to a time zone that is **N hours ahead of UTC (Coordinated Universal Time)**.
  - **UTC+N** is the modern term and is more precise in technical or scientific contexts (e.g., aviation, computing, telecommunications).
  - **GMT+N** is an older term that is still widely recognized but less commonly used in formal applications.

### 日期

- 判断　day of year     
  
  `doy=$(date +%j)`

- 制定日期减一天
  
  `date -d"20140101 -1 days" +"%Y%m%d"`

- 当前时间戳（秒）

  ```shell
  date +%s
  ```


### 剪切板

将剪切板中的内容输出到文件     echo $(xsel --clipboard) >> a.txt 

将文件的内容复制到剪切板         cat a.txt | xsel --clipboard

### securtCRT

```
下载服务器文件  sz filename
上传本地文件   rz filename
```

### top

- “1”
  
  查看所有CPU核的使用情况

- “c”
  
  查看具体进程的路径

```
   l- 开启或关闭第一部分第一行top信息显示

　　t - 开启或关闭第一部分第二行Tasks和第三行 Cpu(s) 信息显示

　　m - 开启或关闭第一部分第四行 Mem 和 第五行 Swap 信息显示

　　N - 以 PID 的大小的顺序排列表示进程列表

　　P - 以 CPU 占用率大小的顺序排列进程列表

　　M - 以内存占用率大小的顺序排列进程列表

　　h - 显示帮助

　　n - 设置在进程列表所显示进程的数量（按完n，再输入个数）

　　q - 退出 top

　　s - 设置显示信息的刷新频率（由于是命令行模式，显示的过程其实是刷屏的过程）
```

- 进程CPU占用率

  top显示某个进程占用cpu达到100%，则表明该进程正在使用所有可用的 CPU 资源。这通常是因为该进程执行的任务非常耗费 CPU 资源，或者该进程存在某些问题导致 CPU 使用率异常高。

  在 Linux 系统中，每个进程都只能在单个 CPU 核心上运行。但是，系统可以通过调度程序（scheduler）在多个 CPU 核心之间轮换运行进程，从而达到让多个进程同时执行的效果。

  ```
  ### cpu
  us：用户态使用的cpu时间比
  sy：系统态使用的cpu时间比
  ni：用做nice加权的进程分配的用户态cpu时间比
  id：空闲的cpu时间比
  wa：cpu等待磁盘写入完成时间
  hi：硬中断消耗时间
  si：软中断消耗时间
  st：虚拟机偷取时间
  ```

  

### 查看指定服务的运行情况

- `journalctl -u xxx.service`
- `journalctl -xe` 查看最近系统服务日志

### 资源占用

```shell
ps -aux | grep 服务名称或pid
# 显示
root  19496  0.0  2.4 4826152 1603360 ?     Sl    2020 503:15 java -jar -Xms1024m -Xmx1024m jenkins.war --httpPort=55555

19496 为PID
0.0 为CPU占用百分比（注意：如果有两个CPU，32核64线程，那么总占比是6400%，占用一线程，cpu占比是100%）
2.4 为内存占用百分比
```

# system

## kernel dumps

```shell
# Changing Kernel Crash Dump Directory (for kdump)
sudo vi /etc/kdump.conf

# Look for the line starting with path and change it to your desired directory
# default: /var/crash
path /new/crash/directory

# Restart the kdump service:
sudo systemctl restart kdump
```

## core dumps

```shell
# 方式一：设置 core 文件的存放路径
echo "/core/core.%e.%p" > /proc/sys/kernel/core_pattern

#	%e is replaced by the executable name.
#	%p is replaced by the process ID.

# 方式二：
sudo vim /etc/sysctl.conf
kernel.core_pattern=/core/core-%e-%p-%t

# Apply the changes
sudo sysctl -p
```

| Feature         | Kernel Dumps                      | Core Dumps                                                |
| --------------- | --------------------------------- | --------------------------------------------------------- |
| Source          | Kernel crashes (kernel panic)     | User-space process crashes                                |
| Trigger         | Kernel panic                      | Process signals (e.g., SIGSEGV)                           |
| Contents        | Entire (or partial) system memory | Process memory                                            |
| Tools           | kdump, crash                      | gdb, apport, ABRT                                         |
| Debugging Scope | Kernel-level debugging            | User-level application debugging                          |
| Location        | Typically /var/crash              | Specified by core_pattern (/proc/sys/kernel/core_pattern) |

## time

- unning duration or uptime

  ```shell
  # option 1
  uptime
  
  # option 2
  who -b
  ```


## process

- Tree view all process

  ```shell
  pstree -g
  ```


## 查看系统配置

- 查看系统

  - `cat /etc/os-release`

- 查看内核

  - `cat /proc/version`
  - `uname -a`

- 查看linux版本

  - `lsb_release -a`
  - `cat /etc/issue`

- > 总核数 = 物理CPU个数 X 每颗物理CPU的核数 
  >
  > 总逻辑CPU数 = 物理CPU个数 X 每颗物理CPU的核数 X 超线程数

  - 查看物理CPU个数

    1. top命令，然后输入数字1查看，各项参数如下
       - `us`：用户空间占用 CPU 的百分比。
       - `sy`：内核空间占用 CPU 的百分比。
       - `ni`：调整过优先级的进程占用 CPU 的百分比。
       - `id`：空闲 CPU 的百分比。
       - `wa`：等待 I/O 的 CPU 时间的百分比。
       - `hi`：硬中断（hardware interrupt）占用 CPU 的时间的百分比。
       - `si`：软中断（software interrupt）占用 CPU 的时间的百分比。
       - `st`：虚拟机或者运行在它上面的虚拟 CPU 占用 CPU 的时间的百分比。
    2. 输入mpstat查看
    3. 输入以下命令

    ```shell
    cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l
    ```

  - 查看每个物理CPU中core的个数(即核数)

    ```shell
    cat /proc/cpuinfo| grep "cpu cores"| uniq
    ```

  - 查看逻辑CPU的总数

    ```shell
    cat /proc/cpuinfo| grep "processor"| wc -l
    ```

## 清理内存

```shell
sync; echo 1 > /proc/sys/vm/drop_caches
```

## 查看buff/cache

- 工具推荐 https://github.com/silenceshell/hcache

# troubleshooting

## 鼠标按键会在终端输入乱码

```shell
# the `reset` command helps restore the terminal to a known good state, which can be helpful in troubleshooting issues or clearing screen clutter.
reset
```

## received disconnect from 10.220.32.16 port 22:2: Too many authentication failures

```shell
# Remove all keys from the agent
ssh-add -D
```

## /etc/hosts 文件无法编辑

```shell
# the file may have the immutable attribute set. Check with:
lsattr /etc/hosts

#  remove i flag
sudo chattr -i /etc/hosts
```

