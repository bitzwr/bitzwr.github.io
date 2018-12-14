title: 配置本地yum源
date: 2016-01-03 23:50:28
categories: linux
tags: linux
---
在linux的众多发行版中，[RedHat Enterprise Linux](http://www.redhat.com/en) 最受企业欢迎。对于红帽系linux(包括CentOS和Fedora)来说，软件包的安装与管理就绕不开yum了。
<!-- more -->

### 前言
yum(Yellow dog Updater, Modified)， 是RedHat系linux的软件包管理器。基于RPM包管理，能够从指定的服务器自动下载RPM包并且安装，可以自动处理依赖性关系，并且一次性安装所有依赖的软件包。   

为什么需要配置本地yum源呢？ 红帽系统虽然是开源的，但是红帽提供了一套的RedHat订阅服务，其中就包括了红帽的yum源。所以想要使用红帽官方的yum源，就必须得付费才可以。  

RedHat Enterprise Linux（CentOS）的系统镜像中，带有一个软件包集合。本文的主要目的就是要用**系统镜像文件**制作**本地的yum源**。

### 软件环境
本次操作使用MacBook Pro作为宿主机，[Oracle VirtualBox 5.0](https://www.virtualbox.org/wiki/Downloads) 提供虚拟环境，CentOS-7.0 作为实验系统来进行。

### 步骤
#### 1. 挂载系统镜像文件
在虚拟机设置CentOS的镜像光盘文件(将光盘放入光驱)
![](https://org-wangbiao.oss-cn-beijing.aliyuncs.com/blog/local-yum-1.png)   
挂载光盘到系统指定的目录

```shell
mkdir /mnt/cdrom # 镜像文件挂载目录
mount /dev/cdrom /mnt/cdrom  # 挂载系统镜像，/dev/cdrom 代表光驱设备
ll /mnt/cdrom # 查看是否挂载成功，如下图样则成功
```
![查看是否挂载成功](https://org-wangbiao.oss-cn-beijing.aliyuncs.com/blog/local-yum-2.png)   

#### 2. 复制系统镜像文件到指定目录，作为yum的源

```shell
cp -r /mnt/cdrom /media/centos7 #将光盘中的内容复制到系统中/media/centos7目录中
```
执行结果如下
![](https://org-wangbiao.oss-cn-beijing.aliyuncs.com/blog/local-yum-3.png)

#### 3. 配置本地yum源
```shell
cd /etc/yum.repos.d/  # 进入yum配置目录
```
![](https://org-wangbiao.oss-cn-beijing.aliyuncs.com/blog/local-yum-4.png)
把该目录下除CentOS-Media.repo外的其他文件都删除（排除干扰，简单粗暴），并编辑CentOS-Media.repo内容如下：

```shell
vi CentOS-Media.repo  # 编辑配置文件，添加如下内容

[centos-media]
name=CentOS 7.0  # 自定义名称
baseurl=file:///media/centos7/  # 上边光盘复制到的目录
enabled=1  # 启用yum源，0为不启用，1为启用
gpgcheck=1  # 启用GPG-KEY，0为不检查，1为检查
gpgkey=file:///media/centos7/RPM-GPG-KEY-redhat-release   # GPG路径

：wq!  # 保存退出
```
编辑后文件如下
![yum源配置文件内容](https://org-wangbiao.oss-cn-beijing.aliyuncs.com/blog/local-yum-5.png)

#### 5. 测试yum是否配置成功
```shell
yum list
```
如果显示出软件包列表，就说明配置成功了。
#### 6. 清理yum缓存
```shell
yum clean all  # 清除yum缓存
yum makecache  # 缓存本地yum源中的软件包信息
```
#### 7.小结
接下来就可以欢快地使用**yum**啦。

```shell
yum install vim  # 安装
yum remove vim  # 卸载
```
### 参考资料

1. http://yum.baseurl.org/
2. http://www.cnblogs.com/shenliang123/p/3829044.html
3. http://www.cnblogs.com/chuncn/archive/2010/10/17/1853915.html