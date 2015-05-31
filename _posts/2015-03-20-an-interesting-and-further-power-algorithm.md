---
layout: post
title: 求幂算法及其背后隐藏的数学
description: 一个高效的求幂算法及其引出的一点数学知识，你可能被需要了解一点离散数学的知识哦。
keywords: 求幂算法,离散数学
---


<!--

<pre class="js" name="colorcode">
$ ls
$ mkdir key_backup //创建备份文件夹
$ cp id_rsa* key_backup //移动你的 key 文件到备份文件夹
$ mr id_rsa*
</pre>

-->

本篇文章我不仅要告诉你怎样高效的求一个数的幂，而且还会来引导来发现，在这个高效算法的背后隐藏着怎样的数学原理。我们常常疑问，一个著名的高效的算法是怎样被想出来的，有时候，其实是我们的数学知识不是那么丰富，如果你可以有足够的数学本领来作为你的理论，或许很多算法你也可以第一次想出来。惊叹数学的伟大与奥妙！(What a pity is i'm not good at math. --! But I'm really interested in it.)

####1.快速求幂算法

言归正传，我们来先说这个高效的求幂算法(the power alogrithm)。这个算法是由高德纳([Donald Knuth](http://en.wikipedia.org/wiki/Donald_Knuth))在其[The Art of Computer Programming](http://www-cs-faculty.stanford.edu/~uno/taocp.html)一书中提到的。

求一个数x的n次幂，我们可以这样简单表示：__x^n__。最简单的就是我们可以用x乘以自己n次即可。高效一点可能也就是我们在学习算法导论的时候分治法时候用到的方法。但是，今天我们既然有了这篇文章，可能会向你介绍一个更为高效的方法。

假设我们想计算`7^6`，这里x=7，n=6，我们把6表示成二进制形式`110`，我们可以知道的是`7^6=7^4*7^2`，而`7^4`和`7^2`什么关系呢？当然`7^4=7^2*7^2`。如果有`7^8`呢？当然`7^8=7^4*7^4`。任何一个整数幂n当然我们可以将其分解为二进制表示，这样我们就可以对这个二进制进行遍历，碰到1的话表示需要将其累乘到最终结果变量上，并且每次循环中维持一个变量始终是自身的平方。我们来看一下代码（python）：

<pre class="python" name="colorcode">
<code>
	    def pow(x, n):
	        y = 1;
	        while (True):
	            t = n % 2
	            n = int(n / 2)
	            if (1 == t):
	                y = y * x
	            if ( 0 == n):
	                break
	            x = x * x
	        return y
</code>
</pre>


我们调用的方式就是:
```
result = pow(2,1024)
```
这个算法在计算非常大的数的时候是十分高效的，高效的原因就是每次都是指数级增长，而且前面的计算结果后面都会被用到，没有浪费。

####2.需要抽象一些

到现在为止，你可能最多也就感慨这个算法是多么被精妙的设计出来。现在我们将其抽象出来，并引入一些数学原理，关于离散数学的一点点知识就好，你不用担心是多么高深的数学知识，因为我也只是一个掌握基本数学知道的人( embarrassing)。

我们可以看到求一个数的幂实际上和一个数**自乘**多次是等价的，我们也可以看到乘法实际上等价于**自加**多次。举个例子2*5能够这样被计算2+2+2+2+2。我们可以做的是把这样一个算法转换为一种更普遍的形式使它能同时应用在乘法和加法上，而且你只需要改变很少的东西即可。

在当前的设计中，我们创建**y**作为乘法的主体，并且设置为1。我们如果要把算法用在加法上，我们需要把**y**设置为0。第二步我们要提供一个函数（准确的说是一种运算）给我们的算法，它能够做我们想要的运算(比如加法和乘法，甚至字符串拼接)。因此我们会传递一个充当**二元运算**角色的函数。这个函数需要遵循以下原则：a·（ b · c ） = (a · b ) · c --这个规则应该叫结合律吧? 还要求返回结果类型应该和输入是一致的。幸运的是，加法和乘法都满足结合律，这样我们就可以把这个它传递给我们的pow算法作为新的基本**运算符**。我们来看下新的算法变成什么样子了：

<pre >
    def pow2(x, n, id, f):
        y = id;
        while (True):
            t = n % 2
            n = int(n / 2)
            if (1 == t):
                y = f(y , x)
            if ( 0 == n):
                break
            x = f(x , x)
        return y
</pre>

我们可以这样简单的调用他：

```
result = pow2(2,10,1,lambda x,y: x*y)
```

唯一需要注意的是，我们传进去的运算必须是满足该运算下的结合律，我们不能传入减法的原因就是因为该运算不满足结合律。

####3.更多的抽象

在前面两部分的基础上，现在是时候向你展示一些跟数学相关的东西了。如果你还稍微记得一些离散数学中的知识的话，你一定对下面的概念有印象：

[半群](http://zh.wikipedia.org/wiki/%E5%8D%8A%E7%BE%A4)(Semigroup)：集合S和其上的二元运算符·，满足封闭性，即S×S→S，并且若·满足结合律，即∀x,y,z∈S，有(x·y)·z=x·(y·z)，则称有序对(S,·)为半群。若S上的运算·有幺元(单位元)(identity element),即：∃e∈S，使得∀s∈S，e·s=e·1=e。则S称为幺半群（独异点）(Monoid)。(你一定想不到在告别离散数学多年后会在这里碰到他吧？不过他确实很有用，只是我们没有用好它，向Alan致敬！)

好了，我们现在来看下，我们都已经抽象到这里了，能否把抽象出来的原理再次应用到实践中去，有什么例子可以用我们的理论的吗？如果一个字符串，我们想重复n次，怎么做？(别再去想for循环叠加了，因为我们现在有了更先进的工具了)这里我们使用能够`string append`作为二元操作符，而且**空白字符串(empty string)**作为单位元素。
<pre>
<code>
def repeat(s, n):
    return pow2(s, n, "", lambda x,y: x+y)
</code>
</pre>
现在我们再来考虑一下**数组(arrays)**(或者其他语言称为**列表(list)**)。我们想把一个数组复制n次。这里空数组会是我们的单位元素（幺元）
<pre>
<code>
def repeat_lst(s,n):
    return pow2(s,n,[],lambda x,y: x+y)
</code>
</pre>
我们可以简单的调用：

```
result = repeat_lst([1,2],6)
```

####4.延伸阅读

+ 这里的快速求幂算法是基于[TAOCP](http://www-cs-faculty.stanford.edu/~uno/taocp.html)中，卷而的4.63节。
+ 所有的关于工作原理的解答都可以在**TAOCP**或者在这本书[《A Computational Introduction to Number Theory and Algebra》](http://shoup.net/ntb)上找到，这本书的免费PDF可以在作者主页上下载到。浏览章节："Computing with large integers - The repeated squaring algorithms"
+ 如果想学习这个算法的一些用法或者想知道更多这个算法背后的理论，请查阅这本叫做[《Elements of Porgramming》](http://www.elementsofprogramming.com)，这本书中文名字叫做[__编程原本__](http://pan.baidu.com/s/1sjDJe3b)。这本书非常了不起，它定义了不同类型的函数和使用类型系统确定函数是否是可结合的，二元的等等。作者是C++STL的设计者,所以这本书的内容可能会比较理论化,然后它能够直接应用在面向对象编程(OOP)。
+ 这里也还要推荐一本不错的书，其中也提及到了本文所述的一些知识。[Structure and Interpretation of Computer Programs](http://mitpress.mit.edu/sicp/full-text/book/book-Z-H-4.html#%_toc_start)

`print "thank you"`




