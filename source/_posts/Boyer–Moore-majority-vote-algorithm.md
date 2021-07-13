---
title: Boyer–Moore majority vote algorithm
excerpt: 常数空间复杂度下的投票算法
date: 2021-07-13 16:21:20
tags:
    - [特色算法]
categories:
	- 笔记
	- 算法

---

# 简介
**博耶-摩尔多数投票算法**(Boyer–Moore majority vote algorithm)，是用来求取众数的一种常数空间复杂度的算法。所谓众数，就是数组中数量超过总数组长度一般的元素，也被称为多数元素、主要元素。

***
# 思想
两次扫描，第一次得到一个候选元素，第二次判断候选元素是否是我们所需的众数。

## 第一次扫描
指定一个元素为候选元素`candidate`（可为任意数），再选定一个变量`count`来进行计数,然后依次扫描，对于数组中每一个元素，首先判断count是否为0，若为0，则把`candidate`设置为当前元素。之后判断`candidate`是否与当前元素相等，若相等则`count+=1`，否则`count-=1`。

## 第二次扫描
第二次扫描统计可能是多数元素出现的次数，从而断定它是不是多数元素。由于最后的候选元素可能不是多数元素，因此需要进行第二次扫描判定。如`[1,5,3,4,2,2,2]`最后存留候选元素为2，`count`值为2，但是其不是多数元素。

***
# End
此算法需要两次遍历，亚线性空间算法无法通过一次遍历就得出输入中是否存在多数元素。如**Graham Cormode**所言：[no algorithm can correctly distinguish the cases when an item is just above or just below the threshold in a single pass without using a large amount of space](https://dl.acm.org/doi/10.1145/1562764.1562789)



