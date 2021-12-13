---
title: docker container
date: 2021-12-13 18:07:46
tags: snippet
categories: docker
---

### mysql

- 密码123456

- 创建容器

  ```
  docker run --name mysql-server -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7
  注意：
  -d:让容器在后台运行
  -P(大写):是容器内部端口随机映射到主机的高端口
  -p(小写):是容器内部端口绑定到指定的主机端口
  ```

- 进入容器

  ```shell
  docker exec -it mysql-server /bin/bash
  ```

- 访问

  `docker exec -it mysql-server mysql -uroot -p`

- 修改root 可以通过任何客户端连接

  ```shell
   ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';
  ```

- 从外部访问docker mysql-server

  ```shell
  mysql -h127.0.0.1 -P3306 -uroot -p
  ```

- 导入sql文件

  ```
  先将文件导入到容器
  #docker cp **.sql 容器名:/root/
  进入容器
  #docker exec -ti 容器名或ID sh
  登录数据库
  # mysql -uroot -p 
  将文件导入数据库
  source 数据库名 < /root/***.sql
  ```

- 导出数据库

  ```shell
  docker exec -it  mysql-server（容器名） mysqldump -uroot -p123456 数据库名称 > /opt/sql_bak/test_db.sql（导出表格路径）
  ```



### portainer

- 密码重置

  - 下载帮助镜像portainer/helper-reset-password

    ```shell
    docker pull portainer/helper-reset-password
    ```

  - 停止运行的portainer

    ```shell
    docker stop "id-portainer-container"
    ```

  - 运行重置命令

    ```shell
    docker run --rm -v portainer_data:/data portainer/helper-reset-password
    ```

  - 结果

    ```verilog
    2020/06/04 00:13:58 Password successfully updated for user: admin
    2020/06/04 00:13:58 Use the following password to login: &_4#\3^5V8vLTd)E"NWiJBs26G*9HPl1
    ```

  - 重新运行portainer,密码 为👆重置的 &_4#\3^5V8vLTd)E"NWiJBs26G*9HPl1

    ```shell
    docker start "id-portainer-container"
    ```

- 现在密码为 admin/admin

- 2021年12月12日， 密码更改 admin/admin123



### nacos

- run

  ```shell
  docker run -d --name nacos -p 8848:8848 -e PREFER_HOST_MODE=hostname -e MODE=standalone nacos/nacos-server
  ```

  - Linux memory is insufficient

    ```shell
    docker run -e JVM_XMS=256m -e JVM_XMX=256m --env MODE=standalone --name nacos -d -p 8848:8848 nacos/nacos-server
    ```

    

### redis

> 使用docker-compose up redis启动容器时，如果配置自定义配置文件 redis.conf，需要设置

```
bind 0.0.0.0
daemonize no
```

> docker-compose.yml文件内容

```yaml
version: "3.7"                                                                            services:
  redis:
    image: "redis:alpine"
    stdin_open: true #打开标准输入，可以接受外部输入。
    tty: true  #模拟一个伪终端。
    volumes:
      - /docker/projects/test/redis.conf:/data/redis.conf # 主机路径:容器路径
    #   - /docker/projects/test/redis/data:/data
    #   - /docker/projects/test/redis/logs:/logs
    command: redis-server --include /data/redis.conf
```

> 使用 docker-compose --verbose up redis启动，可查看启动详情





### 修改已有容器的端口映射

1. 停止容器 

2. 停止docker服务(systemctl stop docker) 

3. 修改这个容器的hostconfig.json文件中的端口（原帖有人提到，如果config.v2.json里面也记录了端口，也要修改）

   ```shell
   cd /var/lib/docker/3b6ef264a040* #这里是CONTAINER ID
   vi hostconfig.json
   如果之前没有端口映射, 应该有这样的一段:
   "PortBindings":{}
   增加一个映射, 这样写:
   "PortBindings":{"3306/tcp":[{"HostIp":"","HostPort":"3307"}]}
   前一个数字是容器端口, 后一个是宿主机端口. 
   而修改现有端口映射更简单, 把端口号改掉就行.
   ```

4. 启动docker服务(systemctl start docker) 

5. 启动容器



### 配置容器的镜像源（安装vim）

```shell
mv /etc/apt/sources.list /etc/apt/sources.list.bak

echo "deb http://mirrors.163.com/debian/ jessie main non-free contrib" >/etc/apt/sources.list

echo "deb http://mirrors.163.com/debian/ jessie-proposed-updates main non-free contrib" >>/etc/apt/sources.list

echo "deb-src http://mirrors.163.com/debian/ jessie main non-free contrib" >>/etc/apt/sources.list

echo "deb-src http://mirrors.163.com/debian/ jessie-proposed-updates main non-free contrib" >>/etc/apt/sources.list 
#更新安装源 
apt-get update 
#如果下载过程中卡在[waiting for headers] 删除/var/cache/apt/archives/下的所有文件 
#安装vim 
apt-get install vim
```