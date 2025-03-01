---
title: file system
date: 2024-08-07 13:59:21
tags: learn
categories: fs
---

# storage

## Object, Block and File Storage

| Feature              | **Object Storage**                          | **Block Storage**                     | **File Storage**                               |
| -------------------- | ------------------------------------------- | ------------------------------------- | ---------------------------------------------- |
| **Data Structure**   | Stores as objects (data + metadata)         | Stores in blocks (raw chunks of data) | Stores as files in directories                 |
| **Access Protocols** | HTTP APIs (e.g., S3)                        | iSCSI, Fibre Channel                  | NFS, SMB/CIFS                                  |
| **Performance**      | High scalability, slower access             | Low latency, high IOPS                | Moderate, varies by file system                |
| **Scalability**      | Virtually unlimited (distributed)           | Scales well with SAN, but complex     | Limited by file system performance             |
| **Cost Efficiency**  | High for large amounts of unstructured data | Can be expensive at scale             | Moderate to expensive                          |
| **Best Use Cases**   | Media content, backups, big data, archives  | Databases, VMs, transactional systems | Shared access, collaboration, home directories |
| **Metadata**         | Rich, customizable per object               | None                                  | Minimal (file permissions, attributes)         |
| **Concurrency**      | Excellent for concurrent access             | Not designed for shared access        | Designed for shared access                     |
| **Example Systems**  | AWS S3, MinIO, Google Cloud Storage         | AWS EBS, iSCSI, Fibre Channel         | NFS, SMB/CIFS, GlusterFS                       |

- **Object Storage**: Best for large-scale unstructured data, low cost, scalable but not suitable for low-latency or transactional workloads.
- **Block Storage**: High performance, low latency, granular control over data, ideal for VMs, databases, but can be costly to scale.
- **File Storage**: File-based storage for shared access, good for collaboration, but less scalable for large datasets.

**Object storage**, **block storage**, and **file storage** are all methods of storing and accessing data, but they differ in structure, management, and how they handle data. Despite their differences, they often complement each other in modern storage systems, and sometimes, the boundaries between them blur depending on use cases.

**Relation**

- **Different Levels of Abstraction**:
  - **Block Storage** is the most granular, dealing with raw blocks of data.
  - **File Storage** is built on top of block storage, organizing data into files and directories.
  - **Object Storage** exists at a higher level, organizing data into objects with associated metadata and accessed through APIs, not files or blocks.
  
- **Integration in Systems**:
  - Object storage can use block storage underneath to store objects.
  - File storage is typically built on block storage, where the blocks form the basis of a file system.
  - Many modern systems (especially in cloud environments) allow the coexistence of these types of storage, where object storage holds bulk data, block storage serves databases and high-performance workloads, and file storage provides shared file access.

**senario**: A Cloud-Based Photo Sharing Application

Let’s say we’re developing a photo-sharing application. Here's how each storage type might be used:

1. **Block Storage**: 
   - The application’s **database** (such as MySQL or PostgreSQL) will store metadata about users, photos, and comments. Databases need low-latency, fast read/write operations, which makes block storage ideal. Each database table or index is stored in blocks, providing high performance and reliability.
   - Example: An AWS EC2 instance uses **Amazon Elastic Block Store (EBS)** for fast data access in the application’s database.
   
2. **File Storage**:
   - For **user-generated content**, like profile pictures or documents that may need to be shared and updated by multiple users, file storage is the best fit. It allows users to upload, read, and modify files easily. The hierarchical structure also works well for organizing files into folders.
   - Example: The application uses **NFS (Network File System)** or **SMB/CIFS** to store shared user profiles, document uploads, or configuration files. In this case, multiple application servers can access the same files.
   
3. **Object Storage**:
   - For **large photos and videos** uploaded by users, object storage is the best choice due to its scalability and cost efficiency. Each file (photo or video) is stored as an object with metadata like date uploaded, user ID, etc. Object storage is ideal because it can easily scale to store millions of large media files.
   - Example: The application stores users’ photos and videos in **Amazon S3** or **MinIO** as objects, where each photo or video is stored independently and accessed through an API.

**The Workflow Example**:

