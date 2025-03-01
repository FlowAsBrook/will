---
title: python poetry
date: 2023-11-03 02:00:28
tags: lib
categories: python
---

## POETRY 

> 每个使用 Poetry 的项目都会有自己的虚拟环境，这样可以避免不同项目之间的依赖冲突。因此，每个项目的依赖项都会独立存放在各自的虚拟环境中。

- 查看安装依赖目录

  ```shell
  poetry env info --path
  ```

- 列出所有虚拟环境

  ```shell
  poetry env list
  ```

- 删除指定虚拟环境

  ```shell
  poetry env remove <虚拟环境名称>
  ```

- 进入 Poetry 创建的虚拟环境

  ```
  poetry shell
  ```


- 锁定并安装项目依赖项的确切版本

  ```shell
  poetry lock
  poetry install
  ```


- 安装test相关依赖

  ```toml
  [tool.poetry.group.test.dependencies]
  pytest = "^7.3.0"
  ...
  ```

  

  ```shell
   poetry install --with test
  ```

  


- 通过`requirement`添加依赖

  ```shell
  poetry add $( cat requirements.txt )
  ```

  

- build编译

  如果使用`build backend`非默认的`poetry core`，则直接使用其他编译工具命令执行编译



一个典型的使用流程如下:

1. 在一个新项目中,首先执行 `poetry install` 安装依赖并生成 `poetry.lock` 文件。
2. 在开发过程中,如果需要新增或更新依赖,先执行 `poetry add/remove 依赖名`。
3. 然后执行 `poetry lock` 更新 `poetry.lock` 文件。
4. 再执行 `poetry install` 安装新的依赖。
5. 开发或运行代码时,执行 `poetry shell` 进入虚拟环境。
6. 在虚拟环境中进行开发、测试和运行。



## HATCH

### 准备

- 新建项目

  `hatch new "Hatch Demo"`

- 已有项目初始化

  `hatch new --init`

- 虚拟环境

  ```shell
  python -m venv venv
  source venv/bin/activate
  ```

- 创建hatch虚拟环境

  ```shell
  hatch env create
  ```
  
- pycharm配置

  虚拟环境需配置成 `hatch shell`提示的目录

- 更新依赖

  - 运行 `hatch shell`命令

- 查看环境信息

  ```shell
  hatch env show --ascii
  ```


- 查看当前项目环境信息

  ```shell
  hatch env find
  ```

- 退出当前环境

  ```shell
  exit
  ```


- 清理环境

  ```shell
  hatch env prune <envName>
  ```


- 格式化

  ```shell
  hatch run lint:fmt
  ```


### 编译

- 方式一：

  ```shell
  python -m pip install --upgrade build
  python3 -m build
  # 一旦完成应该在 dist 目录下产生两个文件：
  ```

  方式二：

  ```shell
  hatch build
  ```


### 代码格式化

- 配置

```toml
[tool.hatch.envs.lint.scripts]
typing = "mypy --install-types --non-interactive {args:src/jarvex_auth tests}"
style = [
  "ruff {args:.}",
  "black --check --diff {args:.}",
]
fmt = [
  "black {args:.}",
  "ruff --fix {args:.}",
  "style",
]
all = [
  "style",
  "typing",
]
```

- 执行命令`hatch run lint:fmt`



## Flit	

### 安装依赖

```shell
flit install
```



---

**Reference**

- [THE BASICS OF PYTHON PACKAGING IN EARLY](https://drivendata.co/blog/python-packaging-2023)

- https://zhuanlan.zhihu.com/p/666166082

  