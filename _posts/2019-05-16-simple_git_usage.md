---
layout: post
title:  "简单Git用法"
date:   2019-05-15 07:34:42 -0500
categories: github
---

## git的本地使用
- 创建新仓库
  ```
  cd /your/folder
  git init
  ```

- 添加文件到缓冲区
  ```
  git add <filename>
  或者使用下面一个命令添加全部文件，更加常用
  git add *
  ```
  
- 提交到仓库
  ```
  git commit -m "提交信息"
  ```

## 与远程仓库同步
  - 与远程仓库绑定
  ```
  git remote add origin <server>
  ```
> 1. 这里的origin 指的是仓库别名，你也可以以其他的名称来明明
> 2. <server>指的是远程仓库的地址,比如`git@github.com:CleverPuppy/PersonalSite.git`  

  - 推送到远程仓库
  ```
  git push origin master
  ```

## 利用github 可以很方便的实现对远程网站的部署

  - 把远程仓库的项目`clone`到本地
  ```
  git clone <server>
  ```
>可以用ssh方式也可以用https方式

  - 远程仓库强制替换本地改动
  ```
  git fetch --all
  git reset --hard
  git pull
  ```
