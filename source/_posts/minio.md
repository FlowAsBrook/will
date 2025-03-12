---
title: minio snippet
date: 2024-09-29 10:25:00
tags: snippet
categories: minio
---

# concept

## Erasure Coding

**Erasure Coding** splits data into data and parity shards. For example, MinIO might use a **4:2 erasure code** (should use 6  disks), meaning each object is split into 4 data shards and 2 parity shards.

> In a 4:2 erasure coding setup:
>
> 4 data shards hold the actual object data.
>
> 2 parity shards are created for redundancy.

In this configuration, every object is broken into smaller pieces, with redundancy pieces (parity shards) stored to allow recovery if a disk or node fails.

## server

```shell
  # service file
  /usr/lib/systemd/system/minio.service
  
  # default config file
  /etc/default/minio
   
  # reload config after modify default file
  sudo systemctl stop minio
  sudo systemctl daemon-reload
  
  # 如果更改MINIO_VOLUMES信息，并且需要用到之前的一些盘符，需要清理干净相关目录的文件（包括隐藏文件 .minio.sys）
  需要目录里清理遗留的所有文件 （rm -rf ./* 命令不会清理隐藏文件）
```

- minio.service

  ```yaml
  [Unit]
  Description=MinIO
  Documentation=https://docs.min.io
  Wants=network-online.target
  After=network-online.target
  AssertFileIsExecutable=/usr/local/bin/minio
  
  [Service]
  Type=notify
  
  WorkingDirectory=/usr/local
  
  User=dingofs
  Group=dingofs
  ProtectProc=invisible
  
  EnvironmentFile=-/etc/default/minio
  ExecStart=/usr/local/bin/minio server $MINIO_OPTS $MINIO_VOLUMES
  
  # Let systemd restart this service always
  Restart=always
  
  # Specifies the maximum file descriptor number that can be opened by this process
  LimitNOFILE=1048576
  
  # Turn-off memory accounting by systemd, which is buggy.
  MemoryAccounting=no
  
  # Specifies the maximum number of threads this process can create
  TasksMax=infinity
  
  # Disable timeout logic and wait until process is stopped
  TimeoutSec=infinity
  
  SendSIGKILL=no
  
  [Install]
  WantedBy=multi-user.target
  ```

- /etc/default/minio

  ```yaml
  MINIO_VOLUMES="http://minio{1...3}.dev.net:19000/mnt/disk2/minio"
  #MINIO_VOLUMES="http://minio{1...3}.dev.net:19000/mnt/disk{0...1}/minio"
  #MINIO_VOLUMES="http://minio1.dev.net:19000/mnt/disk1/minio http://minio2.dev.net:19000/mnt/disk1/minio http://minio3.dev.net:19000/mnt/disk1/minio"
  #MINIO_VOLUMES="http://minio2.dev.net:19000/mnt/minio http://minio3.dev.net:19000/mnt/minio"
  
  # Set all MinIO server options
  #
  # The following explicitly sets the MinIO Console listen address to
  # port 9001 on all network interfaces. The default behavior is dynamic
  # port selection.
  
  #MINIO_OPTS="--console-address :9001"
  MINIO_OPTS=' --console-address=":19001" --address=":19000" '
  
  # Set the root username. This user has unrestricted permissions to
  # perform S3 and administrative API operations on any resource in the
  # deployment.
  #
  # Defer to your organizations requirements for superadmin user name.
  
  MINIO_ROOT_USER=minioadmin
  
  # Set the root password
  #
  # Use a long, random, unique string that meets your organizations
  # requirements for passwords.
  
  MINIO_ROOT_PASSWORD=minioadmin
  ```

  

# client

## install

  ```shell
  curl https://dl.min.io/client/mc/release/linux-amd64/mc \
    --create-dirs \
    -o $HOME/minio-binaries/mc
  
  chmod +x $HOME/minio-binaries/mc
  
  cp $HOME/minio-binaries/mc /usr/bin/
  
  mc alias set myminio http://minio1.dev.net:19000 {ak} {sk}
  ```

## command

- log

```shell
mc admin logs myminio
```

- development

```shell
# add development
mc alias set myminio {endpoint_address} {ak} {sk} 
# e.g. mc alias set myminio http://minio1.dev.net:19000 1111111 22222222

# alias-tls
看下面的 mc alias 内容

# ls alias
mc alias list

# overview info
mc admin info myminio

# check config 
mc admin config get myminio
```
- bucket

```shell
# create bucket
mc mb <ALIAS>/<BUCKET>

# list bucket
mc ls <ALIAS>

# check bucket
mc ls <ALIAS>/<BUCKET>

# count total object/file num
mc ls --summarize --recursive gateway-minio

# check object
mc cat <ALIAS>/<BUCKET>/<OBJECT>

# delete object
mc rm <ALIAS>/<BUCKET>/<OBJECT>

# delete according specify time 
mc find <alias/bucket-name> --older-than <time> --exec "mc rm {}"
# e.g. mc find myminio/mybucket --older-than 30d --exec "mc rm {}"

# delete all data
mc rm --recursive --force <ALIAS>/<BUCKET>

# force delete bucket
mc rb --force <ALIAS>/<BUCKET>  # must delete bucket's object file first
```

- heal

```shell
# Use MinIO’s heal command to ensure the data and metadata are consistent:
mc admin heal -r <ALIAS>/
```



- erasure code

```shell
# set erasure code
mc admin config set myminio storage_class standard=EC:0
# should restart minio.service, and 'systemctl status minio' will print 'WARN: The standard parity is set to 0. This can lead to data loss'

# check storage_class 
mc admin config get myminio storage_class
```

# TLS

