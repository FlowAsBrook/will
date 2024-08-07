---
title: java jenv
date: 2023-12-29 16:50:48
tags: jenv
categories: java
---

## Jenv

### **列出已安装的Java版本：**

运行以下命令列出已安装的Java版本：

```shell
jenv versions
```

这会显示所有已安装的Java版本。

### 5. **设置全局Java版本：**

你可以选择设置一个全局的Java版本，该版本会被默认使用。运行以下命令：

```
jenv global <jdk_version>
```

将 `<jdk_version>` 替换为你想要设置为默认的Java版本。

### 6. **设置项目特定的Java版本：**

如果你想在特定的项目中使用不同的Java版本，可以进入到项目的目录，然后运行以下命令：

```
jenv local <jdk_version>
```

这会在项目目录下创建一个 `.java-version` 文件，指定项目特定的Java版本。

### 7. **验证Java版本：**

在终端中运行以下命令验证当前系统使用的Java版本：

```shell
java -version
```

确保输出显示的是你所期望的Java版本信息。

通过以上步骤，你可以使用 `jenv` 管理多个Java版本，并且在不同的项目中切换使用不同的Java版本。

## 新增java版本

1. brew install openjdk21
2. sudo ln -sfn /opt/homebrew/opt/openjdk@21/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-21.jdk
3. 