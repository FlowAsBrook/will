---
title: linux snippet
date: 2017-05-9 10:01:30
tags: snippet
categories: linux
---

# overview

- /bin/    用以存储二进制可执行命令文件，/usr/bin/也存储了一些基于用户的命令文件。
- /sbin/    许多系统命令的存储位置，/usr/sbin/中也包括了许多命令。
- /root/    超级用户，即根用户的主目录。
- /home/    普通用户的默认目录，在该目录下，每个用户拥有一个以用户名命名的文件夹。
- /boot/    存放Ubuntu内核和系统启动文件。
- /mnt/     通常包括系统引导后被挂载的文件系统的挂载点。
- /dev/    存储设备文件，包括计算机的所有外部设备，如硬盘、是、键盘、鼠标等。
- /etc/    存放文件管理配置文件和目录。
- /lib/    存储各种程序所需要的共享库文件。
- /lost+found/    一般为空，当非法关机时，会存放一些零散的文件。
- /var/    用于存放很多不断变化的文件，例如日志文件等。
- /usr/    包括与系统用户直接有关的文件和目录
- /media/    存放Ubuntu系统自动挂载的设备文件。
- /proc/    这是一个虚拟目录，它是内存的映射，包括系统信息和进程信息。
- /tmp/    存储系统和用户的临时信息。
- /initrd/    用来加载启动时临时挂载的initrd.img映像文件，以及载入所要的设备模块目录。
- /opt/    作为可选文件和程序的存放目录，否则将无法引导计算机进入操作系统。
- /srv/    存储系统提供的服务数据。
- /sys/    系统设备和文件层次结构，并向用户程序提供详细的内核数据信息。

# point

## native lib config

- `/lib` contain "essential" libraries that may be needed even in single-user (admin-only) mode and without /usr mounted
- `/usr/lib` are the libraries for the normal user-programs, that mostly can be found under /usr.
- `/usr/local/lib` are the libraries for locally installed programs and packages ie. things you've compiled and installed from source-packages yourself.

## shell and export

用户登录到Linux系统后，系统将启动一个用户shell。在这个shell中，可以使用shell命令或声明变量，也可以创建并运行 shell脚本程序。运行shell脚本程序时，系统将创建一个子shell。此时，系统中将有两个shell，一个是登录时系统启动的shell，另一 个是系统为运行脚本程序创建的shell。当一个脚本程序运行完毕，它的脚本shell将终止，可以返回到执行该脚本之前的shell。从这种意义上来 说，用户可以有许多 shell，每个shell都是由某个shell（称为父shell）派生的。

在子 shell中定义的变量只在该子shell内有效。如果在一个shell脚本程序中定义了一个变量，当该脚本程序运行时，这个定义的变量只是该脚本程序内 的一个局部变量，其他的shell不能引用它，要使某个变量的值可以在其他shell中被改变，可以使用export命令对已定义的变量进行输出。 export命令将使系统在创建每一个新的shell时定义这个变量的一个拷贝。这个过程称之为变量输出。

- 操作文件名中含有“-”的文件

  使用转移字符 “--”， 比如查看文件“-abc.txt“，命令为 `vim -- -abc.txt`

  

- `/etc/environment`是设置整个系统的环境，而`/etc/profile`是设置所有用户的环境 

## KVM

KVM 的全称是：Kernel-based Virtual Machine，简单一句话概括，就是一个基于 Linux 内核的虚拟化管理系统。

## java config

- use `java -XshowSettings:properties` to show the java.library.path (and others) value.

# practice

## ssh

1. 进入目录` /root/.ssh`

2. 执行命令：`ssh-keygen -t rsa`，会生成文件id_rsa和id_rsa.pub

3. 将公钥写入文件 authorized_keys，`cat id_rsa.pub >> authorized_keys`

4. 在其它机器重复上述操作

5. 将其它机器的公钥拷贝到第一台机器的authorized_keys文件中

   `ssh-copy-id -i id_rsa.pub 用户@ip`

   > 注意，一定不要使用复制拷贝的方式把公钥复制到authorized_keys文件中，因为复制的时候会有特殊字符的加入，可以在vim中使用`:set list`进行验证

6. 将第一台机器的authorized_keys复制到其它机器上

   `scp authorized_keys 用户@ip:/路径/.ssh/`

   注意点

- authorized_keys 的权限是600，即读和写（如果默认不是的话，需要修改）

## debug

```shell
sh -x <shellName>
```

# troubleshoot

## vmware centos ip config

1. ` vi /etc/sysconfig/network-scripts/ifcfg-ens33`,设置`ONBOOT=yes`
2. `service network restart `
3. `ip a`查看ip

## 界面卡死

1. 使用 Ctrl+Alt+F1或F2或到F6，进入tty终端（输入用户名和密码）

2. 执行命令注销桌面重新登录

   ```shell
   sudo pkill Xorg
   或
   sudo restart lightdm
   ```

## 装机问题

- Wifi Not Enabled Acer Aspire 4750

```shell
~$ rfkill list all
0: phy0: Wireless LAN
Soft blocked: no
Hard blocked: yes

~$ sudo rmmod acer-wmi
​```

add below line at the end of the file : /etc/modprobe.d/blacklist.conf
blacklist acer-wmi

Then restart the system.
~$ rfkill list all
0: phy0: Wireless LAN
Soft blocked: no
Hard blocked: no
```



## 机器掉线

### 网卡掉线

```shell
# check
ip a
cat /etc/netplan/xxx.yaml

# resolve
vim /etc/netplan/xxx.yaml
netplan --debug apply
```

