---
layout: post
title:  "OpenFaaS Function构建"
date:   2020-7-24
categories: openfaas k8s
---

本文全部采用faas-cli对openfaas进行操作，
本文代码中的$变量名， 都需要**自行进行修改**，在本文中均**不**指环境变量

## 使用Minikube的额外设置

在使用Minikube时，采用K8S交付的容器是通过Minikube的Docker-daemon，
而Minikube的Docker-daemon与宿主机的Docker-daemon是两个不同的进程。
使用openfaas编译的镜像时默认调用宿主机docker-daemon，
在openfaas 部署编译好的function（也就是镜像）时，默认采用minikube的docker-daemon，
这时minikube会默认在docker hub上拉取所需镜像，而非宿主机中的本地镜像，而导致无法正确部署。

不仅如此，默认的openfaas部署到k8s中时，镜像拉取策略默认为 "always"， 总是从仓库中拉取镜像，
无法直接获得在本地编译的镜像。

所以有以下几个选择
1. 将自己编译好的镜像上传到docker hub 或是其他的registry.
2. 在Minikube中运行本地镜像服务. [教程](https://hasura.io/blog/sharing-a-local-registry-for-minikube-37c7240d0615/)

### 使用aliyun镜像仓库服务

1. 在[aliyun](https://cr.console.aliyun.com/cn-shanghai/instances/repositories)建立仓库

2. 在本机登录aliyun registry

    ```
    # 自行修改以下变量

    docker login --username=$USERNAME $REGISTRY_URL
    ```

    如果需要使用私人仓库镜像，则需要进行步骤3和步骤4.

3. 在openfaas-fn域中配置K8S secrete.

    ```
    # 自行修改变量

    kubectl create secrete docker-registry $SECRETE_NAME \
        --docker-server=$REGISTRY_URL
        --docker-username=$USERNAME
        --docker-password=$PASSWORD
        --namespace openfaas-fn
    ```

4. 将secrete添加到openfaas-fn域的default service account 中

    ```
    kubectl edit serviceaccount default -n openfaas-fn

    # 然后在文件中添加，自行修改变量：
    imagePullSecrets:
    - name: $SECRETE_NAME
    ```

## 创建一个function

```
# 最好新建一个文件夹

# Usage:
#   faas-cli new FUNCTION_NAME --lang=FUNCTION_LANGUAGE [--gateway=http://domain:port] | --list | --append=STACK_FILE) [flags]

# Dockerfile Example
faas-cli new sha512sum --lang=dockerfile
```

之后faas-cli会自动从github拉取模板，得到的文件结构如下：

```
# tree -L 2
├── sha512sum
│   └── Dockerfile
├── sha512sum.yml
└── template
    ├── csharp
    ├── csharp-armhf
    ├── dockerfile
    ├── go
    ├── go-armhf
    ├── java11
    ├── java11-vert-xzhaodao
    ├── node12
    ├── node-arm64
    ├── node-armhf
    ├── php7
    ├── python
    ├── python3
    ├── python3-armhf
    ├── python3-debian
    ├── python-armhf
    └── ruby
```

其中，template中存储的都是模板，`sha512sum.yml`是openfaas的配置文件,
而`/sha512sum/Dockerfile`就是function镜像的`Dockefile`.

在Dockerfile中，需要执行的cli程序，通过设置fprocess的环境变量，`watchdog`会根据这个环境变量来创建进程.
更多关于`watchdog`的信息可在另一篇博文及官方文档中找到.

在本文中，将fprocess设置为sha512sum.

```
ENV fprocess="sha512sum"
```

## 编译

```
faas-cli build -f sha512sum.yml
```

其实这个过程就是调用`docker build`

## 上传镜像到私人仓库

> 确保自己已经登录到私人仓库

```
docker tag $IMAGE_ID $PRIVATE_REPO:TAG  # 重新TAG

docker push $PRIVATE_REPO:TAG           # 上传到私人仓库
```

## 部署function

```
# faas-cli deploy --image=$PRIVATE_REPO:TAG --name=$FUNCTION_NAME

faas-cli deplot --image=$PRIVATE_REPO:TAG --name=sha512sum
```

## 调用

可以通过HTTP请求进行调用，也可通过cli进行调用

```
# 查看部署的function
faas-cli ls

# 调用
faas-cli invoke $FUNCTION_NAME
```