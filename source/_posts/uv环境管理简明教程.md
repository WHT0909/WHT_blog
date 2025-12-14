---
title: uv环境管理简明教程
date: 2025-12-14 20:51:45
tags:
    - 环境管理
categories:
    - 其他计算机技术
---

总结 uv 的使用方法和常用指令
<!-- more -->

<div class="catalog">
    <h3>目录</h3>
    <a href="#chapter1">1. 初始化项目</a><br>
    <a href="#chapter2">2. 创建虚拟环境并运行</a><br>
    <a href="#chapter3">3. 复现 uv 环境</a><br>
</div><br><br>

uv 是一款基于 Rust 编写的 Python 环境管理工具，本文将总结 uv 的使用方法和常用指令。uv 在 Windows 和 Linux / MacOS 系统下的安装详见笔记"Python创建虚拟环境"。本文的所有示例程序均在 Windows Subsystem for Linux 2（WSL 2）的 Ubuntu 20.04 发行版环境中运行。<br>

<h2 id="chapter1">1. 初始化项目</h2>

在项目文件夹中运行`uv init`指令即可完成初始化，运行后的项目结构如下：

```bash
.
├── .git
├── .gitignore # 版本管理
├── .python-version # python 版本说明
├── README.md # 说明文档
├── main.py # 主程序入口
└── pyproject.toml # 配置文件信息
```

其中 pyproject.toml 内容如下：

```bash
[project]
name = "uvtest" # 项目名称
version = "0.1.0" # 项目版本号
description = "Add your description here" # 项目介绍
readme = "README.md" # 说明文档
requires-python = ">=3.9" # python 版本要求
dependencies = [] # 依赖
```

<h2 id="chapter2">2. 创建虚拟环境并运行</h2>

通过`uv venv`指令即可创建环境。根据提示，可通过`source .venv/bin/activate`激活环境。实际上 uv 会首先查找当前项目下有无 .venv 文件夹，无需手动激活也能够正常使用。

```bash
(base) root@XiaoHei:/mnt/d/UvTest# uv venv
Using CPython 3.9.12 interpreter at: /root/anaconda3/bin/python3
Creating virtual environment at: .venv
Activate with: source .venv/bin/activate
```

使用`uv run main.py`运行程序，运行时所有依赖的版本会自动更新

通过`uv add pandas numpy`添加依赖

```bash
(UvTest) (base) root@XiaoHei:/mnt/d/UvTest# uv add numpy --default-index https://pypi.tuna.tsinghua.edu.cn/simple
Resolved 4 packages in 782ms                                                                                                          
Audited 1 package in 4ms 
```

在实际下载的过程中，和 pip 一样，如果不使用国内的镜像源直接下载，uv 将从中央仓库拉取依赖，下载速度非常之慢。配置国内源的方法有两种，一种是直接在指令后加参数（如上面的示例代码）：

```bash
uv add numpy --default-index https://pypi.tuna.tsinghua.edu.cn/simple
```

另一种是永久性的配置镜像源：

```bash
# 清华源
echo 'export UV_DEFAULT_INDEX="https://pypi.tuna.tsinghua.edu.cn/simple"'>> ~/.bashrc
# 令配置立刻生效
source ~/.bashrc
```

依赖下载完成后，可以发现配置文件 pyproject.toml 发生了变化：

```bash
[project]
name = "uvtest"
version = "0.1.0"
description = "Add your description here"
readme = "README.md"
requires-python = ">=3.9"
dependencies = [
    "numpy>=2.0.2",
    "pandas>=2.3.3",
]

[[tool.uv.index]]
url = "https://pypi.tuna.tsinghua.edu.cn/simple"
default = true
```

还可以安装特定版本的库，如`uv add matplotlib==3.9.4`

移除库时，使用指令`uv remove matplotlib`

注意：在使用 uv 时，应尽可能避免使用 pip，以免造成环境污染

通过 uv.lock 文件可严格锁定依赖的版本，避免版本冲突

通过`uv tree`可以清晰地看到项目的依赖关系树：

```bash
(base) root@XiaoHei:/mnt/d/UvTest# uv tree
Resolved 24 packages in 9ms
uvtest v0.1.0
├── matplotlib v3.9.4
│   ├── contourpy v1.3.0
│   │   └── numpy v2.0.2
│   ├── cycler v0.12.1
│   ├── fonttools v4.60.2
│   ├── importlib-resources v6.5.2
│   │   └── zipp v3.23.0
│   ├── kiwisolver v1.4.7
│   ├── numpy v2.0.2
│   ├── packaging v25.0
│   ├── pillow v11.3.0
│   ├── pyparsing v3.2.5
│   └── python-dateutil v2.9.0.post0
│       └── six v1.17.0
├── numpy v2.0.2
└── pandas v2.3.3
    ├── numpy v2.0.2
    ├── python-dateutil v2.9.0.post0 (*)
    ├── pytz v2025.2
    └── tzdata v2025.3
(*) Package tree already displayed
```

<h2 id="chapter3">3. 复现 uv 环境</h2>

首先移除现有的虚拟环境：`rm -rf .venv`

复现环境时必须有 pyproject.toml 和 uv.lock 两个文件

只需`uv sync`即可实现复现

此外，还可以通过`uv add -r requirements`添加依赖

<br><br>
本文参考了以下资源：

- <a href="https://www.bilibili.com/video/BV15MVdzaEUw/?spm_id_from=333.337.search-card.all.click&vd_source=0ea0c7956df75b2935422822b2001158">bilibili：全面掌握UV：Python下一代环境管理懒人工具（Python五分钟）</a>
- <a href="https://zhuanlan.zhihu.com/p/1930714592423703026">知乎：别再忍了！uv 下载慢如龟速？一招配置国内镜像，让你的 Python 体验坐上火箭！</a>