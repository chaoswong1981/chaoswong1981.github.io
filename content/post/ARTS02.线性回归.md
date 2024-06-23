---
title: "ARTS02.最大公约数"
date: 2024-06-22T20:22:13+08:00
draft: false
---

# **Algorithm**

[1768. 交替合并字符串](https://leetcode.cn/problems/merge-strings-alternately/description/?envType=study-plan-v2&envId=leetcode-75)

### 思路：

我的思路其实是很“大白话”的解释：
1. 同时遍历两个字符串
2. 每个字符串都是从头开始取，作为因子
3. 然后检查这个因子是否能够整除整个字符串
4. 如果2个因子能整除各自的字符串，并且2个因子还相同，就保留下来作为公因子 
5. 直到遍历完成，即可得到最大的公因子

### 解法：

```python
class Solution:
  def gcdOfStrings(self, str1: str, str2: str) -> str:
    x, x1, x2 = '', '', ''
    # 同时遍历两个字符串
    for s1, s2 in zip(str1, str2):
      if s1 == None or s2 == None:
        break

      # 得到每个字符串的开头字符串作为因子
      x1 += s1
      x2 += s2

      # 判断得到的因子是否能整除整个字符串
      if self.check_is_substr(str1, x1) and self.check_is_substr(str2, x2):
        # 如果两个因子字符串相同，则记录下来
        if x1 == x2:
          x = x1

    # 返回记录下来的最后因子，这就是两个字符串的最大公因子了
    return x

  def check_is_substr(self, str, x):
    # 计算str是否能被x整除
    step = len(x)
    for i in range(0, len(str), step):
      if str[i:i+step] != x:
        return False
    return True
```

- 执行用时超过 5.25 %
- 消耗内存超过 86.10 %

# **Review**

tinygrad是GeoHot（也叫神奇小子）开源的一个小巧的深度学习框架，这是一篇对2022年对tinygrad介绍的文章：[Introduction to tinygrad: A Simple Deep Learning Framework](https://medium.com/@stackfoss/tinygrad-a-simple-deep-learning-framework-6ac45a54177d)。

文章里介绍了tinygrad的几个比较突出的特性：

- **LLaMA 和 Stable Diffusion 模型的支持**：没错，这么个1000行左右的深度学习框架是可以搭建和运行这两个模型的！
- **傻瓜式优化（Laziness）**：框架能够将你代码里多个矩阵运算，自动合并成一次运算来提高运行效率。
- **神经网络框架**：tinygrad虽小，但却提供了完备的autograd和Tensor支持（这也是作者认为一个深度学习框架最核心的内容），使用这两个核心功能就可以搭建和训练几乎所有的模型。
- **加速器**：框架提供抽象出一个 `accelerator` 的概念，并且提供了CPU、GPU（OpenCL）、C代码（Clang）、LLVM、METAL、CUDA、 Triton、PyTorch的实现。通过使用不同的加速器，就可以调用硬件的力量实现训练的加速。
  >从[tinygrad的文档](https://docs.tinygrad.org/tensor/ops/)可以找到算子定义，其实从这个列表也可以看出自己增加一个新硬件支持要实现哪些算子，也可以了解深度学习框架是如何实现硬件加速的。

总之，这个框架非常具有学习价值：
- 代码量极少，1000行左右
- 不是玩具，能够支持目前世面上几乎所有的深度学习模型：tinyvoice、SD、Transformer……
- 这个框架几乎全是直播的时候写完的，youtube上有全部的录播，可以学习达人的框架设计思想

# **Tip**

`math.gcd`：可求两个数的最大公约数

没做今天的题之前，我对`math`模块的认识是`sin`、`cos`这样的常用函数，没想到这个库其实还有很多便捷的函数可用。

# **Share**

今天的力扣题值得写一写。  
题目提交以后我看了几个靠前的，真的是让我大开眼界，觉得应该多刷刷Leetcode。

比如这个采用递归的方法实现的：
```python
class Solution:
  def gcdOfStrings(self, str1: str, str2: str) -> str:
    m, n = len(str1), len(str2)
    if m < n:
      return self.gcdOfStrings(str2, str1)
    if str1 == str2:
      return str1
    if str1.startswith(str2):
      return self.gcdOfStrings(str2, str1[n:])
    return ''
```
- 执行用时超过 84.32%
- 消耗内存超过 88.01 %

从逻辑上来看，递归真的很像我们自己解题时的思考过程，但在编程的时候，我却总是想不到用递归来实现。

还有这种方法：
```python
class Solution:
  def gcdOfStrings(self, str1: str, str2: str) -> str:
    candidate_len = math.gcd(len(str1), len(str2))
    candidate = str1[: candidate_len]
    if str1 + str2 == str2 + str1:
      return candidate
    return ''
```
- 执行用时超过 99.92 %
- 消耗内存超过 39.07 %

这个思路非常巧妙，不纠结于字符串，而是直接先求出两个字符串长度的最大公约数（不过这里有点取巧，我觉得不应该直接调库函数，可以自己重新实现一下gcd算法），因为如果两个数不存在最大公约数，那就不用求了；如果存在最大公约数，则直接按最大公约数取子串，再测试一下边界用例即可。

对比来看，我的解法执行用时长主要是因为进行了双重循环，而上述的两个解法都是线性的。