- When a user uploads a new photo:
  1. **File Storage** stores any accompanying text (e.g., captions, metadata) that might be shared or updated by other users.
  2. **Block Storage** is used to store transaction information, user details, and other relational data in the app's database.
  3. **Object Storage** stores the actual image file (the photo itself) as an object, and the app accesses it via an HTTP API when needed.

**Visual Relationship**

```plaintext
Object Storage (High-level, flat structure)
|-- Stores: Photos, Videos
|-- Metadata: Filename, upload time, user ID
|-- Access via: APIs (e.g., S3 API)

File Storage (Intermediate level, hierarchical structure)
|-- Stores: Text files, configuration files, shared resources
|-- Hierarchical access: Files are accessed via paths (/photos/, /users/)
|-- Access via: NFS, SMB/CIFS

Block Storage (Low-level, raw storage)
|-- Stores: Database records, application data, raw volumes
|-- Managed by: File systems (ext4, XFS) or directly by applications (databases)
|-- Access via: iSCSI, Fibre Channel, or direct local access
```

- **Key Integration Points**

1. **Data flows between storage types**:
   - Object storage may rely on block storage for its underlying infrastructure. For instance, in a cloud service, object storage systems often store objects across distributed blocks on physical storage.
   - File storage often relies on block storage (e.g., a disk or volume formatted with a file system) to organize files into directories. 

2. **Shared Purpose**:
   - All three storage types can coexist in a modern system, handling different parts of the same application. Block storage handles the core operational data; file storage manages shared, accessible data; and object storage is used for scalable, long-term archival of data.

In summary, **block storage** is the foundational layer, **file storage** organizes files in a hierarchical structure on top of block storage, and **object storage** organizes data in a flat, scalable way with metadata and API-based access. Each serves a different purpose but can be used together for a comprehensive data storage solution in complex applications.

## S3 Gateway

S3 Gateway acts as an intermediary to facilitate interactions between your application and the object storage system. This includes common operations on **buckets** and **objects** such as:

- **Creating buckets** (e.g., `CreateBucket` API call)
- **Reading objects** (e.g., `GetObject`)
- **Uploading or writing objects** (e.g., `PutObject`)
- **Deleting objects or buckets** (e.g., `DeleteObject`, `DeleteBucket`)

The S3 gateway translates these requests into **S3 API calls** that the object storage system understands. This is especially useful when:
1. Your application isn't natively using S3 but requires access to an S3-compatible storage system (via the gateway).
2. You're in a hybrid or on-premises environment where you want applications to interact with object storage in the same way they would with a cloud-based S3 service.

For example, if you have a legacy application that doesn't natively support the S3 API but needs to manage objects (e.g., create or read files), the S3 gateway can allow the application to perform these tasks as if it were working with a regular file system. The gateway will handle the **CRUD** (Create, Read, Update, Delete) operations on **buckets** and **objects** by mapping them to the appropriate S3 commands.

So, when operating on buckets and objects, especially in environments where direct S3 API calls are not feasible or supported, using an S3 gateway is a key method. The gateway allows you to continue using familiar protocols while interacting with S3-compatible storage systems.

# concept

## FS type

![fileSystemType.jpeg](/images/fs/fileSystemType.jpeg)

- Mac 默认可以读 Windows 的 NTFS 格式，但不能写。

- Windows 无法识别 Mac 的 HFS+ 或 APFS 格式。

- Mac 和 Windows 都能正常读写 FAT32 和 ExFAT 格式

- linux

  ```
  Linux：存在几十个文件系统类型：ext2，ext3，ext4，xfs，brtfs，zfs（man 5 fs可以取得全部文件系统的介绍）
  
  不同文件系统采用不同的方法来管理磁盘空间，各有优劣；文件系统是具体到分区的，所以格式化针对的是分区，分区格式化是指采用指定的文件系统类型对分区空间进行登记、索引并建立相应的管理表格的过程。
  
  ext2具有极快的速度和极小的CPU占用率，可用于硬盘和移动存储设备
  ext3增加日志功能，可回溯追踪
  ext4日志式文件系统，支持1EB（1024*1024TB），最大单文件16TB，支持连续写入可减少文件碎片。rhel6默认文件系统
  xfs可以管理500T的硬盘。rhel7默认文件系统
  brtfs文件系统针对固态盘做优化，
  ```

