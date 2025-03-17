---
title: python snippet
date: 2023-11-14 15:28:51
tags: snippet
categories: python
---

# 虚拟环境

项目粒度：python -m venv venv
        			poetry
系统粒度：pyenv
        			conda

# 指令

- 安装tar.gz，解压之后，执行

  ```python
  python3 setup.py install
  ```

## pip

- 国内镜像

  ```
  pip config set global.index-url https://pypi.mirrors.ustc.edu.cn/simple
  pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
  ```

- 安装子模块

  ```shell
  pip install 'fastapi_amis_admin[cli]'  # 注意引号
  ```

- 查看指定依赖版本

  ```python
  pip show package_name
  ```


- 把项目的依赖放入指定文件

  ```python
  pip freeze > requirements.txt
  ```

- 清除缓存

  ```shell
  python -m pip cache purge
  ```

# Buildins

- 装饰器 `staticmethod` 用于定义与类或实例无关的函数，而 `classmethod` 用于定义需要访问类或类变量的函数。

# profiler

## py-spy

- record

```shell
sudo py-spy record -o tableagent.svg --pid 37152
```

# 信创

- 安装python3-devel

  ```shell
  dnf install python3-devel
  ```

# 规范

- **请选择四个空格的缩进，不要使用 Tab，更不要 Tab 和空格混着用。**
- **全局的类和函数的上方需要空两个空行，而类的函数之间需要空一个空行**
- 代码的尾部，每个代码文件的最后一行为空行，并且只有这一个空行。
- 可以使用`#`进行单独注释，请记得要在`#`后、注释前加一个空格。

- 对于操作符，例如`+`，`-`，`*`，`/`，`&`，`|`，`=`，`==`，`!=`，请在两边都保留空格。不过与此对应，括号内的两端并不需要空格。
- 采用 `from module import func` 这样的语句，请确保 func 在本文件中不会出现命名冲突。不过，你其实可以通过 `from module import func as new_func` 来进行重命名，从而避免冲突。
- 如果是类的私有变量，请记得前面增加两个下划线。
- 对于常量，最好的做法是全部大写，并通过下划线连接
- 对于函数名，同样也请使用小写的方式，通过下划线连接起来，例如：`launch_nuclear_missile()`、`check_input_validation()`。

- 对于类名，则应该首字母大写，然后合并起来，例如：`class SpatialDropout2D()`、`class FeatureSet()`。

# Principle

## SOLID

- S *（单一职责原则 Single responsibility principle）* 

  - **一个类应该仅仅只有一个被修改的理由。**

    

- O *（开放-关闭原则 Open–closed principle）*

  - **类应该对扩展开放，对修改封闭。**

  

- L *（里氏替换原则 Liskov Substitution Principle）*

  - **子类应该可以随意替换它的父类 ，而不破坏程序 本身的功能**

  

- I *（接口隔离原则 Interface Segregation Principles）*

  - **客户（client）应该不依赖于它不使用的方法**

    

- D *（依赖倒置原则 Dependency Inversion Principle）*
  - **高层模块不应该依赖于低层模块，二者都应该依赖于抽象。**

## idea

- 好代码在处理边界情况时应该是简洁的、“润物细无声”的



## code practise

#### 边界情况（Edge cases）

- bad style: 
  - LBYL（Look Before You Leap）
- better style:
  - EAFP（Easier to Ask for Forgiveness than Permission）
- best style
  - 在日常编码时，我们应该尽量避免去手动校验数据。而是应该使用*（或者自己实现）*合适的第三方校验模块，把这部分边界处理工作抽象出去，简化主流程代码。

# Method

### 统计运行时间

```python
# 毫秒值
start_time = time.perf_counter()
xxx
cost_time = int((time.perf_counter() - start_time)* 1000)
```

### `__call__函数`

`__call__` 方法是 Python 中的一个特殊方法（也称为魔术方法），用于将一个对象作为函数调用。当一个对象被作为函数调用时，Python 解释器会自动调用该对象的 `__call__` 方法。

在类中定义 `__call__` 方法可以使对象具有可调用的行为，就像调用函数一样。这样的对象被称为可调用对象。通过实现 `__call__` 方法，我们可以在对象被调用时执行特定的逻辑。

以下是一个示例，展示了如何定义和使用 `__call__` 方法：

```python
class CallableClass:
    def __call__(self, *args, **kwargs):
        print("Object is being called")

obj = CallableClass()
obj()  # 等同于调用 obj.__call__()
```

在上面的示例中，我们定义了一个名为 `CallableClass` 的类，并在其中实现了 `__call__` 方法。当创建 `CallableClass` 的实例 `obj` 并将其作为函数调用时，`__call__` 方法会被自动调用，并打印出 "Object is being called"。

需要注意的是，`__call__` 方法可以带有参数，就像普通的函数一样。我们可以在 `__call__` 方法中定义任意的逻辑，可以访问实例的属性、调用其他方法等。

使用 `__call__` 方法可以使对象具有类似函数的行为，这在某些情况下非常有用，例如实现可调用的类或实现函数式编程的概念。

### `**`使用

