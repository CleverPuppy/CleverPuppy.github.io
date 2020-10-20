---
layout: post
title:  "从docker-compose到k8s"
date:   2020-8-17
categories: docker k8s
---

## docker-compose

传统的单节点多容器docker软件，可以采用docker-compose的方式进行部署

采用docker-compose的方式，可以采用一键化的方式，在单机上部署或者是停止多个服务

如mongodb以及mongo-express服务的docker-compose如下

```
# Use root/example as user/password credentials
version: '3.1'

services:

  mongo:
    image: mongo
    restart: always
    environment:
      MONGO_INITDB_ROOT_USERNAME: root
      MONGO_INITDB_ROOT_PASSWORD: example

  mongo-express:
    image: mongo-express
    restart: always
    ports:
      - 8081:8081
    environment:
      ME_CONFIG_MONGODB_ADMINUSERNAME: root
      ME_CONFIG_MONGODB_ADMINPASSWORD: example
```

其中，mongo是数据库服务。mongo-express是mongodb的管理界面。
在mongo-express中，可以通过解析mongo来连接数据库，然后映射端口到localhost:8081

## 迁移到k8s

k8s采用Service资源的方式，采用自身的DNS服务，为Deployment其他需要改服务的容器提供dns解析服务,
即解析`SERVICE_NAME`到对应实际运行服务的容器

### Service

一个典型的Service资源描述文件，在`spec`条目中，必填的有`selector`和`ports`，
前一项说明该Service应该定向到哪些容器中，后者规定该服务自身的端口，和实际容器中的服务端口，
一般相同即可

```
apiVersion: v1
kind: Service
metadata:
  name: myapp
spec:
  selector:
    app: myapp
  ports:
  - port: <Port>
    targetPort: <Target Port>
```

### Deployment

通过deployment控制器来自动部署实际运行软件的容器

一个典型的Deployment，由多个词条嵌套而成，核心的部分就是`template`，
Deployment控制器会根据指定的策略，自动创建`template`中描述的内容（Pod）

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: <Image>
        resources:
          limits:
            memory: "128Mi"
            cpu: "500m"
        ports:
        - containerPort: <Port>
```

#### select 服务与Pod进行匹配

采用Key-Value的方式创造label，在Service设置需要匹配的label，在Deployment中设置被匹配的label，
这样就能采用K8S DNS服务进行Service - Pod的解析