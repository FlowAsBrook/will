---
title: docker container
date: 2021-12-13 18:07:46
tags: snippet
categories: docker
---

> 默认情况下，Docker 会为你提供一个隐含的 ENTRYPOINT，即：/bin/sh -c。所以，在不指定 ENTRYPOINT 时，比如运行在容器里的完整进程是：/bin/sh -c “python xxx.py”，即 CMD 的内容就是 ENTRYPOINT 的参数

## user

```shell
# Add the current user
sudo usermod -a -G docker $(whoami)

# Add a specific user
sudo usermod -a -G docker custom-user
```

# 容器

- check
```shell
# 查看正在运行
docker ps
# 查看所有
docker ps -a
```

- docker run

  > docker run is a higher-level command that combines multiple subcommands (docker create, docker start, docker exec, etc.) into a single step.
```shell
# 创建容器
docker run -p <宿主机端口>:<容器端口> <镜像名称>[:标签]

# 设置容器停止之后自动清理容器
docker run --rm xxx  # --rm This flag tells Docker to automatically remove the container when it exits.

# Run a New Container with Volume Mount
docker run -it --name my-container -v /host/path:/container/path my-image
```
- other
```shell
# 启动
docker start 容器名或容器 id

# 终止
docker stop [NAME]/[CONTAINER ID]:将容器退出。
docker kill [NAME]/[CONTAINER ID]:强制停止一个容器。

# 清理已经停止的容器
docker container prune

# 查看容器端口
docker port {container_id}

# 查看容器启动命令
docker inspect -f '{{.Config.Cmd}}' {container_id}

# 查看容器挂载信息
docker inspect {container_id} --format='{{json .Mounts}}' | jq

# 删除
docker rm -f 容器id
# 批量删除已停止的容器
docker rm $(docker ps -a -q -f "status=exited")


# 导出
docker export 容器id > xxx.tar
# 导入
docker import - test/xxx:v1
# 重启
docker restart $container_id
# 日志
docker logs $container_id

# 复制文件 在容器运行和停止的状态均可执行
docker cp /host/path/ <container_id>:/path/to/file
or
docker cp <container_id>:/path/to/file /host/path/


# Monitor Real-Time
docker stats <containerId>

# Check specify container's PID
docker inspect --format '{{.State.Pid}}' <containerId>
```

## volume

### Propagation Modes

> with rshared: Unmounting inside the container affects the host’s mount as well.
>
> with rprivate: Unmounting inside the container only affects the container, leaving the host’s mounts untouched.

- rprivate **(Recursive Private)**:

  - **Isolated Mount Propagation**: When a mount point is set to rprivate, any mount or unmount events within that mount are **not propagated** between the container and the host or vice versa. Changes in mounts within the container are isolated.

    **Example**: If you mount or unmount a file system inside the container, it won’t affect the corresponding mount on the host. Similarly, changes to the mounts on the host won’t reflect inside the container.

- rshared **(Recursive Shared)**:

  - **Mount Propagation Between Host and Container**: In rshared mode, mount and unmount events are **propagated both ways** between the host and the container.

    **Example**: If you unmount or mount something inside the container, it will be reflected on the host. Conversely, if the host unmounts or mounts a file system on that mount point, it will also reflect inside the container.

## network

| Driver    | Description                                                  |
| :-------- | :----------------------------------------------------------- |
| `bridge`  | The default network driver.                                  |
| `host`    | Remove network isolation between the container and the Docker host. |
| `none`    | Completely isolate a container from the host and other containers. |
| `overlay` | Overlay networks connect multiple Docker daemons together.   |
| `ipvlan`  | IPvlan networks provide full control over both IPv4 and IPv6 addressing. |
| `macvlan` | Assign a MAC address to a container.                         |

- 列出docker的所有网络模式

  ```shell
  docker network ls
  ```

- 针对bridge和host分别查找有哪些container在其中

  ```shell
  docker network inspect bridge
  docker network inspect host
  ```

- 直接查看container的信息，找到network段查看。或者用grep筛选出network。

  ```shell
  docker inspect 容器名/容器ID
  docker inspect 容器名/容器ID | grep -i “network” # 其中grep的“-i”表示不区分大小写。
  ```

## Exit Codes

Common exit codes associated with docker containers are:

- **Exit Code 0**: Absence of an attached foreground process

- **Exit Code 1**: Indicates failure due to application error

- **Exit Code 137**: Indicates failure as container received SIGKILL (Manual intervention or ‘oom-killer’ [OUT-OF-MEMORY])

- **Exit Code 139**: Indicates failure as container received SIGSEGV

- **Exit Code 143**: Indicates failure as container received SIGTERM

- **Exit Code 126**: Permission problem or command is not executable

- **Exit Code 127**: Possible typos in shell script with unrecognizable characters

## third container 

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
  或
  docker exec -it mysql-server /bin/sh
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

- 重新安装
  
  ```shell
  sudo docker run -d -p 8000:8000 -p 9443:9443 --name portainer \
      --restart=always \
      -v /var/run/docker.sock:/var/run/docker.sock \
      -v portainer_data:/data \
      cr.portainer.io/portainer/portainer-ce:2.9.3
  ```

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

## solution

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

# dockerfile

- **RUN** is executed while the image is being build

  while **ENTRYPOINT** is executed after the image has been built.

# advanced

```shell
echo "/core/core.%e.%p.%t" | sudo tee /proc/sys/kernel/core_pattern
```

