---
title: macos
date: 2021-12-20 11:25:00
tags: snippet
categories: macos
---

# shortcuts

```
⌃⌘+F   最大化窗口
⌘+M    最小化窗口
⌘+H    隐藏程序的所有窗口
⌘+W    关闭窗口
⌘+Q    关闭程序
shift + ⌘ + 5    截屏
Home键   Fn+左方向
End键    Fn+右方向
Control+Space  输入法切换
command + shift + . 显示/隐藏文件或目录
Fn-上箭头：Page Up：向上滚动一页
Fn-下箭头：Page Down：向下滚动一页
Fn-左箭头：Home：滚动到文稿开头
Fn-右箭头：End：滚动到文稿末尾
```



# command

## brew

- Disable  auto update when install 

  ```shell
  temp method 1:
  HOMEBREW_NO_AUTO_UPDATE=1 brew install <formula>
  
  temp method 2:
  export HOMEBREW_NO_AUTO_UPDATE=1
  
  permanently method:
  $ vim ~/.bashrc
  export HOMEBREW_NO_AUTO_UPDATE=1
  source ~/.bashrc
  or
  $ vim ~/.zshrc
  export HOMEBREW_NO_AUTO_UPDATE=1
  source ~/.zshrc
  ```

- upgrade app

  ```shell
  HOMEBREW_NO_AUTO_UPDATE=1 brew upgrade <app>  # disable update
  ```

  

## PORT

- 查看指定进程pid的占用端口

  ```shell
  lsof -i -P -n | grep LISTEN | grep {pid}
  ```


## monitor

```
sudo asitop --interval 10
```

## memory

top -l 1 | head -n 10

## finder

快速定位目录 Shift + Command + G

显示隐藏文件 Shift + Command + .

## service

The Mac equivalent to `systemctl` or `service` is `launchctl`.

```bash
# 查看所有的 plist 服务
launchctl list

# 禁用服务
launchctl disable /Library/LaunchDaemons/com.simonkuang.macos.coredns.plist

# 启用服务
launchctl disable /Library/LaunchDaemons/com.simonkuang.macos.coredns.plist

# 杀死进程（不优雅地杀，直接杀进程）并重启服务。对一些停止响应的服务有效。
launchctl kickstart -k /Library/LaunchDaemons/com.simonkuang.macos.coredns.plist

# 在不修改 Disabled 配置的前提下启动服务
launchctl start /Library/LaunchDaemons/com.simonkuang.macos.coredns.plist

# 在不修改 Disabled 配置的前提下停止服务
launchctl stop /Library/LaunchDaemons/com.simonkuang.macos.coredns.plist
```

## extract

- zip

```bash
ditto -V -x -k --sequesterRsrc filename.zip destination
```

## File

- 解压带有中文名称的zip包

```shell
ditto -V -x -k --sequesterRsrc filename.zip destination
```

- 查看目录下文件夹大小

  ```shell
  du -d 1 -h    命令查看当前目录下所有文件夹的大小 -d 指深度，后面加一个数值
  或
  du -hd1
  ```

## Install pkg

a.

```bash
sudo installer -pkg /path/to/package.pkg -target /
# will install the package in /Applications.
```

Here `/` is the mount point of `Macintosh HD` volume. `-target` accepts path like `"/Volumes/Macintosh HD"`, or `/dev/disk0` also.

b.

```bash
installer -pkg myapp.pkg -target CurrentUserHomeDirectory
#will install the package in ~/Applications.
```

## base64

**Using base64 to decode some text**

```
base64 --decode
VGhpcyBpcyBiYXNlNjQgZW5jb2RlZAo=
[ctrl+d]
```

# soft

## wechat

- 在系统语言设置english的情况下，设置微信的language

  ```shell
  defaults write com.tencent.xinWeChat AppleLanguages '("zh-CN")'
  ```

## mysql

- https://stackoverflow.com/questions/10757169/location-of-my-cnf-file-on-macos)

未通过brew安装，安装路径/usr/local/mysql

mysql	login：1qaz2wsx

- 暂未配置my.cnf文件

  ```bash
  mysql --help | grep my.cnf
  order of preference, my.cnf, $MYSQL_TCP_PORT,
  /etc/my.cnf /etc/mysql/my.cnf /usr/local/mysql/etc/my.cnf ~/.my.cnf
  ```

- [Location of my.cnf file on macOS](https://stackoverflow.com/questions/10757169/location-of-my-cnf-file-on-macos)

## dbeaver

- script脚本位置

  ```shell
  /Users/dongwei/Library/DBeaverData/workspace6/dingoDB/Scripts
  ```

  

## age

加密工具，通过brew安装

## docker

环境配置 `ln -s /Applications/Docker.app/Contents/Resources/bin/docker /usr/local/bin/docker`

启动服务

```
open -a Docker
```

## kafka

- 安装目录 `/Users/dongwei/Applications/confluent-7.0.1`
- 启动命令 先启动zookeeper，然后启动kafka

## wmv（microsoft音频格式）

- 使用播放软件elmedia player

## shadowsocksR-NG

会把自己的log文件：**ss-local.log**

保存在路径：~/Library/Logs/ss-local.log

https://www.twisted-meadows.com/shadowsocksx-ng

## clashx

解析地址出错时

```
您的连接不是私密连接
攻击者可能会试图从 [www.google.com](<http://www.google.com/>) 窃取您的信息（例如：密码、通讯内容或信用卡信息）
```

更换节点即可

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/11c3cef1-d99c-462a-b3c0-28a43b4b7f0a/Untitled.png)

## vscode

- 多行编辑  `option+shift+选中`

## intellij idea

- 去除无用的import引用`control + option + o`
- 代码格式化  `command + option + l`

## redis

使用brew安装

```
brew services start/stop/info redis
```

## elastic

- 启动

  ./bin/elasticsearch

## Translate

- homebrew安装的translate-shell

  https://www.soimort.org/translate-shell/

  暂时还有问题

  安装gawk时提示（https://macappstore.org/gawk/）

  gawk: no bottle available!

## karabiner



## mc

```shell
# binary file
curl https://dl.min.io/client/mc/release/darwin-arm64/mc -o $HOME/Applications/minio-binaries/mc

# directory
$HOME/Applications/minio-binaries

# command link
sudo ln -s $HOME/Applications/minio-binaries/mc /usr/local/bin/mc
```



## 破解软件安装

- https://xmac.app/

  - 下载完成之后，安装dmg文件之后，需要执行Help.txt里面的文字

    ```prolog
    Option I
    For a certain application run in Terminal:(我选择这种方式)
    sudo xattr -rd com.apple.quarantine /Applications/LockedApp.app
    
    Option II
    To disable checks globally run in Terminal:
    sudo spctl --master-disable
    ```

## 搜狗输入法

- 简繁体切换
  - `ctrol + shift + F`

## ja-netfilter

> jetbrains activate tool

```shell
Appearance & Behavior -> System Settings -> Language and Region -> Region
需要选择 Asia(except china mainland)
激活脚本依然使用原来的 script/install.sh
```

### PlantUML

```shell
brew install plantuml
```

### mermaid

```shell
npm install -g @mermaid-js/mermaid-cli
```

# gpu

```shell
sudo asitop --interval 10
```

