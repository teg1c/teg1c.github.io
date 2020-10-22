---
layout: mypost
title: Centos 7 / Windows 安装 Python 3 与 Python 2 共存
categories: [CENTOS,WINDOWS,PYTHON]
---
# Centos 7
## 下载 python 3 

```
[root@tegic ~]# wget https://cdn.b15.me/Python-3.7.3.tgz
```

## 安装 python 3 

```$xslt
#解压源文件
[root@tegic ~]# tar -zvxf Python-3.7.3.tgz

#进入源文件目录
[root@tegic ~]# cd Python-3.7.3

#先安装相应依赖
[root@tegic Python-3.7.3]# yum install openssl openssl-devel sqlite sqlite-devel bzip2 bzip2-devel expat expat-devel gdbm gdbm-devel readline readline-devel -y

#编译安装
[root@tegic Python-3.7.3]# ./configure --prefix=/usr/local/python3
[root@tegic Python-3.7.3]# make && make install

#创建链接文件
[root@tegic Python-3.7.3]# ln -sv /usr/local/python3/bin/python3 /usr/local/bin/
[root@tegic Python-3.7.3]# ln -sv /usr/local/python3/bin/pip3 /usr/local/bin/
```

# Windows

## 下载 python3 和 python2 

本次安装的版本是 ` Python3.5.2` 和 `Python2.7.9`
- Python3.5.2 `https://www.python.org/ftp/python/3.5.2/python-3.5.2rc1-amd64.exe`
- Python2.7.9 `https://www.python.org/ftp/python/2.7.9/python-2.7.9.amd64.msi`

## 安装
先安装 `python 3` 再安装 `python 2`

### Python3 安装

首先选择安装目录，本文安装路径为 ` D:\Python\python35 `，然后点击下载好的 `python3`软件包进行安装，具体流程如下

在下图红色标记的地方 `Add Python3.5 to PATH`前勾选，可以直接将`python3`添加到系统环境变量，然后在`Customize installation`中自定义安装路径

![](https://wx3.sinaimg.cn/large/006LCYVcgy1g5hlrymtwnj30a006575k.jpg)

直接 next 即可

![](https://ws3.sinaimg.cn/large/006LCYVcgy1g5hltp764ij30a0065jso.jpg)

下图中`Advanced Options`中没有默认勾选的根据自己的需要进行勾选，黄色标记为`VS 2015`及其以后版本需要的，如果你以后打算在`VS 2015`中配置`python`环境请勾选此项，点击`Browse`选择安装目录，然后点击`Install`进行安装。直至安装完成

![](https://ws1.sinaimg.cn/large/006LCYVcgy1g5hluku9moj30a0065ta1.jpg)

### Python2 安装

点击`python2`安装包，点击运行，`next`，选择安装目录，本文安装路径为`D:\Python\python27`，然后`next`

![](https://ws1.sinaimg.cn/large/006LCYVcgy1g5hlwtzanjj30a008m401.jpg)

下图黄色圈出的部分，显示不能够添加路径到系统变量，不能够直接使用`python`命令没有关系，先点击`next`进行安装，后面专门解决。

![](https://wx4.sinaimg.cn/large/006LCYVcgy1g5hlx82j11j30a008m76j.jpg)

![](https://ws3.sinaimg.cn/large/006LCYVcgy1g5hlxf9ga8j30a008m74l.jpg)

### 添加 python2 到系统环境变量

 打开，控制面板\系统和安全\系统，选择高级系统设置，环境变量，选择`Path`，点击编辑，新建，分别添加`D:\Python\python27`和`D:\Python\python27\Scripts`到环境变量。
 
 
### 修改 python.exe 名字为 python2.exe 和 python3.exe
 
找到`python2`和`python3`的安装目录，修改`python2.7.9`和`python3.5.2`中`python.exe`和`pythonw.exe`的名称为`python2.exe`、`pythonw2.exe`和`python3.exe`、`pythonw3.exe`。
 
然后在运行`cmd`命令，输入`python2`即可运行`python2.7.9`版本
 
输入`python3`即可运行`python3.5.2`版本
 
### pip2 和 pip3设置
 
`Python` 安装包需要用到包管理工具`pip`，但是当同时安装`python2`和`python3`的时候，`pip`只是其中一个版本，以下将提供一个修改方式，即重新安装两个版本的`pip`，使得两个`python`版本的`pip`能够共存。

依次在命令行输入

```angular2
python3 -m pip install --upgrade pip --force-reinstal
python2 -m pip install --upgrade pip --force-reinstall
```

现在可以通过`pip2 -V` 和`pip3-V`查看两个版本的`pip`信息，以后只需运行`pip2install XXX`和`pip3 install XXX`即可安装各自的`python`包。

至此，`pip2`和`pip3` 修改成功。