- windows

  ```
  FAT16：MS—DOS和win95采用的磁盘分区格式，采用16位的文件分配表，只支持2GB的磁盘分区，最大单文件2GB，且磁盘利用率低
  FAT32：（即Vfat）采用32位的文件分配表，支持最大分区128GB，最大文件4GB
  NTFS：支持最大分区2TB，最大文件2TB，安全性和稳定性非常好，不易出现文件碎片。
  ```

## Blocks

- **Definition**: A block is the smallest unit of storage that the filesystem can manage and read/write. Blocks are fixed-size data units (e.g., 4KB or 8KB), commonly used by the filesystem.

- **Purpose**: Blocks enable efficient storage and management of data on physical media, allowing the filesystem to allocate, read, and write specific portions of data without needing to process an entire file.

- **In FUSE**:

  - FUSE-based filesystems often work with underlying block devices (like HDDs or SSDs) or other storage solutions (like networked file systems) that use block-level access.

  - FUSE translates file operations (e.g., open, read, write) into these block operations, allowing efficient data management despite being in userspace, which typically has more overhead than kernel space.

## **Chunks**

- **Definition**: A chunk is a larger, logical grouping of data that may span multiple blocks. Chunks are often used in distributed or object storage systems where data is split into parts for parallelism, redundancy, or ease of distribution.

- **Purpose**: Chunks allow data to be divided and distributed, making it possible for distributed filesystems to store data across multiple nodes, providing redundancy, load balancing, and better performance.

- **In FUSE**:

  - Distributed filesystems or object storage systems accessible via FUSE (like Ceph, GlusterFS, or MinIO gateways) may break files into chunks. FUSE then handles data in terms of these chunks, passing it to the storage backend, which manages chunk distribution and retrieval.

  - Chunks provide advantages in scalability and reliability when FUSE is used to interface with distributed storage systems, allowing filesystems to grow seamlessly across multiple devices.

**How Blocks and Chunks Work Together on FUSE**

In a FUSE-based setup, **blocks** are typically handled at the disk or device level (within a single node), while **chunks** are managed at a higher level (often in a distributed or networked environment). FUSE enables interoperability by translating file operations, letting the application layer access chunks and blocks transparently.

For example, a file request from an application to a FUSE-mounted distributed filesystem may be:

​	1.	Split into chunks (for parallel access across nodes).

​	2.	Each chunk is further divided into blocks if stored on a block-based medium, facilitating storage efficiency and reliability.

## inode

>  In computer science, an **inode** (short for "index node") is a data structure that represents a file or directory in a file system. Inodes are the basic building blocks of a file system, and they play a crucial role in managing files and directories.                                                                                                                

- **properties**

An inode is a unique identifier for a file or directory on a file system. It contains metadata about the file or directory, An inode typically has several properties:             

 **Type**: The type of file or directory (e.g., regular file, directory, symbolic link)                        

**Permissions**: The access rights for the file or directory (owner, group, other)                          

**Timestamps**: Creation, modification, and access timestamps                                     

**Inode number**: A unique identifier for the inode        

**Block count**: The number of blocks allocated to the file                                      

**Block offset**: The starting block offset for the file           

**Inodes vs. Blocks**                                                                                                                             

When a file is created on a file system, it's divided into smaller blocks of data, called **blocks**. Each block contains a portion of the file's contents. The inode, on the other hand, contains metadata about the file, including the location of the blocks that make up the file.

Think of an inode as a "directory entry" for a file, and a block as a chunk of data stored in a file system. When you create or delete a file, the inode is updated to reflect changes in the file's metadata,                                 

while the blocks remain intact.

- **File System Operations**

When you perform file system operations like create, delete, or modify a file, the following happens:                  

1. **Create file**:                         

​    \* A new inode is created with the specified type and permissions.                                

​    \* Blocks are allocated to fill out the file's contents.                                     

2. **Delete file**:                         

