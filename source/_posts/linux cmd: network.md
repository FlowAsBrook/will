---
title: linux network command
date: 2017-05-9 10:01:30
tags: command
categories: linux
---

## port

- Check the listening ports
  
  Run any one of the following command:
  
  ```bash
  sudo lsof -i -P -n | grep LISTEN 
  sudo netstat -tulpn | grep LISTEN
  sudo nmap -sTU -O IP-address-Here
  ```

- checking **remote system** tcp 80 port status
  
  1. Telnet
  
  2. nc
     
     ```
      nc -zvw10 192.168.0.1 22
     其中参数：
     z: zero-I/O mode which is used for scanning
     v: for verbose output
     w10: timeout wait seconds
     ```
3. nmap
   
   ```bash
   telnet myserver.com 80 
   nc -v myserver.com 80
   nc -vn 192.168.40.146 2424
   nmap myserver.com 80 
   ```
- 通过进程名查看占用端口
  
  - 先查看进程pid    `ps -ef | grep 进程名 `
  
  - 再通过pid查看占用端口  
  
    ```
    netstat -nap | grep <pid>
    lsof -p <pid>
    ```
  
- 通过端口查看进程
  
  ```shell
  netstat -an | grep <portnumber>
  lsof -i:<portnumber>
  ```

## pid

- 根据进程号查看启动文件所在位置

  `readlink /proc/<线程ID>/exe`

- 查看进程的启动命令

  `cat /proc/<线程ID>/cmdline`

- 根据PID查看子进程

  `netstat -lntp |grep PID`

## proxy

- 查看本地网络代理
  
  ```
  export | grep -i proxy
  ```

- 关闭代理
  
  ```shell
  unset http_proxy 
  ```

## curl

- pretty json 

  curl 'address:port' | json_pp

## netstat

检测端口是否允许远程访问

```
netstat -an | grep <port>

查看输出结果。如果输出结果中有 0.0.0.0:<port> 或者 :::<port>，表示该端口允许远程访问；
如果输出结果中只有 127.0.0.1:<port> 或者 ::1:<port>，表示该端口只允许本地访问
```

- 在 Ubuntu 中，要开启指定端口的远程访问，需要进行以下步骤：

1. 确认端口是否已经开启：使用 `netstat` 命令检查要开启的端口是否已经在监听状态。如果已经在监听状态，则可以直接进行下一步操作；如果没有在监听状态，则需要先启动对应的服务程序。

2. 修改防火墙规则：使用 `ufw` 命令修改防火墙规则，开放指定端口。例如，要开放端口号为 80 的端口，可以使用以下命令：

   ```
   sudo ufw allow 80/tcp
   ```

   这个命令会将端口号为 80 的端口加入到防火墙规则中，并允许 TCP 协议通过该端口进行访问。

3. 重启防火墙服务：在修改防火墙规则后，需要重启防火墙服务才能使修改生效。可以使用以下命令重启防火墙服务：

   ```
   sudo service ufw restart
   ```

4. 确认防火墙规则是否正确：使用以下命令查看当前的防火墙规则：

   ```
   sudo ufw status numbered
   ```

## subnet mask

> 子网掩码

xx.xx.xx.xx/24

在网络中，IP地址由两个部分组成：网络地址和主机地址。IP地址的网络部分用于标识网络，而主机部分用于标识特定的设备或主机。

CIDR（无类别域间路由）表示法通常用于指定IP地址的子网掩码，以确定网络地址和主机地址的边界。CIDR表示法使用斜线后面的数字来表示网络地址中前多少位是固定的，即网络前缀长度。例如，/24 表示前 24 位是网络地址，剩下的 8 位则是主机地址。

对于 IPv4 地址，每个 IP 地址都由 32 位二进制数表示。通过将前 24 位作为网络地址并保留最后 8 位作为主机地址，可以创建一个包含 256 个可能主机地址的网络。这被称为 "24 位网络" 或 "24 位子网"，它的子网掩码是 255.255.255.0。

因此，当你配置一个 IP 地址并添加 /24 后缀时，你正在指定该地址所属的网络和子网掩码。这样可以帮助确定该地址所在的网络，并确保与其他设备进行通信时，数据包能够正确地路由到目标设备。

# route

```shell
# check existed ip route
ip route

# delete specify ip route
sudo ip route del 172.21.0.0/16

# delete specify ip link
sudo ip link delete br-900457b8a29e
```

# DNS

## add new dns

- method 1:

  ```yaml
  # step1: vim /etc/systemd/resolved.conf
  [Resolve]
  DNS=10.201.44.51 223.5.5.5  # new DNS
  FallbackDNS=127.0.0.53
  
  # step2: restart service
  sudo systemctl restart systemd-resolved
  ```

​	

- method2: (有时候不起效)

  ```yaml
  vim /etc/resolv.conf
  # 注意：添加的dns解析地址，需要放在前面，会有顺序影响
  
  nameserver 10.201.44.51  # new DNS
  nameserver 223.5.5.5	# new DNS
  nameserver 127.0.0.53
  ```

# network

## create new network

step1: vim `/etc/netplan/ib.yaml`

```shell
network:
  version: 2
  renderer: networkd
  ethernets:
    ibp27s0:
      dhcp4: no
      addresses:
        - 192.168.2.52/21
```

step2: `netplan apply`
