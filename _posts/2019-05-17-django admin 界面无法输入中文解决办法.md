---
layout: post
title:  "django admin的无法输入中文的解决办法"
date:   2019-05-15 07:34:42 -0500
categories: django
---

## 如果使用的是python2.7在models.py 中




    from __future__ import unicode_literals
    class XXXX(models.Model):

          ...


        def __unicode__(self):
            return "Instance of my class"

>In Python 2, the object model specifies `__str__()` and  `__unicode__()`methods. If these methods exist, they must return str (bytes) and unicode (text) respectively.

>这里的意思是如果是text文字就用`__unicode__` 如果是bytes字节数据就
用`__str__()`

## 如果是python3 则把函数换成

    def __str__(self):
        return "Instance of my class"


## 通用处理办法

>Django provides a simple way to define __str__() and  __unicode__() methods that work on Python 2 and 3: you must define a __str__() method returning text and to apply the python_2_unicode_compatible() decorator.


    from __future__ import unicode_literals
    from django.utils.encoding import python_2_unicode_compatible

    @python_2_unicode_compatible
    class MyClass(object):
    def __str__(self):
        return "Instance of my class"
