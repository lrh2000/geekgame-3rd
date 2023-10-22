# 字数统计程序

## 功能

输入一段文本，返回文本中 UTF-8 字符的个数（放心不会出现什么很奇怪的 Unicode 的）。

哇！就像 Python 的 `len(...)` 函数一样！

## 笑点解析

### 解析《笑点解析》

仔细想想，不难看出，我们可以把所有的字符都看成是一样的——反正也只是统计它们出现的次数，它们具体是什么并不重要。

比如：

```
Hello!
Hi!
```

是 10 个字符，而：

```
**********
```

也是十个字符，它们其实没什么区别。

于是，我们实际要做的只是把如下 unary 计数法表示的数：

```
**********
```

转换成十进制的：

```
10
```

一大堆 \* 是 unary 计数法，一堆 1 也是 unary 计数法，于是实际上转换的结果相当于一堆 1 相加的结果。

考虑 10 以内的数，比如 5 个 1：

```
11111
```

就是 1！个 5！直接把 1 替换成 5 就完事了。

同理，10 个 1 就是 1 个 10。那 15 个 1 怎么办？

```
111111111111111
```

应该先把 10 个 1 替换成 10，然后剩下 5 个 1（和之前的 0）：

```
1011111
```

之后应该把 0 去掉，然后把 11111 替换成 5，实际上就是把 011111 替换成 5。

那 25 个 1 呢？先把前 10 个 1 替换成 10：

```
10111111111111111
```

再把之后出现的连续的 10 个 1 替换成 10……

```
101011111
```

其实你已经发现，由于 10 后面的那个 10，我们每次把 10 个 1 替换成 10 之后，字符串中间都会多出个 0，这个 0 在之后应该被去掉。

否则，如果不让结果多个 0，就无法实现分隔个位数和十位数的结果，所以还是留着 0 把它当作一个分隔符比较好。

于是，如果结果是：

```
11011111
```

则这段文本更容易被替换成 25。

那不如试着参考 15 个 1 的情况，把 0 之后 n 个 1 替换为 1，而非单纯把一串 1 替换成某个数——这样的话初始的文本之前应该有足够多的 0。

比如对于 25 个 1 的情况：

```
1111111111111111111111111
```

先在之前补 25 个 0：

```
00000000000000000000000001111111111111111111111111
```

然后把 0 后面 10 个 1 替换成 10：

```
00000000000000000000000010111111111111111
```

再做一次同样的替换：

```
00000000000000000000000011011111
```

这时没有连续的 10 个 1 了，剩下的刚好是被一分为二的两个 1 和五个 1，分别代表十位和个位。把 011 替换成 2，011111 替换成 5：

```
0000000000000000000000025
```

最后去掉所有前导 0 就是我们需要的结果。

当然，实际情况下，我们需要在替换时加入一些其他的分隔符，比如 101 个 1，把所有 10 个 1 的情况替换完成之后会变成（省略之前的一大堆 0）：

```
01001
```

这个时候如果再重复把 01 替换成 1 的话结果会变成：

```
11
```

而不是 101，所以只用 0 做分隔符是不太够的。

此外，对于空串，因为它并不包含任何字符，所以自然不会被替换出一堆 0 和 1，更不会凭空多出个 0，这种情况需要特判一下子。

### 好啊！来啊！

首先把所有字符都替换成 1。同时为了让字符串里出现足够多的 0，我们实际上应该把它们替换成 01。

注意对换行符的处理：

```
把【.|\n】替换成【01】喵
```

然后想办法把这堆 0 挪到字符串最开头，也就是看到 10 就把它替换成 01：

```
重复把【10】替换成【01】喵
```

这个时候就可以开始按照我们之前说的道理来进行替换了，注意除了 0 之外，再插入一个别的分隔符，比如 `_`：

```
重复把【01{10}】替换成【10】喵
重复把【01{9}】替换成【_9】喵
重复把【01{8}】替换成【_8】喵
重复把【01{7}】替换成【_7】喵
重复把【01{6}】替换成【_6】喵
重复把【01{5}】替换成【_5】喵
重复把【01{4}】替换成【_4】喵
重复把【01{3}】替换成【_3】喵
重复把【01{2}】替换成【_2】喵
重复把【01{1}】替换成【_1】喵
```

这么处理完之后，你会得到一个包含一些前导 0，以及以 `_` 分隔各位数的数字串。

此时去掉前导 0 和分隔符：

```
把【_|^0*】替换成【】喵
```

最后，对空串进行特殊处理：

```
把【^$】替换成【0】喵
```

当当！就是这样喵！

## 别忘了

关注 MaxXing 喵，关注 MaxXing……

谢谢喵