---
title: linux file command
date: 2016-10-16 12:01:30
tags: command
categories: linux
---

# basic

## copy

```shell
rsync -aP <sourceDir> <targetDir>

cp -r <sourceDir> <targetDir>
```

## du

```shell
du -h --max-depth=1 --exclude='proc' --exclude='home' --exclude='mnt'
```

## dd

```shell
dd if=<input> of=<output> [options]
```

​	•	if: Specifies the **input file** or device (e.g., /dev/sda or /path/to/file).

​	•	of: Specifies the **output file** or device (e.g., /dev/sdb or /path/to/file).

​	•	**bs=SIZE**: Block size, defining how much data to read and write at a time. It can be set to values like 512, 4M, etc. Example: bs=1M reads and writes 1 megabyte at a time.

​	•	**count=N**: Limits the number of blocks copied. For example, count=100 copies 100 blocks of the size specified by bs.

​	•	**status=progress**: Shows real-time progress while copying.

​	•	**conv=notrunc**: Prevents truncation of the output file (useful when appending).

​	•	**conv=sync**: Pads the input to the full block size with null bytes if necessary.

```shell
# Copy specify file to specify path
sudo dd if=/path/to/image.iso of=/dev/sdX bs=4M status=progress
	•	if=/path/to/image.iso: Input is an ISO file.
	•	of=/dev/sdX: Output is the target USB device (e.g., /dev/sdb).
	•	bs=4M: Sets block size to 4MB for faster copying.
	•	status=progress: Shows copy progress.
	
# Clone a disk or partition to an image file
sudo dd if=/dev/sda of=/path/to/backup.img bs=1M status=progress
	•	This creates a raw image backup of /dev/sda (entire disk).
	
# Restore the image created in the above example
sudo dd if=/path/to/backup.img of=/dev/sda bs=1M status=progress

# Erase a Disk (Overwrite with Zeros)
sudo dd if=/dev/zero of=/dev/sdX bs=1M status=progress
	•	if=/dev/zero: Fills the disk with zeros.
	•	of=/dev/sdX: Target disk to be wiped.

# Create a File of a Specific Size
dd if=/dev/urandom of=randomfile.bin bs=1M count=1024  # Create a 1GB file of random data
	•	if=/dev/urandom: Input is random data.
	•	of=randomfile.bin: Output file.
	•	bs=1M count=1024: Creates a 1GB file (1024 blocks of 1MB each).
	
# Test Disk Write Speed
dd if=/dev/zero of=testfile bs=1M count=1024 conv=fdatasync status=progress # Measure disk performance by writing a 1GB file
	•	conv=fdatasync: Ensures data is fully written to disk before timing stops.
```



## grep

- 去除包含特定字符串的行：

```shell
grep -v "pattern" inputfile > outputfile
```

参数 `-v` 是用来反向匹配的选项，它会将不匹配指定模式的行输出

- 查找指定目录下的文件内容

```shell
grep -rn "info" *

# 指定检索目录，指定排出目录（exclude-dir可多次使用）
grep -rn "info" /home/rdx --exclude-dir="log"
```

- 查询大文件里面的内容

格式：

```shell
// 使用管道符可以实现过滤既满足时间又满足ip的行。
grep -n -e “10.198.2.133” prometheus.log |grep -e “2019-09-24”|head -n 3
```

参数解释：
-n 参数的作用是显示查找结果的所在行号
-e 参数表示我们需要搜索的关键字，多个关键字就用多个 -e 参数
prometheus.log 表示待搜索的大日志文件
head -n 3 表示显示前面查询结果的前三条记录

- 排除指定内容

要仅打印与搜索模式不匹配的行，可以使用grep的`-v`或`--invert-match`选项。进行反转的匹配。

```shell
grep -v xxx
```

- 限定查询结果之后的前几行

   `grep -m 10 <pattern> <file> `

- 限定查询结果倒数的几行

   `grep <pattern> <file> | tail -10`

## delete

**1.** To delete all files in a directory except filename, type the command below:

```shell
rm -v !("filename")
```

**2.** To delete all files with the exception of **filename1** and **filename2**:

```shell
rm -v !("filename1"|"filename2") 
```

**3.** The example below shows how to remove all files other than all `.zip` files interactively:

```shell
rm -i !(*.zip)
```

**4.** Next, you can delete all files in a directory apart from all `.zip` and `.odt` files as follows, while displaying what is being done:

```shell
rm -v !(*.zip|*.odt)
```

**5.** 删除指定目录下指定日期的目录，可以使用 `find` 和 `rm` 命令来删除指定目录下指定日期的目录

```shell
find /path/to/directory -type d -mtime +365 -exec rm -rf {} \;
```

**6**. 删除指定目录下前一个星期的文件，可以使用 `find` 和 `rm` 命令来删除指定目录下指定日期的文件

```shell
find /path/to/directory -type f -mtime +7 -exec rm {} \;
```

or

```shell
find /path/to/directory -type f -mtime +7 -delete
```

- 可指定相关名称

  ```shell
  find /var/log -name "*.log" -type f -mtime +30 
  ```

  

## AWK

- `awk -v FS="输入分隔符" -v OFS='输出分隔符' '{if($1==$5) print $1,$5,$10}' filename`
  
  查找filename文件（文件中列的分隔符为“输入分隔符”）中，每一行第一列和第五列相等的行，并输出第一列、第五列、第十列，切输出字段分隔符为“输出分隔符”。如果不配置FS和OFS，那么输入输出分隔符均默认为空