​    \* The inode associated with the file is deleted.                                        

​    \* Any blocks allocated to the file are freed.        

3. **Modify file**:                         

​    \* The existing inode is updated with the new metadata (e.g., changes in permissions, timestamps).                

​    \* No new blocks need to be allocated; any changes occur within the existing block structure.                  

- **Inode Number**                          

The inode number is a unique identifier for an inode. It's used by the operating system and file system to manage files and directories. When you delete a file, the inode number is updated to indicate that it no longer exists.

**In summary**

Inodes represent files and directories in a file system.

They contain metadata such as type, permissions, timestamps, and inode number.

Blocks contain the actual data for a file or directory.

Inodes are updated when files or directories are created, deleted, or modified. 

## dentry

> Directory Entry

A dentry (directory entry) represents a mapping between a file name and an inode. Essentially, a dentry links a file or directory name to its respective inode.

**Key Characteristics of Dentries:**

​	•	**Path Resolution**: Dentries assist in translating file paths to inodes, making it faster for the OS to locate files in complex directory structures.

​	•	**Caches for Performance**: Dentries are cached to speed up file lookups, especially for frequently accessed files. When you access a file, the system checks the dentry cache first, reducing the need to repeatedly search the disk.

​	•	**Relationship with Inodes**: Dentries reference inodes rather than storing file metadata or data directly.

**How Inodes and Dentries Work Together:**

When accessing a file (e.g., opening or modifying it), the filesystem:

1. Uses the directory path to locate the **dentry** for each component of the path, starting from the root (/) to the desired file.

2. Each **dentry** provides a reference to the **inode** that holds the file’s metadata and data block locations.

	3.	Once the correct **inode** is found, the system can access the data blocks on the disk to perform the desired file operation.

**Summary:**

​	•	**Inodes** store metadata and data pointers for each file, managing essential details and the physical location of data.

​	•	**Dentries** map filenames to inodes, facilitating path resolution and improving filesystem performance through caching.

This division helps optimize file access and management, making filesystems more efficient and scalable.

## LVM 

>  (Logical Volume Manager) Represents the LVM system that manages the disks (physical volumes), creates VGs, and allocates LVs.

​	•	**Disk**: Represents the physical disks, such as /dev/sda or /dev/vdb.

​	•	**VG (Volume Group)**: Aggregates the space of one or more physical disks.

​	•	**LV (Logical Volume)**: A subdivision of the VG space, which can be mounted.

​	•	**Mount Point**: Where the LV is mounted in the filesystem.

<img src="/images/fs/lvm architecture.png">

## partitioning vs LVM

The key difference between **partitions** and **LVM** (Logical Volume Management) lies in how they manage and allocate disk space. Here’s a breakdown of each:

**Partitioning**:

Partitioning is a traditional way to divide a disk into sections, with each section functioning as a separate storage unit. These partitions are created directly on the disk, and once created, they are static unless you re-partition the disk (which can be complex and risky if the partitions contain data).

- **Physical Disk-Based**: Partitions are fixed divisions of a physical disk, where each partition has a specific size.
- **Static**: Once created, partitions are hard to resize, and managing space between partitions can be inflexible.
- **Limited**: Typically, you are limited to a small number of primary partitions (e.g., 4 primary partitions on an MBR disk, though extended partitions can allow more).
- **Direct Mounting**: Partitions can be formatted with a filesystem (e.g., `xfs`, `ext4`) and mounted directly to the filesystem.

Example:

```
/dev/sda1  ->  /boot
/dev/sda2  ->  /
/dev/sda3  ->  /home
```

**LVM**

>  Logical Volume Management

LVM is a more flexible and advanced disk management system. Instead of dividing a disk into fixed partitions, it uses logical volumes that can span across multiple physical disks. LVM allows you to create, resize, and move logical volumes much more easily than traditional partitions.

