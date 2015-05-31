---
layout: post
title: 扩展欧几里得算法求模反元素
description: 介绍扩展欧几里得算法以及其对模反元素的求法
keywords: 欧几里得算法, 扩展欧几里得算法,模反元素,extended Euclidean algorithm,模乘逆元,modular multiplicative inverse
---


## 1.　欧几里得算法
　　　欧几里得算法你如果没用听过，那么辗转相除法你一定知道吧，其实两者是一样的。我们用一种简单并且有效的方式来表示：对于不完全为０的非负整数a,b，用gcd(a,b)来表示a,b的最大公约数。那么欧几里得算法则可以表示为：gcd(a,b)=gcd(b,a%b)。

python代码如下：

<pre>
<code>
	def gcd(a,b):
	    if b==0:
		return a
	    else:
		return gcd(b,a%b)
</code>
</pre>

## 2.扩展欧几里得算法

根据１所述，设a,b的最大公约数为gcd(a,b),则存在整数x,y使得gcd(a,b)＝ax+by。扩展欧几里得则是用来求出这个x,y的，并且同时可以求出a,b的最大公约数。

首先来看一下扩展欧几里得算法实现背后的原理。我们现在假设有n和m且m<n，需要求解nx+my=gcd(n,m):

* `当m==0时`，gcd(n,m)=n，此时x=1,y=0
* `当m!=0时`，设
	       \[nx_1+my_1=gcd(n,m)\]
	       \[ mx_2+(n\%m)y_2=gcd(m,n\%m)\]
   根据欧几里得定理知道，gcd(n,m)=gcd(m,n%m)，所以有
　　　\[nx_1+my_1 = mx_2+(n\%m)y_2=mx_2+(n-(n//m)*m)y_2\]
   根据等式两边的各项系数相等原则有，
   $$
      \begin{cases}
       x_1=y_2 \\
       y_1=x_2-(n/m)*y_2
      \end{cases}
   $$

这样我们就得到了求解 $x_1,y_1$的方法：$x_1,y_1$的值基于$x_2, y_2$．如此循环递归下去，必然有m==0的时刻，所以递归可以触底结束．

python代码如下：

<pre>
  <code>
    def ext_Euclid(n, m):
        if (m == 0):
            return 1, 0
        else:
            x, y = ext_Euclid(m, n%m)
            x, y = y, (x -(n//m)*y)
            return x, y
  </code>
  </pre>
　

## 3.求模反元素

首先说一下什么是模反元素，　一个整数a模n的模反元素假设是x，则x使下列式子成立
\[ax\equiv 1(mod\ n)　\]
而这个式子则等价于 ax-1=kn,进一步等价于ax-kn=1，现在我们知道了a,n的值，要求x的值，令y= -k,则变为$ax+ny=1$.可以看到这个式子与扩展欧几里得算法要解决的方程在形式上基本是一致的，唯一不同之处在于要有gcd(a,n)=1这个先决条件．因此要求a与n要互质，否则模反元素不存在．
到这里我们就可以直接用扩展欧几里得算法求解一个元素关于某个整数模的模反元素了．

补充说明一点的是，这篇文章会出现是因为我在研究RSA(to be continued...)，在RSA中，求解模反元素的时候要避免得到一个负值，处理的办法是：x'=(x+n)%n，如此得到一个做小的正值．









