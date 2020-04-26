---
title: "Python数据处理.Pandas之索引"
date: 2018-11-30T12:46:25+08:00
lastmod: 2018-11-30T12:46:25+08:00
draft: false
tags: [Python, 数据处理]
categories: [Python 数据处理]
author: "Dereen"
comment: true
toc: true
autoCollapseToc: false
---

Pandas的标签处理需要分成多种情况来处理,Series和DataFrame根据标签索引数据的操作方法是不同的,单列索引和双列索引的操作方法也是不同的。

## 单列索引

```python
In [2]: import pandas as pd

In [3]: import numpy as np

In [4]: df = pd.DataFrame(np.ones((2, 4)), index=list("AB"), columns=list("abcd"))

In [5]: df.iloc[0,0]=100

In [6]: df
Out[6]: 
       a    b    c    d
A  100.0  1.0  1.0  1.0
B    1.0  1.0  1.0  1.0
```

### reindex所插入的标签如果是原来的标签中没有的，就会将该行的值全部置为NaN

```python
In [7]: df.reindex(["A", "f"])
Out[7]: ssss
       a    b    c    d
A  100.0  1.0  1.0  1.0
f    NaN  NaN  NaN  NaN

In [8]: df
Out[8]: 
       a    b    c    d
A  100.0  1.0  1.0  1.0
B    1.0  1.0  1.0  1.0
```

### 使用index修改标签

```python
In [9]: df.index = ["a", "b"]

In [10]: df
Out[10]: 
       a    b    c    d
a  100.0  1.0  1.0  1.0
b    1.0  1.0  1.0  1.0
```

### 使用set_index将某一列变为标签

```python
In [11]: df.set_index("a")
Out[11]: 
         b    c    d
a
100.0  1.0  1.0  1.0
1.0    1.0  1.0  1.0

In [12]: df
Out[12]: 
       a    b    c    d
a  100.0  1.0  1.0  1.0
b    1.0  1.0  1.0  1.0
# 使用drop参数控制将某一列作为索引后是否删除原数据
In [13]: df.set_index("a", drop=False)
Out[13]: 
           a    b    c    d
a
100.0  100.0  1.0  1.0  1.0
1.0      1.0  1.0  1.0  1.0
# 使用unique函数可以去除重复值
In [14]: df.set_index("b", drop=False).index.unique()
Out[14]: Float64Index([1.0], dtype='float64', name='b')

In [15]: df.set_index("b", drop=False).index
Out[15]: Float64Index([1.0, 1.0], dtype='float64', name='b')

In [16]: len(df.set_index("b", drop=False).index.unique())
Out[16]: 1
```

## 双列索引

```python
In [17]: df.set_index(["a","b"])
Out[17]: 
             c    d
a     b
100.0 1.0  1.0  1.0
1.0   1.0  1.0  1.0
# levels这个列表中含有两个列表,分别是双列索引的每一列
In [18]: df.set_index(["a","b"]).index
Out[18]: 
MultiIndex(levels=[[1.0, 100.0], [1.0]],
           labels=[[1, 0], [0, 0]],
           names=['a', 'b'])
           
In [19]: a = pd.DataFrame({'a': range(7),'b': range(7, 0, -1),'c': ['one','one','one','two','two','two', 'two'],'d': list("hjklmno")})

In [20]: a
Out[20]: 
   a  b    c  d
0  0  7  one  h
1  1  6  one  j
2  2  5  one  k
3  3  4  two  l
4  4  3  two  m
5  5  2  two  n
6  6  1  two  o

In [21]: b = a.set_index(["c","d"])

In [22]: b
Out[22]: 
       a  b
c   d
one h  0  7
    j  1  6
    k  2  5
two l  3  4
    m  4  3
    n  5  2
    o  6  1

In [23]: c = b["a"]

In [24]: c
Out[24]: 
c    d
one  h    0
     j    1
     k    2
two  l    3
     m    4
     n    5
     o    6
Name: a, dtype: int64
```

### 双列索引取值

```python
In [25]: c["two"]["l"]
Out[25]: 3

In [26]: c["one"]
Out[26]: 
d
h    0
j    1
k    2
Name: a, dtype: int64

In [27]: d = a.set_index(["d","c"])

In [28]: d = d["a"]


In [43]: d
Out[43]: 
d  c
h  one    0
j  one    1
k  one    2
l  two    3
m  two    4
n  two    5
o  two    6
Name: a, dtype: int64
# 对于索引数少的列在后的情况,如果直接取会发生错误
In [44]: d["one"]
---------------------------------------------------------------------------
KeyError                                  Traceback (most recent call 
...
KeyError: 'one'
```
### swaplevel()函数进行标签列换位

```python
In [45]: d.swaplevel()
Out[45]: 
c    d
 one  h    0
     j    1
     k    2
 two  l    3
     m    4
     n    5
     o    6
Name: a, dtype: int64

In [46]: d = d.swaplevel()

In [47]: d["one"]
Out[47]: 
d
h    0
j    1
k    2
Name: a, dtype: int64

In [48]: b
Out[48]: 
       a  b
c   d      
 one h  0  7
    j  1  6
    k  2  5
 two l  3  4
    m  4  3
    n  5  2
    o  6  1
```

### 对于DataFrame类型数组的双列索引,取值时应该加上loc或iloc

```python
In [49]: b.loc["one"]
Out[49]: 
   a  b
d      
h  0  7
j  1  6
k  2  5

In [51]: d.loc["two"].loc["m"]
Out[51]: 4

```