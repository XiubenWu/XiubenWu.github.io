---
title: 消失的cmp
excerpt: python3中的cmp函数
date: 2022-05-15 16:11:57
tags: 
  -	Python
categories:
  -	爬坑
---

# 简介

cmp是compare的缩写，顾名思义，它的作用用于比较。在python2或C/C++等语言中，cmp函数允许自定义排序函数，即接收两个参数，根据两个参数的关系来决定返回-1（参数1排在参数2之前），0（相等），1（参数1排在参数2之后）三种数值。cmp常用于对列表进行客制化排序。

# python2中的cmp

在python2中，sorted排序有三个参数

```python
sorted(iterable[,cmp,[,key[,reverse=True]]])
```

默认情况下返回从小到大排序的列表。

第一个参数是一个iterable，返回值是一个对iterable中元素进行排序后的列表(list)。

可选的参数有三个，cmp、key和reverse，各自作用如下：

- cmp指定一个定制的[比较函数](https://www.zhihu.com/search?q=比较函数&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A139574674})，这个函数接收两个参数（iterable的元素），如果第一个参数小于第二个参数，返回一个负数；如果第一个参数等于第二个参数，返回零；如果第一个参数大于第二个参数，返回一个正数。默认值为None。

- key指定一个接收一个参数的函数，这个函数用于从每个元素中提取一个用于比较的关键字。默认值为None。key参数的值应该是一个函数，这个函数接收一个参数并且返回一个用于比较的关键字。对复杂对象的比较通常是使用对象的切片作为关键字。

- reverse是一个[布尔值](https://www.zhihu.com/search?q=布尔值&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A139574674})。如果设置为True，列表元素将被倒序排列。

# python3中的cmp

python3废弃了python2 中sorted函数的cmp参数，python3中的sorted形式如下：

```python
sorted(iterable，key=None,reverse=False)
```

  它的声明如下：

```python
def sorted(*args, **kwargs): # real signature unknown
    """
    Return a new list containing all items from the iterable in ascending order.
    
    A custom key function can be supplied to customize the sort order, and the
    reverse flag can be set to request the result in descending order.
    """
    pass
```

- key主要是用来进行比较的元素，只有一个参数，具体的函数的参数就是取自于可迭代对象中，指定可迭代对象中的一个元素来进行排序。
- reverse是一个布尔值。如果设置为True，列表元素将被倒序排列，默认为False。

```
例子：
s = 'asdf234GDSdsf23' #排序:小写-大写-奇数-偶数
 
print("".join(sorted(s, key=lambda x: (x.isdigit(),x.isdigit() and int(x) % 2 == 0,x.isupper(),x))))
原理：先比较元组的第一个值，FALSE<TRUE，如果相等就比较元组的下一个值，以此类推。

先看一下Boolean value 的排序：
print(sorted([True,Flase]))===>结果[False,True]
Boolean 的排序会将 False 排在前，True排在后 .
1.x.isdigit()的作用是把数字放在后边,字母放在前边.
2.x.isdigit() and int(x) % 2 == 0的作用是保证奇数在前，偶数在后。
3.x.isupper()的作用是在前面基础上,保证字母小写在前大写在后.
4.最后的x表示在前面基础上,对所有类别数字或字母排序。
最后结果：addffssDGS33224
```

在python3中，取消了cmp自定义排序函数。自定义排序只能从key入手，而key参数又只能够接收一个参数，如果要实现两个元素之间的自定义比较方式，可借助cmp_to_key函数。它是functools下的一个函数，用于将一个cmp函数变成一个key函数，从而赋给sorted中的key参数，实现自定义排序。

```
例题：
给定一组非负整数 nums，重新排列每个数的顺序（每个数不可拆分）使之组成一个最大的整数。

注意：输出结果可能非常大，所以你需要返回一个字符串而不是整数。
输入：nums = [3,30,34,5,9]
输出："9534330"
输入：nums = [0,0]
输出："0"

class Solution:
    def largestNumber(self, nums) -> str:
    	from functools import cmp_to_key
        if sum(nums)==0: # 连续0情况
            return '0'
        nums = sorted(nums, key=cmp_to_key(lambda x, y: -1 if str(x) + str(y) > str(y) + str(x) else 1))
        return "".join([str(x) for x in nums])
```



