---
title: "Python数据处理.关于matplotlib绘制直方图偏移的问题"
date: 2018-12-02T12:46:25+08:00
lastmod: 2018-12-02T12:46:25+08:00
draft: false
tags: [Python, 数据处理]
categories: [Python 数据处理]
author: "Dereen"
comment: true
toc: true
autoCollapseToc: false
---

在使用pyplot绘制直方图的时候我发现了一个问题，在给函数.hist()传参的时候，如果传入的组数不是刚刚好（就是说这个组数如果是使用(最大值-最小值)/组距计算出来，而这个数字不是整除得来而是取整得来的话），图像就会产生偏移现象。

看下面这段代码：绘制IMDB排行前1000电影的时长分布直方图
```python
# coding=utf-8
from matplotlib import pyplot as plt
import pandas as pd
# 数据准备
file_path = "./IMDB-Movie-Data.csv"

df = pd.read_csv(file_path)
runtime_data = df["Runtime (Minutes)"]
# 计算组数
max_runtime = max(runtime_data)
min_runtime = min(runtime_data)
num_bin = int((max_runtime-min_runtime)//6)
# 配置图形参数
plt.figure(figsize=(20, 8), dpi=80)
plt.grid(alpha=0.5)
# 绘图
plt.hist(runtime_data, num_bin)
plt.xticks(range(min_runtime, max_runtime+6, 6))
plt.show()
```
结果如下：
![](https://i.loli.net/2018/09/25/5ba9bd4caad34.png)
- 产生这个问题的原因就在于，在这个程序中`max_runtime-min_runtime`的值是125，不能被6整除，所以产生了偏移。
如果我们将上述代码中的`(max_runtime-min_runtime)//6`和`plt.xticks(range(min_runtime, max_runtime+6, 6))`中的6更换为一个能被125整除的数，比如5，结果会是如何呢？
![](https://i.loli.net/2018/09/25/5ba9befce735c.png)
- 我们可以看到问题解决了，偏移消失了，但是这并不是解决问题的根本办法，如果我们就是要用6作为组距而不想偏移呢？
**我们可以传入一个列表参数来解决这个问题**
```python
# coding=utf-8
from matplotlib import pyplot as plt
import pandas as pd
# 数据准备
file_path = "./IMDB-Movie-Data.csv"

df = pd.read_csv(file_path)
runtime_data = df["Runtime (Minutes)"]
# 将传参从组数改为传入列表
max_runtime = max(runtime_data)
min_runtime = min(runtime_data)
	
plt.figure(figsize=(20, 8), dpi=80)

plt.hist(runtime_data, range(min_runtime, max_runtime+6, 6))
plt.grid(alpha=0.5)
plt.xticks(range(min_runtime, max_runtime+6, 6))
plt.show()
```
结果如图：
![](https://i.loli.net/2018/09/25/5ba9c056abec9.png)