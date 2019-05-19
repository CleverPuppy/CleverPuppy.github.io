---
layout: post
title:  "在centos7虚拟机中使用命令行安装增强功能"
date:   2019-05-18 13:22:42 -0500
categories: centos7 virtualbox
---

## 增强功能

在安装完虚拟机后，可以安装Hypervisor提供的增强组件，可以实现一些更为方便的功能。在VirtualBox中，包括像虚拟机`分辨率的改变`，以及在宿主机和虚拟机中`共享剪贴板`，和自动`挂载宿主机中的文件`都需要`增强功能`的支持

## 安装步骤

1. 点击虚拟机顶部菜单，设备->安装增强功能
    
    这个时候，virtualbox会为虚拟机添加一个包含了安装增强组件的`光盘映像`。

2. 将cdrom挂载到文件系统

    >很多情况下，会自动挂载到/opt/目录下，如果在/opt/目录下有类似```VboxGuestAdditions-6.0.8```的文件夹，则可以跳过这一步


    cdrom 位于```/dev/cdrom```
    ```
    sudo mount -r /dev/cdrom {挂载路径}
    ```
3. 安装一些依赖
    ```
    sudo yum install epel-release && sudo yum install update

    sudo yum install yum install dkms binutils gcc make patch libgomp glibc-headers glibc-devel kernel-headers 

    sudo yum install yum install kernel-devel
    ```

4. 切换到{挂载路径}， 然后运行安装程序

    ```
    sudo ./VBoxLinuxAdditions.run
    ```

