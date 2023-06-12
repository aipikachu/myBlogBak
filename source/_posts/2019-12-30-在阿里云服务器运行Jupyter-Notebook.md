---
title: 在阿里云服务器运行Jupyter Notebook
tags:
  - Jupyter Notebook
mathjax: true
abbrlink: 23962
date: 2019-12-30 19:56:20
---

**声明**：本文是在其他人的方法上的摘要总结，主要是作为供个人使用的备忘记录。本文主要参考如下文章：

* [远程访问jupyter notebook](https://yq.aliyun.com/articles/441132)
* [远程访问阿里云服务器jupyter](https://blog.csdn.net/sinat_28442665/article/details/85612475)

`Jupyter notebook`是一个基于浏览器的`python`数据分析工具，方便易用。最简单的方法是通过[`Anaconda`](https://anaconda.org)来安装，`Anaconda`中集成了丰富的第三方`python`库，使用简单。具体的安装方法不再赘述。

<!-- more -->

安装完成之后，在终端键入`jupyter notebook`便可以在本地浏览器中使用。但是，默认的运行方式下，只能够是在本地的浏览器中访问。所以，对于拥有服务器的人们来说，为了能够远程访问安装在服务器端的`Jupyter notebook`，则是需要做一些额外的配置。在参考其他人的方法后，做简要摘要记录如下。

### 生成`jupyter notebook`配置文件

在终端键入如下命令：

```bash
jupyter notebook --generate-config
```

此时，即会生成新的配置文件（默认名称为`jupyter_notebook_config.py`），一般位于`/root/.jupyter`目录。

### 设置`Jupyetr`登陆密码，生成密文

终端键入：

```bash
ipython
```

并依次键入：

```bash
from notebook.auth import passwd
passwd()
```

在后续窗口中，键入密码，并再次确认输入密码（说明：键入密码时，在窗口中不会显示任何内容）

```bash
Enter password:
Verify password:
```

两次键入密码无误后，一般会生成密文密钥，形式如下所示：

```bash
Out[2]: 'sha1:ce00......'
```

将密文`'sha1:ce00......'`复制下来。

### 修改默认配置文件

默认的配置文件一般位于`/root/.jupyter/jupyter_notebook_config.py`。在终端键入

```bash
vim /root/.jupyter/jupyter_notebook_config.py
```

将其中的如下部分内容修改为如下所示形式：

```bash
c.NotebookApp.ip = '*'
c.NotebookApp.password = u'sha1:ce00...刚才复制的那个密文内容'
c.NotebookApp.open_browser = False
c.NotebookApp.port = 8888 #随便指定一个端口
c.NotebookApp.allow_remote_access = True
```

### 启动Jupyter

至此，配置基本完成，在终端键入：

```bash
jupyter notebook
```

即可在本地浏览器直接访问`http://address_of_remote:8888`就可以看到jupyter的登陆界面，键入密码之后就可以访问服务器上的`Jupyter notebook`了。

### 进一步：将`Jupyter`服务器作为一个后台的服务，始终启动

但是在上面的设置中还存在一个问题，就是一旦关闭终端，`Jupyter`程序也就终止了运行。这是由于该`Jupyter`程序是作为当前终端的一个子进程，在用户终端关闭的时候将收到一个`hangup`信号，从而使得`Jupyter`程序被关闭。

为了让程序能忽视`hangup`信号，可以使用`nohup`命令。同时需要配合`&`来将程序放入后台中运行。在终端键入如下命令：

```bash
nohup jupyter notebook --allow-root &
```

此时会在当前目录生成一个nohup.out文件，可以看作是程序的输出日志文件。如果要查看该日志文件，只需在终端键入：

```bash
tail -fn 50 nohup.out
```

但是，如果需要查看，或者终止由`nohup`提交到后台运行的程序的话，则需要额外的命令辅助。首先是查看后台运行的程序，在终端键入：

```bash
ps ux
```

即可在终端中显示当前后台运行的的程序号PID。若要终止某一程序的话，只需要在终端中键入：

```bash
kill -9 10259   # 其中，10259为该进程的PID号
```

至此，常规设置结束。再次强调，本文仅是作为本人备忘记录使用。
