---
title: "ARTS01.打卡第一天"
date: 2024-06-13T14:23:23+08:00
draft: false
tags: ['ARTS计划']
---

# **Algorithm**

[1768. 交替合并字符串](https://leetcode.cn/problems/merge-strings-alternately/description/?envType=study-plan-v2&envId=leetcode-75)

### 思路：

这道题我认为考察的是对边界值的处理。
因为两个字符串不一样长，所以基本思路是：
1. 比较两个字符串的长度，按短的进行遍历
2. 遍历过程中不断的将两个字符串对应位置的字符添加到返回字符串中
3. 再将长字符串剩余的部分添加到返回字符串中

### 解法：

```python
class Solution:
    def mergeAlternately(self, word1: str, word2: str) -> str:
        result = ''
        for v1,v2 in zip(word1, word2):
            result += v1 + v2

        l1, l2 = len(word1), len(word2)
        if l1 >= l2:
            result += word1[l2:]
        else:
            result += word2[l1:]
        return result
```

- 执行用时超过 88.01 %
- 消耗内存超过 32.99 %

参考了几个力扣社区里的py3解法，思路大致都与上面相同，不过倒是了解到一个新的库函数 `itertools.zip_longest`。

# **Review**

最近在学习docker，所以看了一篇相关的文章：[You should stop writing Dockerfiles today — Do this instead](https://medium.com/kpmg-uk-engineering/you-should-stop-writing-dockerfiles-today-do-this-instead-3cd8a44cb8b0)

### 大意：

这位哥们天天写Dockerfile写烦了，结果Docker社区出了个新工具：`docker init`，大哥用完后觉得屌爆了，剩下的就是讲这个新工具的用法。

![](/arts01/1.jpg)

总结一下：
- 使用此工具能够写出超过90%新手的更好维护的Dockerfile
- 提供了一个傻瓜式的行业最佳实践
- 用这个工具能够更省时、更有效、更不易引入安全漏洞

### 点评：

之前学docker的时候自己做镜像才要写Dockerfile，写的时候一般都是边查手册边google的。而目前这个`docker init`提供了一种类似于脚手架式的命令行工具，通过问答的方式就可以轻松编写一个还不错的Dockerfile。

文章里还提到了这个工具可能基于生成式AI，但通篇看下来，觉得更像是基于社区上传的无数的Dockerfile，用大数据实现了一个伪AI（它能够探测你的项目，通过更少的问答来生成文件）

未来AI还将持续改变我们的生活，最起码会让程序员的生活变得更好：）

# **Tip**

### zip_longest

语法：`zip_longest（iter1，iter2，fillval）`

用这个其实就可以将上面的力扣题目改写成更简单的方式：
```python
from itertools import zip_longest

class Solution:
    def mergeAlternately(self, word1: str, word2: str) -> str:
        result = ''
        for v1,v2 in zip(word1, word2, ''):
            result += v1 + v2
        return result
```

# **Share**

