---
title: kmp解析
math: true
photos:
  -	https://gitlab.com/XiubenWu/xiubenwu-images/-/raw/master/img/20211212kmp0.png
excerpt: ------
date: 2021-12-11 19:13:35
tags:
  -	算法
categories:
  -	笔记
  -	算法
---

# 简介

KMP(Knuth-Morris-Pratt)算法是一种字符串匹配算法，由D.E.Knuth，J.H.Morris和V.R.Pratt提出的，因此人们称它为克努特—莫里斯—普拉特操作（简称KMP算法），正是用它的三个发明者的名字缩写来命名的。KMP算法的核心是利用匹配失败后的信息，尽量减少模式串与主串的匹配次数以达到快速匹配的目的。具体实现就是通过一个next数组实现，数组本身包含了模式串的局部匹配信息。KMP算法的时间复杂度为$O(m+n)$.

# 原理

KMP算法的核心有以下几点：

- 模式字符串(用来匹配的字符串)的next数组；
- 用于求取next数组的最长公共(相等)前后缀；
- 匹配失败后的回溯.

## 最长公共前后缀

- 前缀：不包含最后一个字符；
- 后缀：不包含第一个字符；

例如：

```
aabaacaab
最长公共前后缀为aab，长度为3

a
前缀和后缀均为""(空),即没有前后缀，公共前后缀长度为0
```

求解最长公共前后缀，可以使用动态规划，dp[i]表示子串s[0:i+1]的最长公共前缀(如下所示)。dp数组存放当前[0,i]\(左闭右闭)个字符的最长公共前后缀的长度，求dp[i]的时候，我们已经知道dp[i-1]，即[0,i-1]子串中最长公共前后缀的长度，设dp[i-1]=x，s[0,i-1]的公共前缀为,s[0:x]，s[x]为最长公共前缀的后面一位，比较s[x]与s[i],若它们相等则s[i]的最长公共前后缀为dp[x]+1=dp[i-1]+1，若s[x]与s[i]不相等，则需要找次长公共前后缀(相当于将前缀移动到后缀的地方)。
$$
dp[i](LoopNext(index))=\left\{
\begin{aligned}
&dp[index-1]+1&,&s[i]==s[dp[index-1]]\\
&0&,&other
\end{aligned}
\right.
$$


index是记录匹配是跳转情况的临时指针。以i=6为例(最后一个字符b),`s[dp[6-1]]=s[3]=a!=s[6]=b`，因此选择跳转到次最长的公共前后缀，index=dp[6-1]=3，重复操作，`s[dp[3-1]]=s[1]=b=s[i]=b`，出现相等情况，跳出循环，其它情况为临时的指针递减至0也未找到相等的字符串，此时也跳出循环。`dp[6] = dp[3-1]+1=2`.

```
i = 0123456
s = abaabab
dp= 0011232
```

```python
# 动规求解最长公共前后缀
def next_cal(s):
    n = len(s) # 字符串长度
    dp = [0] * n # 动规数组，KMP中的next数组，也有其它叫法...
    for i in range(1, n):
        index = i # 临时指针
        while index > 0 and s[dp[index - 1]] != s[i]: # 直到找到匹配字符或指针归0
            index = dp[index - 1]
        if s[dp[index - 1]] == s[i]: # 有匹配项
            dp[i] = dp[index - 1] + 1
        else: # 没有任何可以匹配的
            dp[i] = 0
    return dp
```

## 字符串匹配

至此，已经使用动态规划得到模式串的next数组，接下来的匹配过程也很简单。模式串初始化一个索引指针，表示这个指针之前的字符均匹配，匹配串作一次主循环，对于匹配串的每一个字符，使用模式串指针所指的字符进行比较，若相等两个字符串的指针都加一，若不等，则利用next数组将模式串的指针进行回溯(**等价于将最长公共前缀放到最长公共后缀的地方**)，这个地方的实现方法有多种，例如`移位数等于已匹配的字符串长度减去最长公共前后缀长度`等，有些next数组从-1开始等，但核心思想均为前缀移到后缀的地方。若移动之后还是不像等则使用next数组继续移动前缀到后缀处，直到相等或模式串指针到0(next数组性质决定其为递减，一直循环终将至0)

```python
class KMP:
    def __init__(self):
        pass

    def search_string(self, s, target):
        next_arr = self.__next_arr(target) # 建立next数组
        n_s = len(s) # 匹配串长度
        n_t = len(target) # 模式串长度
        index_t = 0 # 模式串索引指针
        for i in range(n_s):
            if s[i] == target[index_t]: # 字符相等，两者均后移
                index_t += 1
            else: # 字符不相等
                while index_t != 0 and s[i] != target[index_t]: # 使用next数组循环匹配
                    index_t = next_arr[index_t - 1]
            if index_t == n_t: # 模式串匹配完成
                print(i - n_t + 1) # 输出匹配串的起始索引
                index_t = next_arr[index_t - 1] # 继续匹配下一个
        return next_arr

    def __next_arr(self, s):
        n = len(s)
        dp = [0] * n
        for i in range(1, n):
            index = i
            while index > 0 and s[dp[index - 1]] != s[i]:
                index = dp[index - 1]
            if s[dp[index - 1]] == s[i]:
                dp[i] = dp[index - 1] + 1
            else:
                dp[i] = 0
        return dp
```

# 实现

```go
package main

import (
	"fmt"
)

func main() {
	var s string
	s = "CEBDAEEAACEBDAE" //匹配串
	target := "EBDAE" // 模式串
	ans := kmp(s, target)
	fmt.Println(ans)
}
func calNext(s string) []int {
	n := len(s)
	var index int
	dp := make([]int, n)
	for i := 1; i < n; i++ {
		index = i
		for index > 0 && s[dp[index-1]] != s[i] {
			index = dp[index-1]
		}
		if index == 0 {
			if s[0] == s[i] {
				dp[i] = 1
			} else {
				dp[i] = 0
			}
		} else {
			dp[i] = dp[index-1] + 1
		}

	}
	return dp
}
func kmp(s, target string) []int {
	var index int
	n_s, n_t := len(s), len(target)
	next_arr := calNext(target)
	var ans []int
	for i := 0; i < n_s; i++ {
		if s[i] == target[index] {
			index++
		} else {
			for index > 0 && s[i] != target[next_arr[index-1]] {
				index = next_arr[index-1]
			}
		}
		if n_t == index {
			ans = append(ans, i-n_t+1)
			index = next_arr[index-1]
		}
	}
	return ans
}

```



