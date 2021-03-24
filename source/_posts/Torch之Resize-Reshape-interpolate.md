---
title: Torch之Resize/Reshape/interpolate
math: true
excerpt: 虚假的Resize？真实的Resize!&dArr;
date: 2021-03-24 16:18:17
tags:
    - torch
    - Python
categories:
    - [爬坑]
    - [笔记]
---

# 概述
在处理数据的时候，经常需要改变数组维度的大小。其中涉及了数组重组、压缩、拉伸等变换方式。官方库提供了诸如`resize`、`reshape`等数组维度转换函数，那该如何运用才能实现自己想要的结果呢。此文以python中的Tensor为例来简要分析几种数组变换的区别。

# 正文
`resize`、`reshape`、`veiw`等是我们在Torch中常用的维度变换函数。

## resize_()函数
此函数直接操作原始张量，即结果返回到原始张量中，按照`tensor.resize_()`调用即可。

```python
运行：
a = torch.tensor([[1, 2, 3], [4, 5, 6]])
print(a)
a.resize_((3, 2))
print(a)

输出：
tensor([[1, 2, 3],
        [4, 5, 6]])

tensor([[1, 2],
        [3, 4],
        [5, 6]])
```
以上是转换的输入输出总尺寸（变量中存放的总的数据量相同，可视为所有维度总乘积相等）相同的情况，接下来尝试不同时的情况。
```python
运行：
a = torch.tensor([[1, 2, 3], [4, 5, 6]])
print(a)
a.resize_((2, 2))
print(a)
输出：
tensor([[1, 2, 3],
        [4, 5, 6]])
tensor([[1, 2],
        [3, 4]])
```
```python
运行：
a = torch.tensor([[1, 2, 3], [4, 5, 6]])
print(a)
a.resize_((3, 3))
print(a)
输出：
tensor([[1, 2, 3],
        [4, 5, 6]])
tensor([[                1,                 2,                 3],
        [                4,                 5,                 6],
        [32651492442964069, 29273822787141743, 27303575259512924]])
```
由以上两个例子可以看出`resize`采用多减少补的原则，当输出总尺寸小于原总尺寸时，将自动舍弃索引值较大的数据，使用索引值靠前的数据来组成新的数组；当输出总尺寸大于原尺寸时，则自动补充随机的数据。

## reshape函数
对于输入输出总尺寸相同的情况，`reshape`函数的结果与`resize`函数的结果相同。
```python
运行：
a = torch.tensor([[1, 2, 3], [4, 5, 6]])
print(a)
a = a.reshape((3, 2))
print(a)
输出：
tensor([[1, 2, 3],
        [4, 5, 6]])
tensor([[1, 2],
        [3, 4],
        [5, 6]])

```
当总尺寸不相同的时候，`reshape`函数则无法进行计算，即`reshape`函数没有裁剪和自动填充功能，只能够进行简单的维度重组。

## view函数
`view`的功能与reshape相同，只能进行维度重组，不可进行裁剪和扩充，用法也和`reshape`相似。
`view`和`reshape`都可以将某一个维度设置为`-1`来实现该维度自适应。
```python
运行：
a = torch.tensor([[1, 2, 3], [4, 5, 6]])
print(a)
a = a.view((-1, 2))
print(a)
输出：
tensor([[1, 2, 3],
        [4, 5, 6]])
tensor([[1, 2],
        [3, 4],
        [5, 6]])
```

## interpolate函数
`intepolate`是`torch.nn.functional`库中的一个插值函数。使用插值的方式来进行数组重组，实现的效果类似于图像的拉伸压缩。
```
def interpolate(input, size=None, scale_factor=None, mode='nearest', align_corners=None, recompute_scale_factor=None):
    r"""Down/up samples the input to either the given :attr:`size` or the given
        :attr:`scale_factor`

        The algorithm used for interpolation is determined by :attr:`mode`.

        Currently temporal, spatial and volumetric sampling are supported, i.e.
        expected inputs are 3-D, 4-D or 5-D in shape.

        The input dimensions are interpreted in the form:
        `mini-batch x channels x [optional depth] x [optional height] x width`.

        The modes available for resizing are: `nearest`, `linear` (3D-only),
        `bilinear`, `bicubic` (4D-only), `trilinear` (5D-only), `area`
```
更据原函数中的描述，此函数仅接受3、4、5维度的数据(深度学习专用函数了)，而且可以选择不同的插值方式，运用灵活。
```python
运行：
a = torch.tensor([[[[1.0, 10.0], [10.0, 1.0]]]])
print(a)
a = interpolate(a, size=(5, 5), mode='bicubic',align_corners=True)
print(a)
输出：
tensor([[[[ 1.0000,  3.0391,  5.5000,  7.9609, 10.0000],
          [ 3.0391,  4.1542,  5.5000,  6.8458,  7.9609],
          [ 5.5000,  5.5000,  5.5000,  5.5000,  5.5000],
          [ 7.9609,  6.8458,  5.5000,  4.1542,  3.0391],
          [10.0000,  7.9609,  5.5000,  3.0391,  1.0000]]]])
```
# End
以上只是torch中的简单示例，不同语言中有些许差异。

