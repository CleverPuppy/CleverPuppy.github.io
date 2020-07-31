---
layout: post
title:  "Dockerfile 简单入门"
date:   2020-7-30
categories: markdown
---

# Dockerfile 简单入门

### 参考资料

- [dockerfile文档](https://docs.docker.com/engine/reference/builder/)


## 使用

使用dockerfile来构建一个docker镜像，在本质上来说，是让docker完成**一系列的初始化工作**，然后再执行用户所需的指令，而dockerfile就是描述这一系列的初始化工作。

### **例子**

```
FROM python:3

WORKDIR /usr/src/app

COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD [ "python", "./your-daemon-or-script.py" ]
```
这个示例dockerfile就采用docker执行Python脚本的一系列工作，首先采用`python:3`这个镜像作为基类镜像，然后安装运行所需依赖，复制本地文件到`WORKDIR`中。每次使用`docker run <image-name>`时，默认执行`your-daemon-or-script.py`代码。

### **编译**
```
docker build <path>
```
在编译过程中，`<path>`中的所有内容会发送到docker daemon，所以在不要包含和构建这个镜像不相关的文件

#### 可选参数

- 指定dockerfile
```
docker build -f <path/to/a/dockerfile> <path>
```

- 指定保存信息

  尖括号里分别是仓库名，镜像名和镜像TAG
```
docker build -t <repo-name>/<image-name>:<tag> <path>
```

- 不使用缓存
```
docker build --no-cache <path>
```

## 语法
dockerfile 由多条指令构成，每条指令由一个特殊关键词开始。

### 关键词
 - FROM
 - RUN
 - CMD
 - LABEL
 - EXPOSE
 - ENV
 - ADD
 - COPY
 - ENTRYPOINT
 - VOLUME
 - USER
 - WORKDIR
 - ARG
 - ONBUILD
 - STOPSIGNAL
 - HEALTHCHECK
 - SHELL

大部分关键词都是所见即所得，有一些关键词行为比较像，但是又有差别

## CMD 和　ENTRYPOINT

两个语句都是用于在容器Build之后，启动容器时执行命令。形式均为:
```
['excecutable', 'param1', 'param2'...]
```

但是，两者配合使用时，CMD作为ENTRYPOINT的参数，形式为：
```
['param1', 'param2'...]
```

例如:
```
COPY ./docker-entrypoint.sh /
ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["postgres"]
```

## ADD 和 COPY

两个语句表现很相似，都是添加文件到镜像中。
但是COPY只能复制本地文件，ADD还具有解压tar文件和复制URL文件的功能。

在使用COPY时，要尽量避免一次将所有文件都复制进去，这样可以更有效的利用镜像的cache机制，而不会文件更改，需要从头开始全部重新build。
例如：
```
COPY requirements.txt /tmp/
RUN pip install --requirement /tmp/requirements.txt
COPY . /tmp/
```

同时，由于镜像的大小很关键，应该尽量避免复制大文件。可以通过`RUN wget` 或是
`RUN curl`的方式来下载文件,这样可以在操作完成后，将不需要的文件删除。
例如：
```
ADD http://example.com/big.tar.xz /usr/src/things/
RUN tar -xJf /usr/src/things/big.tar.xz -C /usr/src/things
RUN make -C /usr/src/things all
```
应改用:
```
RUN mkdir -p /usr/src/things \
    && curl -SL http://example.com/big.tar.xz \
    | tar -xJC /usr/src/things \
    && make -C /usr/src/things all
```

如果不是要用到自动解压tar的功能，应该避免使用ADD，而使用COPY。