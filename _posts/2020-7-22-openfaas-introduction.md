---
layout: post
title:  "OpenFaaS Introduction"
date:   2020-7-22
categories: openfaas k8s
---

# OpenFaaS Introduction

## 介绍

> [OpenFaaS官网](https://www.openfaas.com/)

## 快速环境搭建

[K8S下安装文档](https://docs.openfaas.com/deployment/kubernetes/)

1. 安装`faas-cli`

    ```
    # MacOS and Linux users

    # If you run the script as a normal non-root user then the script
    # will download the faas-cli binary to the current folder
    $ curl -sL https://cli.openfaas.com | sudo sh

    # Windows users with (Git Bash)
    $ curl -sL https://cli.openfaas.com | sh
    ```

2. 安装arkade

    ```
    # For MacOS / Linux:
    curl -SLsf https://dl.get-arkade.dev/ | sudo sh
    ```

3. 安装OpenFaaS

    ```
    # 单机安装
    arkade install openfaas

    #集群安装
    arkade install openfaas --load-balancer
    ```

    在安装结束后,可以看到openfaas的 URL 和 密码, 之后就可以通过网页端来操作.

    之后如果需要查看安装信息,可以通过`arkade info`命令获得.

    ```
    arkade info openfaas
    ```

## 原理

### WatchDog

> [openfaas watchdog Github页面](https://github.com/openfaas/faas/tree/master/watchdog)

和传统意义上的看门狗程序来说很不一样,openfaas开门狗主要是为`function`提供一个通用的io接口.
作为一个将openfaas 网关收到的HTTP请求转化成`function`输入的一个中间件. 

本质上来说watchdog是一个小型的web server,将HTTP请求处理为`stdin`输入给`function`,
然后将`function`的`stdout`作为HTTP RESPONS返回.

![watchdog原理](https://camo.githubusercontent.com/61c169ab5cd01346bc3dc7a11edc1d218f0be3b4/68747470733a2f2f7062732e7477696d672e636f6d2f6d656469612f4447536344626c554941416f34482d2e6a70673a6c61726765)

#### of-watchdog运行模式

1. HTTP 模式
   
   先启动进程,然后等待HTTP 请求

2. 序列化模式

   HTTP请求-> 序列化-> fork

3. 流模式

   HTTP请求-> fork -> 流

4. Afterburn

   request死了后,容器就死了

5. 静态(文件)模式
      
   开启一个静态文件服务器

### alpine linux

> [arpine linux 官网](https://www.alpinelinux.org/)

openfaas使用arpine作为容器宿主linux,主要的优点就是小,作为容器时不超过8M,更够更快速启动.

同时包管理也包含了绝大多数常用的软件.