---
title: "Python数据处理.Pandas之分组"
date: 2018-11-15T12:46:25+08:00
lastmod: 2018-11-15T12:46:25+08:00
draft: false
tags: [Python, 数据处理]
categories: [Python 数据处理]
author: "Dereen"
comment: true
toc: true
autoCollapseToc: false
---

假如我们现在有这样一组数据：星巴克在全球的咖啡店信息，如下图所示。数据来源：[starbucks_store_locations](https://www.kaggle.com/starbucks/store-locations)。我们想要统计中国每个城市的星巴克商店的数量，那我们应该怎么做呢？


<!--more-->


![starbucks_store_worldwide](https://i.loli.net/2018/10/12/5bc04fa839a60.png)

在pandas中，为我们提供了一个处理分组问题的函数`groupby()`:

```python
# coding=utf-8

import pandas as pd

file_path = "./starbucks_store_worldwide.csv"
# 设置最多输出20行
pd.set_option('display.max_rows',20)

df = pd.read_csv(file_path)
df_CN = df[df["Country"]=="CN"]

grouped_CN = df_CN.groupby(by="City")
print(grouped_CN["Brand"].count())

```

结果如下：

```
City
Admiralty          2
Causeway Bay       5
Central            1
Chaiwan            1
Changshu           1
Changzhou          1
Fortress Hill      1
Hangzhou           2
Hong Kong        104
Jiaxing            2
                ... 
长春市               10
长沙市               26
阳江市                1
青岛市               28
靖江市                2
鞍山市                3
马鞍山                3
高邮市                1
黄石市                1
龙岩市                2
Name: Brand, Length: 197, dtype: int64

Process finished with exit code 0
```

假如我们不想先提取中国的数据再分组，而是想一步到位应该怎么做呢？

```python
# coding=utf-8

import pandas as pd

file_path = "./starbucks_store_worldwide.csv"
# 设置最多输出20行
pd.set_option('display.max_rows',20)
df = pd.read_csv(file_path)

grouped = df[df["Country"] == "CN"]["Brand"].groupby(by=[df["Country"], df["City"]]).count()

print(grouped)
```

结果如下：

```
Country  City         
CN       Admiralty          2
         Causeway Bay       5
         Central            1
         Chaiwan            1
         Changshu           1
         Changzhou          1
         Fortress Hill      1
         Hangzhou           2
         Hong Kong        104
         Jiaxing            2
                         ... 
         长春市               10
         长沙市               26
         阳江市                1
         青岛市               28
         靖江市                2
         鞍山市                3
         马鞍山                3
         高邮市                1
         黄石市                1
         龙岩市                2
Name: Brand, Length: 197, dtype: int64
```

我们可以发现，与上面结果不同的是，这次的索引成了2列，不但有city，还有Country。这是因为我们再用group分组的时候传入了2个参数进去。

**这里需要注意，最终的结果其实是包含双列索引的单列1维数组，类型是`pandas.core.series.Series`**