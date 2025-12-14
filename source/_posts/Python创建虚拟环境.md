---
title: Python创建虚拟环境
date: 2025-12-14 15:45:35
tags:
    - 环境管理
categories:
    - 其他计算机技术
---

整理了 Python 创建虚拟环境的几种方法
<!-- more -->

<div class="catalog">
    <h3>目录</h3>
    <a href="#chapter1">1. 使用 venv 创建</a><br>
    <a href="#chapter1.1" style="margin-left: 2em;">1.1 venv 的安装和配置</a><br>
    <a href="#chapter2">2. 使用 uv 创建</a><br>
    <a href="#chapter2.1" style="margin-left: 2em;">2.1 uv 的安装和配置</a><br>
    <a href="#chapter2.2" style="margin-left: 2em;">2.2 uv 管理 python 版本</a><br>
    <a href="#chapter2.3" style="margin-left: 2em;">2.3 uv 管理虚拟环境</a><br>
    <a href="#chapter3">3. anaconda 环境管理</a><br>
</div><br><br>

虚拟环境可以将不同项目所需的库隔离开，使其互不影响。本文将介绍几种创建虚拟环境的方法，所有的示例程序均在 Windows Subsystem for Linux 2（WSL 2）的 Ubuntu 20.04 发行版环境中运行。<br>

<h2 id="chapter1">1. 使用 venv 创建</h2>

<h3 id="chapter1.1">1.1 venv 的安装和配置</h3>
基本语法：

```bash
python -m venv 环境名称
```

环境名称通常命名为.venv。示例代码如下：

```bash
# 进入项目目录
mkdir my_project && cd my_project

# 创建虚拟环境（命名为'.venv'是常见约定）
python3 -m venv .venv
```

激活虚拟环境：

```bash
# Windows 系统
.venv\Scripts\activate

# Linux / MacOS 系统
source .venv/bin/activate
```

激活后，命令行会显示环境名称：`(.venv) $`

退出虚拟环境时，使用指令：`deactivate`即可

整个过程示例如下：

```bash
(base) root@XiaoHei:/home/wanghaotian/TestCode# python -m venv .venv
(base) root@XiaoHei:/home/wanghaotian/TestCode# source .venv/bin/activate
(.venv) (base) root@XiaoHei:/home/wanghaotian/TestCode# pip install pandas==1.3.3 requests==2.26.0 Django==3.2.12
# 安装过程略
Successfully installed Django-3.2.12 asgiref-3.11.0 certifi-2025.11.12 charset-normalizer-2.0.12 idna-3.11 numpy-2.0.2 pandas-1.3.3 python-dateutil-2.9.0.post0 pytz-2025.2 requests-2.26.0 six-1.17.0 sqlparse-0.5.4 typing_extensions-4.15.0 urllib3-1.26.20
(.venv) (base) root@XiaoHei:/home/wanghaotian/TestCode# pip list
Package            Version
------------------ -----------
asgiref            3.11.0
certifi            2025.11.12
charset-normalizer 2.0.12
Django             3.2.12
idna               3.11
numpy              2.0.2
pandas             1.3.3
pip                22.0.4
python-dateutil    2.9.0.post0
pytz               2025.2
requests           2.26.0
setuptools         58.1.0
six                1.17.0
sqlparse           0.5.4
typing_extensions  4.15.0
urllib3            1.26.20
WARNING: You are using pip version 22.0.4; however, version 25.3 is available.
You should consider upgrading via the '/home/wanghaotian/TestCode/.venv/bin/python -m pip install --upgrade pip' command.
(.venv) (base) root@XiaoHei:/home/wanghaotian/TestCode# deactivate
(base) root@XiaoHei:/home/wanghaotian/TestCode# pip show Django
WARNING: Package(s) not found: Django # 没有找到包，可以看出虚拟环境实现了库的有效隔离
```

该部分参考了以下资源：

- <a href="https://www.runoob.com/python3/python-venv.html">菜鸟教程：Python 虚拟环境的创建（venv）</a>

<h2 id="chapter2">2. 使用 uv 创建</h2>

<h3 id="chapter2.1">2.1 uv 的安装和配置</h3>
uv 是一款由 Rust 编写的 Python 包管理器，相比于传统的 venv 更加高效快捷

uv 的安装：

```bash
# Linux / MacOS
curl -LsSf https://astral.sh/uv/install.sh | sh

# Windows
 powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

安装过程比较慢，需要耐心等待

```bash
# Windows
PS C:\Windows\system32> powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
Downloading uv 0.9.17 (x86_64-pc-windows-msvc)
Installing to C:\Users\15806\.local\bin
  uv.exe
  uvx.exe
  uvw.exe
everything's installed!

To add C:\Users\15806\.local\bin to your PATH, either restart your shell or run:

    set Path=C:\Users\15806\.local\bin;%Path%   (cmd)
    $env:Path = "C:\Users\15806\.local\bin;$env:Path"   (powershell)
```

```bash
# Linux
(base) root@XiaoHei:/home/wanghaotian/TestCode# curl -LsSf https://astral.sh/uv/install.sh | sh
downloading uv 0.9.17 x86_64-unknown-linux-gnu
no checksums to verify
installing to /root/.local/bin
  uv
  uvx
everything's installed!

To add $HOME/.local/bin to your PATH, either restart your shell or run:

    source $HOME/.local/bin/env (sh, bash, zsh)
    source $HOME/.local/bin/env.fish (fish)
```

安装完成后，需要将 uv 配置到环境变量中去，并使用`uv --version`验证

```bash
# 1. 编辑 root 用户的 bash 配置文件
vim ~/.bashrc

# 2. 在文件末尾添加以下内容：
export PATH="$HOME/.local/bin:$PATH"

# 3. 刷新配置
source ~/.bashrc

# 4. 检查，出现类似于"uv 0.9.17"的提示即成功
uv --version
```

<h3 id="chapter2.2">2.2 uv 管理 python 版本</h3>

查看可用 python版本：`uv python list`

安装特定 python 版本：`uv python install 3.12`

设置全局默认 python 版本：`uv python default 3.12`

<h3 id="chapter2.3">2.3 uv 管理虚拟环境</h3>

```bash
# uv 创建名为 .venv 虚拟环境
uv venv

# 在 Windows 下激活环境
.venv\Scripts\activate

# 在 Linux 下激活环境
source .venv/bin/activate

# 为当前项目固定 Python 3.11 版本
uv python pin 3.11

# 安装某个特定版本的库
uv pip install requests==2.31.0
```

该部分参考了以下资源：

- <a href="https://uv.doczh.com/getting-started/installation/"> uv 中文文档</a>
- <a href="https://www.runoob.com/python3/uv-tutorial.html"> 菜鸟教程：uv 入门教程 -- Python 包与环境管理工具</a>

<h2 id="chapter3">3. anaconda 环境管理</h2>

anaconda 是最常用的环境管理工具之一，网络上有很多下载 anaconda 的教程，笔者在此不再过多赘述，仅给出几个常用的指令：

```bash
# 创建环境
conda create --name myenv python=3.8 

# 激活环境
conda activate myenv

# 退出环境
conda deactivate

# 删除环境
conda remove --name myenv --all
```

该部分参考了以下资源：
- <a href="https://zhuanlan.zhihu.com/p/24478448255">知乎：超全常用 conda 命令整理</a>