- [enabling-tls](https://min.io/docs/minio/linux/operations/network-encryption.html#enabling-tls)

> 172.20.61.102 minio1.dev.net 
> 172.20.61.103 minio2.dev.net 
> 172.20.61.105 minio3.dev.net
>
> /etc/default/minio: 
> MINIO_VOLUMES="https://minio{1...3}.dev.net:19000/mnt/minio{1...2}" 
> MINIO_OPTS=' --console-address=":19001" --address=":19000" '

By default, the MinIO server looks for the TLS keys and certificates for each node in the following directory: `${HOME}/.minio/certs` (该证书存放各自机器的`private.key` 和 `public.crt`， 每台机器的 `private.key` 和 `public.crt`都不一样)

If using Certificates signed by a non-global or non-public Certificate Authority, *or* if using a global CA that requires the use of intermediate certificates, you must provide those CAs to the MinIO Server `/root/.minio/certs/CAs` (用来存放私有的证书`ca.crt`，该证书用于生成机器的`private.key` 和 `public.crt`， 集群的每台机器存放的`ca.crt`应该保持一致)

1. **Generate CA and Certificates `generate_certificate.sh`**

```bash
# Generate CA private key
openssl genrsa -out ca.key 4096

# Create CA certificate
openssl req -x509 -new -nodes -key ca.key -sha256 -days 3650 \
    -out ca.crt \
    -subj "/C=US/ST=YourState/L=YourCity/O=YourOrg/OU=YourUnit/CN=MinioCA"

# Generate server private keys for each node
for node in minio1 minio2 minio3; do
    openssl genrsa -out ${node}.key 2048
    
    # Create server certificate signing request (CSR)
    openssl req -new -key ${node}.key -out ${node}.csr \
        -subj "/C=US/ST=YourState/L=YourCity/O=YourOrg/OU=YourUnit/CN=${node}.dev.net"
    
    # Create server certificate configuration
    cat > ${node}.ext << EOF
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
subjectAltName = @alt_names

[alt_names]
DNS.1 = ${node}.dev.net
IP.1 = 172.20.61.$(grep ${node} /etc/hosts | awk '{print $1}' | cut -d. -f4)
EOF

    # Sign server certificate with CA
    openssl x509 -req -in ${node}.csr -CA ca.crt -CAkey ca.key \
        -CAcreateserial -out ${node}.crt -days 3650 \
        -sha256 -extfile ${node}.ext
done
```

2. **Distribute Certificates `copy_certificate.sh`**
```bash
# Create certificate directories
for node in minio1 minio2 minio3; do
    # Copy node-specific certificate and key
    scp ${node}.crt root@${node}.dev.net:/root/.minio/certs/public.crt
    scp ${node}.key root@${node}.dev.net:/root/.minio/certs/private.key
    
    # Copy CA certificate to trusted CAs
    scp ca.crt root@${node}.dev.net:/root/.minio/certs/CAs/
done
```

3. **Update MinIO Configuration**
Modify `/etc/default/minio` on each node to ensure proper TLS configuration:
```bash
# Update MINIO_VOLUMES to use https
MINIO_VOLUMES="https://minio{1...3}.dev.net:19000/mnt/minio{1...2}"
```

4. Set Correct Permissions (optional)
```bash
for node in minio1 minio2 minio3; do
    ssh root@${node}.dev.net "
    chown -R minio:minio /root/.minio/certs
    chmod 700 /root/.minio/certs
    chmod 600 /root/.minio/certs/private.key
    chmod 644 /root/.minio/certs/public.crt /root/.minio/certs/CAs/*
    "
done
```

5. **Restart MinIO Cluster**
```bash
systemctl daemon-reload
systemctl restart minio
```

6.  **Trust certificate globally**

   > 每台机器均需要操作一遍（这样可以保证mc设置alias成功）

   ```shell
   sudo cp ca.crt /etc/pki/ca-trust/source/anchors/
   sudo update-ca-trust extract
   sudo openssl verify -CAfile /etc/pki/tls/certs/ca-bundle.crt /etc/pki/ca-trust/source/anchors/ca.crt
   ```

7. Verification

```bash
# Check TLS configuration on each node
for node in minio1 minio2 minio3; do
    echo "Checking ${node}:"
    ssh root@${node}.dev.net "
    openssl x509 -in /root/.minio/certs/public.crt -text -noout
    echo '---'
    netstat -tuln | grep 19000
    echo '---'
    systemctl status minio
    "
done
```

browser check

<img src="/images/minio/cert.jpg" style="zoom: 60%">

## mc alias

- step1: fetch the MinIO server’s certificate

  ```shell
  echo -n | openssl s_client -connect 172.30.14.127:19000 -showcerts | openssl x509
  ```

- step2: Move the Certificate to the Trusted Store

  - ubuntu

    ```shell
    sudo mkdir -p /usr/local/share/ca-certificates/  
    # copy the entire certificate from step1 (including -----BEGIN CERTIFICATE----- and -----END CERTIFICATE-----).
    sudo vim /usr/local/share/ca-certificates/minio.crt
    sudo update-ca-certificates
    ```

  - rocky

    ```shell
    sudo mkdir -p /etc/pki/ca-trust/source/anchors/
    # copy the entire certificate from step1 (including -----BEGIN CERTIFICATE----- and -----END CERTIFICATE-----).
    sudo vim /etc/pki/ca-trust/source/anchors/minio.crt
    sudo update-ca-trust extract
    export SSL_CERT_FILE=/etc/pki/ca-trust/source/anchors/minio.crt
    ```

- step3: add alias

  ```shell
  mc alias set minio127 https://172.30.14.127:19000 <ak> <sk>
  ```

  
