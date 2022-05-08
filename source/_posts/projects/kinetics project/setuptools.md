---
title: setuptools入门
tags: 
- 环境配置
- setuptools
categories:
- kinetics项目
---
# `setuptools` Quickstart

## basics

1. 声明使用setuptools 来打包项目

```
# pyproject.toml
[build-system]
requires = ["setuptools"]
build-backend = "setuptools.build_meta"
```

2. 通过配置文件setup.py来 specify 包信息

```python
from setuptools import setup

setup(
    name='mypackage',
    version='0.0.1',
    packages=['mypackage'],
    install_requires=[
        'requests',
        'importlib-metadata; python_version == "3.8"',
    ],
)
```

3. 需要一个构建器builder

```
# pip install build
python -m build
```

## extensions

1. 自动包发现

```
from setuptools import find_packages  # or find_namespace_packages

setup(
    # ...
    packages=find_packages(
        where='.',
        include=['mypackage*'],  # ["*"] by default
        exclude=['mypackage.tests'],  # empty by default
    ),
    # ...
)
```

2. 依赖管理

```
setup(
    # ...
    install_requires=["docutils", "requests <= 0.4"],
    # ...
)
```

3. 开发者模式

```
pip install --editable .
```

## experiments

> 遵循教程，实验打包流程：https://packaging.python.org/en/latest/tutorials/packaging-projects/

![image-20220414140619160](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202204141406193.png)

