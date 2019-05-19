---
layout: post
title:  "Markdown的基本用法"
date:   2019-05-19 11:41:42 -0500
categories: markdown
---

> 使用标记语言可以 __快速创建格式化文档__，如markdown.\
> 文件名后缀为`.markdown`或是`.md`\
> 下面给出了一些markdown的基本用法，包括排版，链接，图片和代码块

## 标题
  * `#`号代表了标题h1，如`# title1`为一级标题
  * `##`代表h2

## 段落
  * `换行符`是切换到下一个段落
  * `\` 是切换到另一行

## 强调
  * `*` 或者 `_` 包围使其 *倾斜*
  * `**` 或者 `__`包围使其 **加粗**
  * 单独使用`*` 和 `_` 需要加\作为转义字符，如`\*`

## 代码
  * 放在\`\`中间 `X+Y=Z`
  * 放在\`\`\`和 \`\`\`之间生成代码块
  * 也可以使用缩进生成代码块（个人不推荐）

## 引用
  * `>`符号

## 列表

example:\
无标号列表
```
- Flor
- Apple
- Pie
```
效果：
- Flor
- Apple
- Pie

example:\
有标号列表
```
Four steps to better sleep:
  1. Stick to a sleep schedule
  2. Create a bedtime ritual
  3. Get comfortable
  4. Manage stress
```
效果：\
Four steps to better sleep:
  1. Stick to a sleep schedule
  2. Create a bedtime ritual
  3. Get comfortable
  4. Manage stress

## 链接
  * url标签应该包括在<>里面
  * 文本链接[百度](baidu.com)


        [百度](baidu.com)

  * 经常用到的链接 [百度][1] and [baidu][1]\

[1]:baidu.com

        [百度][1] [baidu][1]
        ...
        ...
        可以统一放置文档末尾
        [1]:baidu.com

## 图片
  * 通过url使用图片
  ![](http://commonmark.org/help/images/favicon.png)


        ![](http://commonmark.org/help/images/favicon.png)

  * 多重引用  


        ![][1]
        [1]: http://commonmark.org/help/images/favicon.png

