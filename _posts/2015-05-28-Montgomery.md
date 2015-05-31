---
layout: post
title: 蒙哥马利幂模运算
description: 介绍了蒙哥马利幂模运算的算法思想和流程
keywords: 蒙哥马利幂模运算, Montgomery, RSA
---


蒙哥马利（Montgomery）幂模运算是计算`a^b%k`的一种方式。而`a^b%k`的计算是RSA加密算法的核心之一。其实现快速实现的方式是将模幂运算转换为乘幂运算。

## 1.相关的一点数学知识

数论中的两个基本定理：

1. __(a+b)%n = (a%n+b%n)%n__
2. __(a\*b)%n = (a%n\*b%n)%n__

要理解这两个定理其实是很简单，甚至不需要过于教科书式的证明过程。a+b要理解为a个１加上b个１，而a*b要理解为a个b相加或者b个a相加。在此不再多述其细节。

## 2.一个简单的例子

我们来看一下如何计算 __a^15%n__ 这个例子：

我们可以令 res, A1, A2, A3, A4分别等于如下结果：

* A1 = a%n
  <br>res = A1 = a%n
* A2 = A1\*A1%n = (a%n \* a%n)%n = a^2%n
  <br>res = res\*A2%n = (a%n \* a^2%n)%n = a^3%n (用定理02.)
* A3 = A2\*A2%n = (a^2%n \* a^2%n)%n = a^4%n
  <br>res = res\*A3%n = (a^3%n \* a^4%n)%n = a^7%n
* A4 = A3\*A3 = (a^4%n \* a^4%n)%n = a^8%n
  <br>res = res\*A4%n = (a^7%n \* a^8%n)%n = __a^15%n__

Bingo!!!

## 3. 用python的一个简单实现

在具体实现之前，你可能对于以上所述的累加方式不太理解，如果你想明白这之后的一点道理的话，可以看一下我之前的[一篇文章](http://axhiao.github.io/2015/03/20/an-interesting-and-further-power-algorithm.html),在那里你或许会发现一些对你的理解有帮助的东西。

有了上述的整个描述过程，我就直接po出代码了: now its just an easy thing. enjoy it.

<pre>
<code>
#coding=utf-8

def int2baseBinary(x):
    '''
    convert a integer into a binary list in reverse order
    '''
    binList = []
    while x != 0:
        binList.append(x%2)
        x = x >> 1
    return binList


def modExp(a, d, n):
    '''
    compute a^d%n with Montgomery
    '''
    res = 1
    lt = int2baseBinary(d)
    for i in lt:
        if i:
            res = (res * a) % n
        a = (a * a) % n
    return res


if "__main__" == __name__:
    print modExp(2790, 2753, 3233)
</code>
</pre>



