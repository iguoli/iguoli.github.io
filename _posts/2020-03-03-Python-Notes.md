---
title: Python 学习笔记
date: 2020-03-03
tags: Python
key: 2020-03-03-Python-Notes
---

Python 的语法概念总结。

<!--more-->

## 模块 (Modules)

### 创建模块 (Module Creation)

一个包含 Python 代码，以 ".py" 为后辍的文件，都会自动被认为是一个 Python 模块。要导入模块，其文件名必须以 ".py" 为后辍。

在导入模块时，Python 会把内部模块名映射到外部文件名，即模块名前加上模块搜索路径，再为模块名添加 ".py" 后辍。例如：模块 `mod` 会映射为外部文件 `<PYTHONPATH>/mod.py`。

### 模块搜索路径 (The Module Search Path)

模块搜索路径包含：

1. 程序的主目录。  
   - 如果执行一个脚本，该脚本所在目录即主目录，该脚本即顶层脚本。
   - 如果是交互模式，当前工作目录 (CWD) 即为主目录。  
   - 因为主目录总是第一个被搜索，因此会覆盖其它路径中的同名模块。
2. `PYTHONPATH` 环境变量中的目录。
3. 标准链接库目录。
4. 特定位置的.pth文件中的目录。

这四部分组成了 `sys.path` 的内容。

### import vs. from 语句

像 `def` 一样，`import` 和 `from` 是可执行语句，而不是编译期的声明，因此可以嵌套在 `if` 或 `try` 等代码块中。此外，它们是隐性的赋值语句：

- `import` 将整个模块对象赋值给一个变量名。

- `from` 将一或多个变量名赋值给另一个模块中的同名对象。

以下两段代码的效果相同

```python
from module import name1, name2     # 将 module 中的 name1 和 name2 复制到当前模块的同名对象
```

```python
import module                       # 获取模块对象

name1 = module.name1                # 将模块对象的变量赋值到当前模块的同名对象
name2 = module.name2
del module                          # 删除模块对象
```

### 模块命名空间

模块就是命名空间，存在于模块内的变量名就是模块对象的属性。

- 模块语句在首次导入时执行，Python 会建立空的模块对象。
- 顶层的赋值语句会创建模块属性（顶层语句即不在 `def` 或 `class` 之内的语句）。
- 模块的命名空间可以通过模块对象的 `__dict__` 属性或 `dir()` 函数获取。
- 模块是一个独立作用域（本地变量就是全局变量）。

## 模块包 (Module Packages)

一个包含 Python 代码文件的目录就称为包。包导入是把系统中的目录变成一个 Python 命名空间。

### `__init__.py` 包文件

包导入语句中的每个目录内必须有一个 `__init__.py` 文件。Python 模块搜索路径中的容器目录不需要此文件。

`__init__.py` 文件的作用就是将目录扮演为模块文件:

- 执行初始化代码
- 生成模块命名空间
- 控制 `from *` 语句的行为。在模块文件内，可以使用 `__all__` 列表来控制 `from *` 语句导入哪些变量名。

```python
# somemodule.py or __init__.py

def spam():
    pass

def grok():
    pass

blah = 42

# 使用 from * 时，只导出 'spam' 和 'grok' 变量名
__all__ = ['spam', 'grok']
```

### 包的相对导入与绝对导入

- 绝对导入：导入仅检查 `sys.path` 搜索路径上的模块，跳过包自己目录下的模块。
- 相对导入：在 `from` 语句中使用 `前置点号(".")`，导入只检查包自己目录下的模块，不检查 `sys.path` 搜索路径上的模块。

在 Python3.X 中，包的默认导入就是绝对导入。而在 Python2.X 中，包的默念导入是先相对导入再绝对导入，即先检查包自己的目录，再检查 `sys.path` 搜索路径中的目录。

相对导入的语法:

```python
# 从当前文件所在目录导入 spam 模块
from . import spam

# 从当前文件所在目录中的 spam 模块导入 name 变量
from .spam import name
```

- 相对导入只能用于包内的模块文件。如果模块文件作为脚本直接运行，作为顶层脚本，它不再属于任何包，因此会报没有父级包的错误信息: <span style="color:red">ImportError: attempted relative import with no known parent package</span>。应该通过从包的外部执行一个顶层脚本来调用该包下的模块，或者从包的外部以模块来运行，类似于这样的代码 `python -m dir.test`。

- 相对导入只适用于 `from` 语句，不适用于 `import` 语句。