---
title: linux command
date: 2015-10-16 12:01:30
tags: command
categories: linux

---

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

### 光标

```
Ctrl+a：光标回到命令行首。 （a：ahead）
Ctrl+e：光标回到命令行尾。 （e：end）
Ctrl+b：光标向行首移动一个字符。 （b：backwards）
Ctrl+ f：光标向行尾移动一个字符。 （f：forwards）
Ctrl+w: 删除光标处到行首的字符。
Ctrl+k：删除光标处到行尾的字符。
Ctrl+u：删除整个命令行文本字符。
Ctrl+h：向行首删除一个字符。
Ctrl+d：向行尾删除一个字符。

Ctrl + xx ：在命令行尾和光标之间移动
```

### 时区

[CentOS 7 时区设置](https://www.cnblogs.com/zhangeamon/p/5500744.html)

### grep

限定查询结果之后的前几行 `grep -m 10 <pattern> <file> `

限定查询结果倒数的几行 `grep <pattern> <file> | tail -10`

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
下载服务器文件    sz filename
上传本地文件 rz filename
```

### tr命令

tr -- translate or delete characters 

- 大小写转换
  
  ```shell
  cat file | tr A-Z a-z 
  cat file | tr a-z A-Z
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

### 查看指定服务的运行情况

- `journalctl -u xxx.service`



### 资源占用

```shell
ps -aux | grep 服务名称或pid
# 显示
root  19496  0.0  2.4 4826152 1603360 ?     Sl    2020 503:15 java -jar -Xms1024m -Xmx1024m jenkins.war --httpPort=55555

19496 为PID
0.0 为CPU占用百分比（注意：如果有两个CPU，32核64线程，那么总占比是6400%，占用一线程，cpu占比是100%）
2.4 为内存占用百分比

```



## 清理缓存

```shell
sync; echo 1 > /proc/sys/vm/drop_caches
```



## centos安装离线依赖

```shell
rpm -ivh name.rpm
```