- **Flexible**: Logical volumes can be resized, expanded, or shrunk easily without affecting data. You can add more disks to an LVM group to increase space.
- **Virtual**: LVM creates a layer of abstraction over physical disks. You group one or more physical disks (or partitions) into a **Volume Group (VG)**, and from that pool of storage, you create **Logical Volumes (LV)**.
- **Resizing**: Logical volumes can be resized on the fly (especially for file systems like XFS or ext4 with online resize capabilities).
- **Spanning Disks**: LVM allows logical volumes to span multiple physical disks or partitions, meaning you are not limited by the size of a single disk.
- **Snapshots and RAID**: LVM supports features like snapshots and can be combined with RAID for redundancy.

Components:

1. **Physical Volume (PV)**: These are physical disks or partitions that are part of an LVM system.
2. **Volume Group (VG)**: A pool of storage made up of one or more physical volumes.
3. **Logical Volume (LV)**: A virtual partition that you can format and mount like a traditional partition. It is created from the space in the volume group.

Example:

1. You create a **Volume Group** (`vg1`) using two physical disks (`/dev/sdb`, `/dev/sdc`).
2. From the volume group, you create **Logical Volumes** (`lv_root`, `lv_home`) and mount them.
   
   ```
   /dev/vg1/lv_root  ->  /
   /dev/vg1/lv_home  ->  /home
   ```

**Comparison**

| Feature                 | Partition                                | LVM                                                   |
| ----------------------- | ---------------------------------------- | ----------------------------------------------------- |
| **Storage Flexibility** | Static size, hard to resize              | Dynamic, easy to resize or move volumes               |
| **Number of Volumes**   | Limited (4 primary, extended partitions) | Unlimited logical volumes in a volume group           |
| **Disk Spanning**       | Partition tied to a single disk          | Logical volumes can span multiple disks               |
| **Resizing**            | Complex and risky                        | Easily resizable without data loss                    |
| **Snapshots**           | Not supported                            | Supports snapshots for backups                        |
| **Management**          | Basic partition management tools         | Advanced management with `lvcreate`, `lvextend`, etc. |

**Example Scenario**:

1. **Partition**: You have a 500GB disk, and you partition it into `/boot`, `/`, and `/home`. If you need more space for `/home`, you'd have to resize or repartition the disk, which is not easy.

2. **LVM**: With LVM, you create a Volume Group from a few physical volumes (disks or partitions), and you can create logical volumes (`/`, `/home`) from that pool of space. If `/home` needs more space, you can easily expand it by adding another disk or reallocating space within the volume group.

**Summary**:

- **Partitioning** is a simple, traditional way to divide a disk, but it’s rigid and hard to manage once the partitions are set.
- **LVM** is a flexible, powerful storage management tool that allows you to create logical volumes that can be resized, expanded, and managed more easily, often spanning multiple disks.

## volume vs fs

The relationship between volumes and file systems is fundamental to understanding how data is organized and stored in computer systems.

**Volume**:

- A volume is a single accessible storage area with a single file system.
- It's a logical device that can span one or more physical storage devices or parts of them.
- In simple terms, you can think of a volume as a container for storing data.

**File System**:

- A file system is a method and data structure that the operating system uses to control how data is stored and retrieved on a volume.
- It defines how files are named, stored, organized, and accessed on the volume.

Relationship:

1. One-to-One Mapping:
   - Typically, there is a one-to-one relationship between a volume and a file system.
   - Each volume usually has one file system formatted on it.
2. Formatting:
   - When you "format" a volume, you're actually creating a file system on that volume.
   - This process prepares the volume to store files and directories in a way that the operating system can understand.
3. Mounting:
   - When you "mount" a volume, you're making its file system accessible to the operating system.
   - The mount point becomes the root directory of that file system within the larger file hierarchy.
4. Types:
   - Different types of file systems (e.g., NTFS, ext4, XFS, FAT32) can be used on volumes, each with its own features and limitations.
5. Management:
   - Volume management often involves tasks like creating, resizing, or deleting volumes.
   - File system management includes tasks like formatting, checking for errors, and managing permissions.
6. Abstraction:
   - The volume provides a level of abstraction between the physical storage (like hard drives or SSDs) and the file system.
   - This abstraction allows for features like RAID, where multiple physical devices can appear as a single volume.
7. Performance and Features:
   - The choice of file system can affect the performance and features available on a volume.
   - For example, some file systems support larger file sizes, journaling, or built-in compression.