- [exclude a column with awk](https://www.commandlinefu.com/commands/view/6872/exclude-a-column-with-awk), 比如打印除第5列的其它所有列
  
  awk '{ $5=""; print }' file

## wc

- 语法

```shell
语法：wc [选项] 文件…
  - c 统计字节数。
  - l 统计行数。
  - w 统计字数。
```

## chmod

使文件可以直接执行的命令：chmod +x filename

使所有用户对目录都有读写权限：sudo chmod ugo+rw /opt

```
r=4，w=2，x=1
若要rwx属性则4+2+1=7；
若要rw-属性则4+2=6；
若要r-x属性则4+1=7
```

## find

- 查找具体文件    

```shell
find / -name 文件名称
```

- 查找指定用户的文件

```shell
find ./* -user 用户名
```

- 查找指定用户组的文件

```shell
find ./* -group 用户组
```

- 匹配查找除了某个特定文件类型以外的所有文件，并将结果传递给 `rm` 命令进行删除

  ```shell
  find . ! -name "*.txt" -delete
  ```

- 匹配多个

  ```shell
  find . ! \( -name "log4j*" -o -name "flink*" \)
  ```


- 指定天数数据

  ```shell
  # 查找30天之前的文件
  find <directory> -type f -name "*.tar.gz" -mtime +30
  
  # 查找30天之内的文件
  find <directory> -type f -name "*.tar.gz" -mtime -30
  ```

  

## ls

`ls -lh`以可读性G、M查看文件的大小

## SED

- 替换字符
  
  linux环境：
  
  ```shell
  sed -i 's/Search_String/Replacement_String/g' Input_File
  ```
  
  mac环境（需要设置备份，以防文件损坏）
  
  ```shell
  sed -i .bak 's/Search_String/Replacement_String/g' Input_File
  ```

- 删除指定多行

  ```shell
  sed -i '1,5d' example.txt
  ```

  

## tar

- c – Creates a new .tar archive file.

- x — to untar or extract a tar file

- v – Verbosely show the .tar file progress.

- f – File name type of the archive file.

- z — gzip archive file

- j —  bz2 feature compress and create archive file

- t — to list the contents of tar archive file

```shell
# extract files to a specific destination directory
tar -xvf <archive.tar> -C <destination_directory>
```

## wget

- 下载指定目录

```bash
wget -r --no-parent http://abc.tamu.edu/projects/tzivi/repository/revisions/2/raw/tzivi/
```

## link

- 创建软连接

  ```shell
  ln  -s  [源文件或目录]  [目标文件或目录]
  ```

- 查找指定目录的软连接文件

  ```shell
  ls -alR | grep ^l
  ```

- relink

  ```shell
  ln -sf <new_target> <link_name>
  # -s: Create a symbolic link.
  # -f: Force overwriting an existing link.
  ```

## sort

```
sort --parallel=8 -S 4G -T /data -k2,3 largefile.txt > sorted_file.txt
```

> 使用了8个线程并行排序，并且sort命令在排序过程中最多使用4GB的内存缓冲区。我们还使用了`-T /data`选项，指定sort命令使用/data目录来存储临时文件，而不是默认路径。
>
> “-k1,2”表示先按照第1列排序，若第1列相同则按照第2列排序。

## base64

- 解码

  `echo [base64-encoded-string] | base64 --decode`

- 编码

  `echo "your string" | base64`

## tr

tr -- translate or delete characters 

- 大小写转换

  ```shell
  cat file | tr A-Z a-z 
  cat file | tr a-z A-Z
  ```

# senario

## 创建文件

```shell
# method 2
fallocate -l 1G fileName
```

### dd

```shell
dd if=/dev/zero of=/path/to/directory/filename bs=block_size count=number_of_blocks
# Create a 100MB File in /tmp/
dd if=/dev/zero of=/tmp/testfile.img bs=1M count=100
```

- **`if=/dev/zero`**: Input file (`/dev/zero` provides null bytes; use `/dev/urandom` for random data).
- **`of=/path/to/directory/filename`**: Output file path and name.
- **`bs`**: Block size (e.g., `1K`, `1M`, `1G`).
- **`count`**: Number of blocks to write

## 文件分割

```shell
split [-a] [-b] [-C] [-l] [要分割的文件名] [分割后的文件名前缀]
–version 显示版本信息
– 或者-l,指定每多少行切割一次，用于文本文件分割
-b 指定切割文件大小,单位 m 或 k
-C 与-b类似，但尽量维持每行完整性
-d 使用数字而不是字母作为后缀名
-a 指定后缀名的长度，默认为2位
```

将多个分割的文件进行合并

```shell
cat files_name_1 files_name_2 files_name_3 > files_name
```

- 按行数分割

  ```
  split -l 10000 bigfile.txt smallfile
  ```

  > 分割之后的文件不影响读取

- 统计某个文件中的字符数，需要注意的是，如果文件中包含多字节字符（如中文），则每个字符将被视为多个字符来计算。

  ```shell
  wc -c /path/to/file
  ```

  在这基础上，统计内容所占KB

  ```shell
  wc -c /path/to/file | awk '{print $1/1024}'
  ```

- awk对文件按照指定多列的内容进行排序

  ```
  awk '{print $0}' head_100.csv | sort -t ',' -k2,3 > head_100_sort.csv
  ```

  > 并用`sort`命令根据指定列的内容进行排序。`-t`选项表示使用制表符作为字段分隔符，`[列数]`是你要排序的那一列，“-k1,2”表示先按照第1列排序，若第1列相同则按照第2列排序。

- 统计字符的长度

  ```shell
  echo 字符 | wc -m
  ```


## 批量替换文件名

```bash
rename -n -e 's/待替换字符串/替换字符串/'  *.png
```

## 转换文件编码格式

- 查看编码

  ```shell
  # vim
  :set fileencoding
  
  # file
  file -i filename
  ```

  

- 转换编码

然后使用 `iconv` 进行编码格式的转换，比如将一个 utf-8 编码的文件转换成 GBK 编码，命令如下：

```shell
$ iconv -f UTF-8 -t GBK input.file -o output.file
```

- 如果遇到]iconv: 未知xxxx处的非法输入序列,一种解决方法是加入 -c选项：忽略无效字符

  ```shell
  iconv -c  -f gb2312 -t utf8 test.txt -o output.file
  ```

```
iconv -f gb18030 -t UTF-8 input.file -o output.file

gb18030
```

## 格式化json

```shell
echo '{"kind": "Service", "apiVersion": "v1", "status": {"loadBalancer": true}}'|jq .
```

## 加密

用zip命令对文件加密压缩和解压

```shell
zip -re filename.zip filename 
回车，输入2次密码
```

## markdown转word

```shell
pandoc -o output.docx -f markdown -t docx filename.md
```

## 清理指定日期之前的文件

