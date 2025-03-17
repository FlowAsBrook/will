---
title: conda
date: 2025-03-14 12:22:53
tags: tool
categories: conda
---

# install

```shell
# download binary
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm ~/miniconda3/miniconda.sh

# active
source ~/miniconda3/bin/activate
conda init --all
```

# command

- 查看环境

  ```
  conda env list
  ```

- 新建环境

  ```
  conda create --name your_env_name
  ```

  指定python版本

  ```
    conda create --name xxx python=3.10
  ```

- 激活环境

  ```
  conda activate xxx
  ```

- 删除环境

  ```
  conda remove --name xxx --all
  ```