8. Multiple File Systems:
   - In some advanced setups, it's possible to have multiple file systems on a single volume, though this is less common.
9. Logical Volume Management (LVM):
   - LVM allows for more flexible management of storage, where volumes can be easily resized, and multiple physical devices can be combined into a single logical volume.
10. Cloud and Virtualization:
    - In cloud and virtualized environments, volumes and file systems are often abstracted further, allowing for easy scaling and management.

## mount vs bind

- Mount

  - **Mounting** is the process of making a file system accessible at a certain point in the directory tree.

  - Typically used to attach external storage devices, like USB drives or partitions, to a directory.

  - Example: `mount /dev/sdX1 /mnt/mydrive`

- Bind

  - **Bind mounting** allows you to map a directory to another location in the same file system.

  - This is useful for accessing the same files from multiple locations without duplicating them.

  - Example: `mount --bind /source/directory /target/directory`

- Key Differences

  - **Purpose**: Mount is for attaching entire file systems; bind is for duplicating directory paths.

  - **Use Cases**: Mount is common for devices; bind is useful for creating alternative paths to existing directories within the same file system.

So, When you bind mount `directory1` (which is mounted to external file system A) to `directory2`, you create an alias for `directory1` at `directory2`. This means:

- Accessing `directory2` will show the contents of file system A.
- Any data written to `directory2` will be stored in file system A, not the local file system.

## hard link vs soft link             

|                            | Hard Link           | Soft Link (Symbolic Link) |
| -------------------------- | ------------------- | ------------------------- |
| Shared storage             | Same file on disk   | Separate file on disk     |
| Modifying one affects both | Yes                 | No                        |
| Separated copy             | One copy, two names | Two separate files        |

- Hard links share the same storage location on disk and modify together.

- Soft links point to an alias or shortcut that does not share the same storage location. Modifying one does not affect the other.

## tmpfs

- tmpfs is a special filesystem that uses RAM (and swap) for fast, temporary file storage.
- RAM is the hardware that holds the system’s active data, while tmpfs is a virtual filesystem that leverages RAM for storage.
- tmpfs is useful for temporary data that doesn’t need to persist after reboots, providing fast I/O performance since it’s in memory.

## nvme

**NVMe (Non-Volatile Memory Express)** is a high-performance storage protocol designed to take full advantage of the speed and efficiency offered by modern solid-state drives (SSDs), particularly those using **NAND flash** or **3D NAND** memory. It was developed to overcome the limitations of older storage protocols (such as SATA and SAS) by optimizing the interaction between the storage device and the CPU over faster interfaces like **PCIe (Peripheral Component Interconnect Express)**.

- NVMe is both a **protocol** and a **standard** for connecting **non-volatile memory** (such as NAND flash memory) directly to a computer system via high-speed interfaces, most commonly **PCIe**. It allows data to be transferred at much faster speeds compared to older storage protocols, enabling devices to reach their full potential and significantly reducing latency.

**nvme vs lvm**

- **NVMe** is the hardware interface and protocol that provides high-speed storage, primarily used for demanding workloads.
- **LVM** is a logical volume manager that allows you to manage storage in a flexible way, and it can be used with **NVMe drives** to create logical volumes, resize them, and add new storage easily.

| Feature          | NVMe                                             | LVM                                                 |
| ---------------- | ------------------------------------------------ | --------------------------------------------------- |
| **Type**         | Storage protocol (hardware level)                | Logical volume management (software layer)          |
| **Main Purpose** | Fast, high-performance storage for SSDs          | Manage storage volumes dynamically                  |
| **Speed**        | Very high (compared to SATA)                     | No direct impact on speed, but provides flexibility |
| **Flexibility**  | Limited to storage capacity and speed            | Highly flexible with resizing and pooling storage   |
| **Management**   | Managed directly by the OS or filesystem         | Provides abstraction and dynamic management         |
| **Use Cases**    | High-performance workloads, low-latency apps     | Dynamic storage management, snapshots, pooling      |
| **Interaction**  | Can be used as a physical storage device for LVM | Can manage NVMe storage via logical volumes         |
| **Tools**        | nvme-cli, lsblk, fdisk, etc.                     | lvcreate, vgcreate, pvcreate, etc.                  |


