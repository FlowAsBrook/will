---
title: python env
date: 2023-12-18 14:10:58
tags: env
categories: python
---

# conda

> conda相当于虚拟机，在里面安装python和依赖

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


# pyenv

```shell
# 查看当前版本
pyenv version

# 查看所有版本
pyenv versions

# 查看所有可安装的版本
pyenv install --list

# 安装指定版本
pyenv install 3.6.5
# 安装新版本后rehash一下
pyenv rehash

# 删除指定版本
pyenv uninstall 3.5.2

# 指定版本
pyenv shell <version> -- select just for current shell session
pyenv local <version> -- automatically select whenever you are in the current directory (or its subdirectories)
pyenv global <version> -- select globally for your user account

# 指定多个全局版本, 3版本优先
pyenv global 3.6.5 2.7.14

# 实际上当你切换版本后, 相应的pip和包仓库都是会自动切换过去的
# 当前项目使用的pyenv版本会在 `.python-version`文件中体现
```

- reference

  - [specifying-your-python-version](https://realpython.com/intro-to-pyenv/#specifying-your-python-version)

  

# venv

在 Linux 中配置 Python 项目的运行虚拟环境通常使用 `venv`（虚拟环境）模块。以下是一般的步骤：

1. **安装 venv 模块（如果尚未安装）：**

   检查你的 Python 版本并确保安装了 `venv` 模块。

2. **创建虚拟环境：**

   在项目目录中，运行以下命令创建一个名为 `myenv` 的虚拟环境：

   ```shell
   python3 -m venv <envName>  # default always use venv as env name 
   
   # Use the desired Python version:
   python3.9 -m venv myenv
   ```

   这将在当前目录创建一个名为 `myenv` 的虚拟环境文件夹。

3. **激活虚拟环境：**

   激活虚拟环境可通过以下命令：

   ```
   source myenv/bin/activate
   ```

   这将激活名为 `myenv` 的虚拟环境。激活后，你的终端提示符可能会显示虚拟环境的名称。

4. **安装项目依赖：**

   在激活的虚拟环境中，使用 `pip` 安装项目所需的依赖：

   ```
   pip install -r requirements.txt
   ```

   其中 `requirements.txt` 是包含项目依赖项的文件。

5. **退出虚拟环境：**

   当你完成项目工作后，可以通过以下方式退出虚拟环境：

   ```
   deactivate
   ```

   这将使虚拟环境退出，并恢复到系统默认环境。

使用虚拟环境有助于隔离项目的依赖，避免与系统其他项目的依赖冲突，并提供更干净的开发环境。

**uvicorn**

> 正常情况下虚拟环境 /path/to/venv/bin/ 下面是有 uvicorn 的启动命令的，即 /xxx/venv/bin/uvicorn

如果当前虚拟环境不识别`uvicorn`，可通过

- 方式一

```shell
python -m uvicorn xxx:app --reload
```

- 方式二

```
pip install --force-reinstall uvicorn
```

