---
layout: post
title:  "K8S环境搭建-国内镜像指南"
date:   2020-7-21
categories: k8s
---

# K8S环境搭建-国内镜像指南

快速搭建ubuntu 18.04 docker + k8s　开发环境

## ubuntu 18.04 LTS 国内镜像

> Ubuntu 的软件源配置文件是 /etc/apt/sources.list。将系统自带的该文件做个备份，将该文件替换为下面内容，即可使用 [TUNA 的软件源镜像](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)。

```
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse

# 预发布软件源，不建议启用
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
```

## 安装docker

采用清华开源镜像站的docker-ce[镜像](https://mirrors.tuna.tsinghua.edu.cn/help/docker-ce/)

>如果你过去安装过 docker，先删掉:

```
sudo apt-get remove docker docker-engine docker.io
```

>首先安装依赖:

```
sudo apt-get install apt-transport-https ca-certificates curl gnupg2 software-properties-common
```

>信任 Docker 的 GPG 公钥:

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

>对于 amd64 架构的计算机，添加软件仓库:

```
sudo add-apt-repository \
   "deb [arch=amd64] https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

>如果你是树莓派或其它ARM架构计算机，请运行:

```
echo "deb [arch=armhf] https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/ubuntu \
     $(lsb_release -cs) stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list
```

>最后安装

```
sudo apt-get update
sudo apt-get install docker-ce
```
### 添加当前用户到Docker组

此举相当于给当前用户root权限！之后使用docker不需要sudo

```
sudo groupadd docker
sudo usermod -aG docker $USER
# 之后重新登陆
```


### docker国内镜像加速
> [中科大Docker Hub 镜像缓存](https://mirrors.ustc.edu.cn/help/dockerhub.html)

1. 配置文件`/etc/docker/daemon.json`添加:
```
{
  "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn/"]
}
```

2. 重启docker
```
sudo systemctl restart docker
```

-------------------
也可采用阿里云镜像服务,需要注册阿里云账号,可加速托管镜像

## K8S开发环境快速搭建

采用minikube快速搭建k8s开发环境,其中[阿里云](https://github.com/AliyunContainerService/minikube)为minikube提供了镜像加速的服务.

1. 安装Kubectl

    下载最近release

   ```
   curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl

   ```

   授予执行权限
   ```
   chmod +x ./kubectl
   ```

   添加到PATH
    ```
    sudo mv ./kubectl /usr/local/bin/kubectl
    ```

   测试是否安装成功
    ```
    kubectl version --client
    ```
2. 安装minikube
   ```
   curl -Lo minikube https://kubernetes.oss-cn-hangzhou.aliyuncs.com/minikube/releases/v1.12.1/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
   ```
3. 启动minikube,完成安装


   缺省会安装Docker驱动的minikube

   ```
   minikube start --image-mirror-country cn --registry-mirror=https://docker.mirrors.ustc.edu.cn

   ```

4. 打开k8s控制台
   ```
   minikube dashboard
   ```
