---
title: docker best practices
date: 2021-08-16 14:34:30
tags: practice
categories: docker
---

# best practices

## install

### dnf

- repo

  ```shell
  sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
  ```

- install

  ```shell
  sudo dnf -y install docker-ce docker-ce-cli containerd.io docker-compose-plugin
  ```

- start

  ```shell
  sudo systemctl --now enable docker
  ```

- (optional) allow a non-root user to manage docker

  ```shell
  # Add the current user
  sudo usermod -aG docker $USER
  
  # Add a specific user
  sudo usermod -a -G docker custom-user
  
  # logout and login new session 
  ```

## config default data directory

- default data directory `/var/lib/docker`

- change default directory

  ```shell
  # step1
  systemctl stop docker
  systemctl stop docker.socket
  
  # step2
  vim /etc/docker/daemon.json
  
  {
    "data-root": "/path/to/directory"
  }
  
  # step3 (optional)
  sudo mv /var/lib/docker /path/to/directory
  
  # step2
  systemctl start docker.socket
  systemctl start docker 
  
  # check modify available
  docker info | grep "Docker Root Dir"
  ```

## non-root user manage docker

1. Create the `docker` group.

   ```shell
   sudo groupadd docker
   ```

2. Add your user to the `docker` group.

   ```shell
   sudo usermod -aG docker $USER
   ```

3. Log out and log back in so that your group membership is re-evaluated.

   If testing on a virtual machine, it may be necessary to restart the virtual machine for changes to take effect.

   On a desktop Linux environment such as X Windows, log out of your session completely and then log back in.

   On Linux, you can also run the following command to activate the changes to groups:

   ```shell
   newgrp docker 
   ```

4. Verify that you can run `docker` commands without `sudo`.

   ```shell
   docker run hello-world
   ```

## migration

reference 

[5 ways to move Docker container to another host](<https://bobcares.com/blog/move-docker-container-to-another-host/>)

[Build a Docker Image with MySQL Database](https://morioh.com/p/d8d9e7732952)

### Plan A

- Step1	create an Image From a Container

> Create a new image from a container’s changes
>
> [commit command](<https://docs.docker.com/engine/reference/commandline/commit/>)

```shell
 sudo docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
```

options

| Name, shorthand    | Default | Description                                       |
| ------------------ | ------- | ------------------------------------------------- |
| `--author` , `-a`  |         | Author (e.g., "will brook")                       |
| `--change` , `-c`  |         | Apply Dockerfile instruction to the created image |
| `--message` , `-m` |         | Commit message                                    |
| `--pause` , `-p`   | `true`  | Pause container during commit                     |

- Step 2    export the image to a file  

```shell
sudo docker save -o /path/to/your_image.tar your_image_name
```

- Step 3 load the Docker image file

```shell
sudo docker load -i your_image.tar
```

### Plan B

- Step 1

First save the new image by finding the container ID (using [`docker container ls`](https://docs.docker.com/engine/reference/commandline/ps/)) and then committing it to a new image name. Note that only `a-z0-9-_.` are allowed when naming images:

```shell
# create image from container
docker container commit c16378f943fe rhel-httpd:latest
```

- Step 2

 tag the image with the host name or IP address, and the port of the registry:

```shell
 # re-tag repository:tag info about image
 docker image tag rhel-httpd:latest registry-host:5000/myadmin/rhel-httpd:latest
 or
 docker tag 0e5574283393 registry-host:5000/myadmin/rhel-httpd:latest
```

- Step 3

log in from Docker client:

```
docker login <harbor_address>
```

- Step 4

push the image to the registry using the image ID. 

In this example the registry is on host named `registry-host` and listening on port `5000`. (harbor默认配置端口80，详见harbor.yml)

```shell
 # push repository:tag,
 docker image push registry-host:5000/myadmin/rhel-httpd:latest
 or
 docker push registry-host:5000/myname/myimage
```

## Pull Image from Harbor

[Connecting to Harbor via HTTP](<https://goharbor.io/docs/2.0.0/install-config/run-installer-script/#connect-http>)

- Step 1

add the option `--insecure-registry` to your client’s Docker daemon. By default, the daemon file is located at `/etc/docker/daemon.json`.

```shell
{
"insecure-registries" : ["ip:port", "0.0.0.0"] #如果port为80，则可省略
}
```

Restart Docker Engine.

```shell
systemctl restart docker
```

- Step 2

```shell
docker pull hostAddress/library/REPOSITORY:TAG
```

## check docker disk usage

```shell
docker system df
```

## removes unused containers, images, and volumes

```shell
docker system prune -a
or 
docker system prune
```

# troubleshoot

- Failed to load listeners: no sockets found via socket activation: make sure the service was started by systemd

```
edit the docker’s systemd unit file and change the ExecStart:
sudo systemctl edit docker.service

edite The value ExecStart, change fd:// to unix://:
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd -H unix://
```

- Error creating default "bridge" network: cannot create network (docker0): conflicts with network (docker0): networks have same bridge name

```shell
sudo rm -rf /var/lib/docker/network
sudo systemctl start docker
```

- accident delete /var/run/docker.sock

```shell
sudo systemctl stop docker
sudo systemctl restart docker.socket
sudo systemctl start docker
```

- check run image error 

  ```shell
  docker inspect --format='{{.State.Status}} {{.State.ExitCode}} {{.State.Error}}' $(docker ps -alq)
  ```

  