# FUSE

> Filesystem in Userspace

- install

  ```shell
  dnf install fuse fuse-libs
  ```

# command

## basic

```shell
# list avaliable disk
fdisk -l

# list nvme (sudo dnf install -y nvme-cli)
nvme list

# list block disk
lsblk -l
```

### Check Disk Health

```shell
dmesg | grep -i 'error'
```

### umount

- Unmount Using umount -l (Lazy Unmount)

  ```shell
  sudo umount -l <path/to/mount>
  ```

  The -l (lazy) option detaches the filesystem immediately and postpones the actual unmount until it’s no longer in use. This should allow you to unmount the device without needing to terminate the processes that are holding it.

## LVM

- 检查指定vg的可用空间

  ```shell
  sudo vgdisplay vg_nvme2n1 | grep "Free"
  ```

- delete lvm

```shell
# step 1:
lsblk
NAME                                                                                                  MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sr0                                                                                                    11:0    1 1024M  0 rom  
vda                                                                                                   252:0    0  300G  0 disk 
├─vda1                                                                                                252:1    0    6G  0 part /boot
└─vda2                                                                                                252:2    0  290G  0 part 
  └─rl_172--20--7--230-root                                                                           253:0    0  290G  0 lvm  /
vdb                                                                                                   252:16   0  100G  0 disk 
└─ceph--d79fdfae--bdd5--4ea7--a907--740181f88091-osd--block--37262a3f--354b--4d6a--95db--eb18abd939ce 253:1    0  100G  0 lvm 

# step3:
sudo vgchange -an ceph--d79fdfae--bdd5--4ea7--a907--740181f88091-osd--block--37262a3f--354b--4d6a--95db--eb18abd939ce

# step4
sudo lvchange -an /dev/ceph-d79fdfae-bdd5-4ea7-a907-740181f88091/osd-block-37262a3f-354b-4d6a-95db-eb18abd939ce

# step5
sudo vgremove ceph-d79fdfae-bdd5-4ea7-a907-740181f88091
```

- create lvm 

```shell
# step1 create vg(volume group)
sudo vgcreate s3_vg /dev/vdb

# step2 create lv(logic volume)
sudo lvcreate -L 50G -n minio_lv s3_vg

# step3 format xfs on lvm
sudo mkfs.xfs /dev/s3_vg/minio_lv

# step4 create mount point
sudo mkdir -p /mnt/minio

# step5 mount logic point
sudo mount -o noatime /dev/s3_vg/minio_lv /mnt/minio

# step6 verify
df -h /mnt/minio

Filesystem                  Size  Used Avail Use% Mounted on
/dev/mapper/s3_vg-minio_lv   50G  389M   50G   1% /mnt/minio
```

- umount lvm

```shell
# step1 umount directory
sudo umount /mnt/your_mount_point

# step2 Deactivate the Logical Volume
sudo lvchange -an /dev/volume_group/logical_volume

# step3 Remove the Logical Volume
sudo lvremove /dev/volume_group/logical_volume

# (Optional) step4 Remove the Volume Group 
sudo vgremove volume_group

# (Optional) step5 Remove the Physical Volume
sudo pvremove /dev/sdX
```

- check

```shell
# check vgs
sudo vgs
```

## partition

- check specify disk partition

  ```shell
  # method 1
  sudo parted /dev/<name> print
  
  # method 2
  sudo fdisk -l /dev/nvme2n1
  ```

  

# toubleshooting

- resolve `d?????????` status directory

  ```shell
  umount -l /path/to/mount
  # or 
  fusermount -uz /path/to/mount
  ```


- 排查指定分区占用进程

  ```shell
  # 假设 /dev/nvme2n1p1 是 /dev/nvme2n1 的子分区，在对/dev/nvme2n1p1操作的时候会提示 cannot open /dev/nvme2n1p1: Device or resource busy，这时可以排查是否是主分区是否被占用，然后kill掉相应process
  sudo lsof | grep /dev/nvme2n1
  ```

  