- `**` 运算符用于解包字典或关键字参数。当应用于字典时，`**` 运算符可以将字典中的键值对解包为一系列关键字参数传递给函数或方法。

  ```python
  def print_person_info(name, age, city):
      print(f"Name: {name}")
      print(f"Age: {age}")
      print(f"City: {city}")
  
  person = {
      'name': 'Alice',
      'age': 25,
      'city': 'New York'
  }
  
  print_person_info(**person)
  ```

  

### 布尔真假判断

虽然所有用户类实例的布尔值都是真。但是 Python 提供了改变这个行为的办法：**自定义类的 `__bool__` 魔法方法** *（在 Python 2.X 版本中为 `__nonzero__`）*。当类定义了 `__bool__` 方法后，它的返回值将会被当作类实例的布尔值。

另外，`__bool__` 不是影响实例布尔真假的唯一方法。如果类没有定义 `__bool__` 方法，Python 还会尝试调用 `__len__` 方法*（也就是对任何序列对象调用 `len` 函数）*，通过结果是否为 `0` 判断实例真假。

### 在条件判断中使用 all() / any()

`all()` 和 `any()` 两个函数非常适合在条件判断中使用。这两个函数接受一个可迭代对象，返回一个布尔值，其中：

- `all(seq)`：仅当 `seq` 中所有对象都为布尔真时返回 `True`，否则返回 `False`
- `any(seq)`：只要 `seq` 中任何一个对象为布尔真就返回 `True`，否则返回 `False`

### 使用 partial 构造新函数

```python
def multiply(x, y):
    return x * y
def double(value):
    # 返回另一个函数调用结果
    return multiply(2, value)
```

可写成

```python
import functools

double = functools.partial(multiply, 2)
```

### Mixin

> Mixin 是面向对象编程中的一个术语，它表示一种设计模式，用于将一组功能或行为以模块化的方式添加到类中，而不需要使用继承的方式。

```python
class SendMailMixin:
    def send_mail(self, email):
        print(f"Sending mail to {email}")

class User:
    def __init__(self, email):
        self.email = email

class AdminUser(User, SendMailMixin):
    def send_admin_mail(self):
        self.send_mail(self.email)

admin = AdminUser("admin@example.com")
admin.send_admin_mail()  # 输出: Sending mail to admin@example.com
```



**Mixin和abc.ABC的区别:**

1. **用途**: Mixin侧重于代码复用和功能扩展,而abc.ABC则用于定义抽象类和接口。
2. **继承关系**: Mixin通常与其他类一起使用多重继承,而abc.ABC作为基类被具体类继承。
3. **抽象程度**: Mixin可以包含具体的实现,而abc.ABC中的抽象方法必须由子类实现。
4. **类型检查**: abc.ABC可用于类型检查,而Mixin无法直接进行类型检查。

### LOOP

```python
for i, name in enumerate(names):
    print(i, name)
```

- 使用 product 扁平化多层嵌套循环
- 使用 islice 实现循环内隔行处理
- 使用 takewhile 替代 break 语句
- 使用生成器编写自己的修饰函数

### 修改外层变量时记得使用 nonlocal

- 闭包的参数使用

### 类对象

- `cls`

```python
@classmethod
def _send_request(cls, method, endpoint, json=None, params=None):
```



### 使用 dataclass 简化代码

总结一下,dataclass主要提供了以下几方面的功能:

1. **自动生成构造函数和基本方法**
    - 自动生成`__init__`构造函数
    - 自动生成`__repr__`、`__eq__`等基本方法

2. **数据字段管理**
    - 定义数据字段
    - 支持为字段设置默认值
    - 支持字段类型注解
    - 支持只读字段、后期初始化字段

3. **数据类行为定制**
    - 可自定义字段排序
    - 可自定义字段命名规则(snake_case/CamelCase)
    - 支持自定义`__post_init__`方法做数据校验
    - 可控制是否生成`__repr__`方法
    - 支持生成`__hash__`方法用于哈希

4. **性能优化**
    - 支持使用`__slots__`优化内存使用

5. **与静态类型检查工具结合**
    - 支持与mypy等类型检查工具结合使用

6. **继承和嵌套**
    - 支持数据类的继承
    - 支持嵌套数据类作为字段

通过这些功能,dataclass可以极大简化数据类的定义和使用,减少样板代码。它使得创建数据类变得高效和优雅,同时还提供了足够的定制空间。无论是简单的数据持有对象还是复杂的数据结构,dataclass都能发挥它的优势,提高代码的可读性和可维护性。

# Package

### 文件处理 

- pathlib

### json

json模块主要有四个比较重要的函数，分别是：

- `dump` - 将Python对象按照JSON格式序列化到文件中
- `dumps` - 将Python对象处理成JSON格式的字符串
- `load` - 将文件中的JSON数据反序列化成对象
- `loads` - 将字符串的内容反序列化成Python对象

# Grammar

- `*args` 接收并处理所有传递进来的位置参数，`**kwargs` 接收并处理所有传递进来的关键字参数。

## struture

- 在Python中，基本数据类型（如整数、布尔值）是不可变的，这意味着当你将一个基本数据类型的变量传递给一个函数或方法时，你实际上是传递了它的值的副本，而不是它本身。

  因此，如果你在一个方法内部修改了一个基本数据类型的成员变量，这个修改不会影响到任何其他方法中对该成员变量的引用。
