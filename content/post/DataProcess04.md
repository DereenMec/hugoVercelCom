---
title: "Python数据处理.Pandas"
date: 2018-11-14T12:46:25+08:00
lastmod: 2018-11-14T12:46:25+08:00
draft: false
tags: [Python, 数据处理]
categories: [Python 数据处理]
author: "Dereen"
comment: true
toc: true
autoCollapseToc: false
---

*pandas* is an open source, BSD-licensed library providing high-performance, easy-to-use data structures and data analysis tools for the Python programming language.这段话是pandas官方网站对于Pandas的介绍，翻译过来就是：pandas是一个开源的，BSD许可的库，为Python编程语言提供高性能，易于使用的**数据结构和数据分析工具**。


<!--more-->

## Series一维数组

### 创建一个pandas一维数组

`pandas.Series()`创建的是带有标签的一维数组。例如：

```python
In [1]: import numpy as np

In [2]: import string

In [3]: import pandas as pd
# 第一种创建方法，传入两个列表
In [4]: pd.Series([1, 2, 3, 4, 5, 6], index =list("abcdef"))
Out[4]:
a    1
b    2
c    3
d    4
e    5
f    6
dtype: int64
# 第二种创建方法，传入一个字典
In [5]: temp_dict = {"name":"xiaohong", "age":"13", "address":"binxian"}

In [6]: pd.Series(temp_dict)
Out[6]:
name       xiaohong
age              13
address     binxian
dtype: object
# 第三种创建方法，利用string.ascii_uppercase生成一个字典，再传入生成一个带标签数组
In [7]: a = {string.ascii_uppercase[i]:i for i in range(10)}

In [8]: a
Out[8]:
{'A': 0,
 'B': 1,
 'C': 2,
 'D': 3,
 'E': 4,
 'F': 5,
 'G': 6,
 'H': 7,
 'I': 8,
 'J': 9}

In [9]: pd.Series(a)
Out[9]:
A    0
B    1
C    2
D    3
E    4
F    5
G    6
H    7
I    8
J    9
dtype: int64
# 当有标签的地方没有值时，该位置的元素值就被置为NaN
In [10]: pd.Series(a, index=list(string.ascii_uppercase[5:15]))
Out[10]:
F    5.0
G    6.0
H    7.0
I    8.0
J    9.0
K    NaN
L    NaN
M    NaN
N    NaN
O    NaN
dtype: float64

```

###  pandas数组元素类型修改

要修改pandas数组中元素的类型与numpy中是一样的：

```python
In [11]: pd.Series(a).astype("float64")
Out[11]:
A    0.0
B    1.0
C    2.0
D    3.0
E    4.0
F    5.0
G    6.0
H    7.0
I    8.0
J    9.0
dtype: float64

```

### 取一维数组的值或标签

这个操作大体也与numpy中相应的操作相同：

```python
In [16]: a = pd.Series(a)

In [17]: a
Out[17]:
A    0
B    1
C    2
D    3
E    4
F    5
G    6
H    7
I    8
J    9
dtype: int64

In [18]: a[0:7]
Out[18]:
A    0
B    1
C    2
D    3
E    4
F    5
G    6
dtype: int64

In [19]: a[[1,3,4]]
Out[19]:
B    1
D    3
E    4
dtype: int64

In [20]: a[["A","E","G"]]
Out[20]:
A    0
E    4
G    6
dtype: int64

In [21]: a[a>4]
Out[21]:
F    5
G    6
H    7
I    8
J    9
dtype: int64

```

### pandas一维数组的组成

```python
In [22]: a.index
Out[22]: Index(['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J'], dtype='object
')

In [23]: a.values
Out[23]: array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9], dtype=int64)

In [24]: type(a.index)
Out[24]: pandas.core.indexes.base.Index

In [25]: type(a.values)
Out[25]: numpy.ndarray

```

**由上面的代码可以看出，pandas Series一维数组实际上是由一个numpy一维数组和类型为`pandas.core.indexes.base.Index`的一维数组拼成的。**

## DataFrame多维数组

### 创建DataFrame多维数组

```python
# 要创建一个DataFrame数组，我们只需给pd.DataFrame（）中传入一个数组即可
In [1]: import pandas as pd

In [2]: import numpy as np

In [3]: pd.DataFrame(np.arange(0, 12).reshape(3, 4))
Out[3]: 
   0  1   2   3
0  0  1   2   3
1  4  5   6   7
2  8  9  10  11
# 既然DataFrame是带标签的数组，那我们如何定义它的标签？看下面的代码：
In [6]:  a = pd.DataFrame(np.arange(12).reshape(3,4),index=list("abc"),columns=
   ...: list("defg"))
# 从结果可以看出：index插入的是行标签，columns插入的是列标签
In [7]: a
Out[7]:
   d  e   f   g
a  0  1   2   3
b  4  5   6   7
c  8  9  10  11
# 除了传入数组，我们也可以传入字典作为参数，来创建一个DataFrame数组

In [9]: d1 = {"name":["chenpi","dufu"],"age":[1,3],"color":["orange","white"]}

In [10]: pd.DataFrame(d1)
Out[10]:
     name  age   color
0  chenpi    1  orange
1    dufu    3   white

```

### 查看多维数组的参数

```python
In [13]: d2 = pd.DataFrame(d1)

In [14]: d2
Out[14]: 
     name  age   color
0  chenpi    1  orange
1    dufu    3   white
# 查看行索引
In [15]: d2.index
Out[15]: RangeIndex(start=0, stop=2, step=1)
# 查看列索引
In [16]: d2.columns
Out[16]: Index(['name', 'age', 'color'], dtype='object')
# 查看值，可以看到DataFrame数组的值其实就是一个numpy同维数组
In [17]: d2.values
Out[17]: 
array([['chenpi', 1, 'orange'],
       ['dufu', 3, 'white']], dtype=object)
# 查看形状
In [18]: d2.shape
Out[18]: (2, 3)
# 查看类型
In [19]: d2.dtypes
Out[19]: 
name     object
age       int64
color    object
dtype: object
# 查看维度
In [20]: d2.ndim
Out[20]: 2

```

## 数组切片

### 使用head()、tail()方法取头或尾连续多行

```python
# 取第一行
In [42]: d2.head(1)
Out[42]: 
     name  age   color
1  chenpi    1  orange
# 取前二行
In [43]: d2.head(2)
Out[43]: 
     name  age   color
1  chenpi    1  orange
2    dufu    3   white
# 如果不给head()指定参数则默认取前5行，这里因为整个数组只有2行
In [45]: d2.head()
Out[45]: 
     name  age   color
1  chenpi    1  orange
2    dufu    3   white
# 用tail()方法从后往前取行
In [46]: d2.tail(1)
Out[46]: 
   name  age  color
2  dufu    3  white
# 如果不想要索引可以这样取（values后面的参数同numpy中相同）
In [49]: d2.values[1]
Out[49]: array(['dufu', 3, 'white'], dtype=object)

```

### 利用列表的切片功能

```python
# 先建立一个数组如下
In [71]: t1 = pd.DataFrame(np.arange(100).reshape(10, 10), index=list("ABCDEFGHI
    ...: J"), columns=list("KLMNOPQRST"))
In [72]: t1
Out[72]: 
    K   L   M   N   O   P   Q   R   S   T
A   0   1   2   3   4   5   6   7   8   9
B  10  11  12  13  14  15  16  17  18  19
C  20  21  22  23  24  25  26  27  28  29
D  30  31  32  33  34  35  36  37  38  39
E  40  41  42  43  44  45  46  47  48  49
F  50  51  52  53  54  55  56  57  58  59
G  60  61  62  63  64  65  66  67  68  69
H  70  71  72  73  74  75  76  77  78  79
I  80  81  82  83  84  85  86  87  88  89
J  90  91  92  93  94  95  96  97  98  99
# 取前5行
In [73]: t1[:5]
Out[73]: 
    K   L   M   N   O   P   Q   R   S   T
A   0   1   2   3   4   5   6   7   8   9
B  10  11  12  13  14  15  16  17  18  19
C  20  21  22  23  24  25  26  27  28  29
D  30  31  32  33  34  35  36  37  38  39
E  40  41  42  43  44  45  46  47  48  49
# 取前5行“Q”列
In [74]: t1[:5]["Q"]
Out[74]: 
A     6
B    16
C    26
D    36
E    46
Name: Q, dtype: int64
# 取“Q”列数据
In [79]: t1["Q"]
Out[79]: 
A     6
B    16
C    26
D    36
E    46
F    56
G    66
H    76
I    86
J    96
Name: Q, dtype: int64
# 单列数组的类型是Series一维数组
In [80]: type(t1["Q"])
Out[80]: pandas.core.series.Series

```

### loc()函数切片

#### `df.loc()`通过标签索引行数据

```python
# 使用loc函数进行切片与numpy中切片的方式是相同的
In [86]: t1.loc["D", "K"]
Out[86]: 30
# 取“C”行
In [87]: t1.loc["C", :]
Out[87]: 
K    20
L    21
M    22
N    23
O    24
P    25
Q    26
R    27
S    28
T    29
Name: C, dtype: int64
# 取“O”列
In [88]: t1.loc[:, "O"]
Out[88]: 
A     4
B    14
C    24
D    34
E    44
F    54
G    64
H    74
I    84
J    94
Name: O, dtype: int64
# 取多行多列
In [89]: t1.loc[["D", "H"], ["O", "Q"]]
Out[89]: 
    O   Q
D  34  36
H  74  76
# 取“A”到“J”行，（这里需要注意的是，与一般的切片不同，这里的“J”是可以取到的）
In [90]: t1.loc["A":"J"]
Out[90]: 
    K   L   M   N   O   P   Q   R   S   T
A   0   1   2   3   4   5   6   7   8   9
B  10  11  12  13  14  15  16  17  18  19
C  20  21  22  23  24  25  26  27  28  29
D  30  31  32  33  34  35  36  37  38  39
E  40  41  42  43  44  45  46  47  48  49
F  50  51  52  53  54  55  56  57  58  59
G  60  61  62  63  64  65  66  67  68  69
H  70  71  72  73  74  75  76  77  78  79
I  80  81  82  83  84  85  86  87  88  89
J  90  91  92  93  94  95  96  97  98  99

```

#### `df.iloc()`通过位置索引行数据

```python
# 使用iloc进行切片与numpy中完全相同
In [91]: t1.iloc[[1, 3, 5], [2, 4, 6]]
Out[91]: 
    M   O   Q
B  12  14  16
D  32  34  36
F  52  54  56
# 0：10依然包含第10列
In [94]: t1.iloc[0:10]
Out[94]: 
    K   L   M   N   O   P   Q   R   S   T
A   0   1   2   3   4   5   6   7   8   9
B  10  11  12  13  14  15  16  17  18  19
C  20  21  22  23  24  25  26  27  28  29
D  30  31  32  33  34  35  36  37  38  39
E  40  41  42  43  44  45  46  47  48  49
F  50  51  52  53  54  55  56  57  58  59
G  60  61  62  63  64  65  66  67  68  69
H  70  71  72  73  74  75  76  77  78  79
I  80  81  82  83  84  85  86  87  88  89
J  90  91  92  93  94  95  96  97  98  99

```

## pandas之NaN

```python
In [128]: t2 = t1.iloc[:4,:4]

In [129]: t2
Out[129]: 
    K   L   M   N
A   0   1   2   3
B  10   11  12  13
C  20   21  22  23
D  30   31  32  33

# 我们可以发现与在numpy中不同的是，在pandas中我们要将一个整型元素变为NaN，不需要在预先对其进行类型转换，pandas会帮我们将这一列的数据自动转换为float类型
In [130]: t2.iloc[1, 1] = np.nan
In [131]: t2
Out[131]: 
    K     L   M   N
A   0   1.0   2   3
B  10   NaN  12  13
C  20  21.0  22  23
D  30  31.0  32  33
# 使用isnull可以判别该位置的值是否是NaN
In [132]: pd.isnull(t2)
Out[132]: 
       K      L      M      N
A  False  False  False  False
B  False   True  False  False
C  False  False  False  False
D  False  False  False  False
# notnull则是反效果
In [133]: pd.notnull(t2)
Out[133]: 
      K      L     M     N
A  True   True  True  True
B  True  False  True  True
C  True   True  True  True
D  True   True  True  True
# 选择L列中不为NaN的行
In [10]: t2[pd.notnull(t2["L"])]
Out[10]: 
    K     L   M   N
A   0   1.0   2   3
C  20  21.0  22  23
D  30  31.0  32  33
# 我们可以使用dropna方法来删除包含NaN的行或列
In [11]: t2.dropna(axis=0)
Out[11]: 
    K     L   M   N
A   0   1.0   2   3
C  20  21.0  22  23
D  30  31.0  32  33
# 添加参数how="all"可以指定删除全为NaN的行
In [12]: t2.dropna(axis=0, how="all")
Out[12]: 
    K     L   M   N
A   0   1.0   2   3
B  10   NaN  12  13
C  20  21.0  22  23
D  30  31.0  32  33
# 此外，我们可以使用参数inplace=True来进行原地操作，改变t2的值
In [13]: t2.dropna(axis=0, inplace=True)

In [14]: t2
Out[14]: 
    K     L   M   N
A   0   1.0   2   3
C  20  21.0  22  23
D  30  31.0  32  33
# 我们可以使用fill.na来对值为NaN的位置进行重新赋值
In [18]: t2.fillna(0)
Out[18]: 
    K     L     M   N
A   0   0.0   0.0   3
C  20  21.0  22.0  23
D  30   0.0   0.0  33
# 这里可以看出这个操作是有返回值的非原地操作，如果需要原地操作，需要加参数inplace=True
In [19]: t2
Out[19]: 
    K     L     M   N
A   0   NaN   NaN   3
C  20  21.0  22.0  23
D  30   NaN   NaN  33
# 当然，我们还可以填充每列的最大最小值、均值等等
In [20]: t2.fillna(t2.mean())
Out[20]: 
    K     L     M   N
A   0  21.0  22.0   3
C  20  21.0  22.0  23
D  30  21.0  22.0  33
In [21]: t2
Out[21]: 
    K     L     M   N
A   0   NaN   NaN   3
C  20  21.0  22.0  23
D  30   NaN   NaN  33
# 这里有个和numpy不同的地方：pandas在计算均值时遇到NaN会自动忽略，而不是将NaN作为计算结果
In [22]: t2.mean()
Out[22]: 
K    16.666667
L    21.000000
M    22.000000
N    19.666667
dtype: float64

```



## 布尔索引

```python
# 在狗的名字和使用次数统计的数组中进行布尔索引
In [95]: df = pd.read_csv("~/桌面/dogNames2.csv")
In [97]: df = df.sort_values(by="Count_AnimalName", ascending=False)
# 取使用次数大于800并且小于1000次的行
In [99]: df[(800<df["Count_AnimalName"])&(df["Count_AnimalNam
    ...: e"]<1000)]
Out[99]: 
      Row_Labels  Count_AnimalName
2660     CHARLIE               856
3251        COCO               852
12368      ROCKY               823
# 取使用次数大于800或者小于1次的行
In [100]: df[(800<df["Count_AnimalName"])|(df["Count_AnimalNa
     ...: me"]<1)]
Out[100]: 
      Row_Labels  Count_AnimalName
1156       BELLA              1195
9140         MAX              1153
2660     CHARLIE               856
3251        COCO               852
12368      ROCKY               823
```



## pandas读外部数据

### 读取CSV文件

我们可以使用`pandas.read_`后接文件类型如`pandas.read_csv`来读取csv文件。

```python
import pandas as pd
# 读取当前目录下的dogNames2文件
df = pd.read_csv("./dogNames2.csv")
print(df)
```



### 读取数据库文件

对于数据库文件，该如何读取呢？

```python
# 读关系数据库
pd.read_sql(sql_sentence.connection)
```

对于非关系数据库如mongodb：

```python
from pymongo import MongoClient
import pandas as pd
# 读取douban数据库中tv1数据
client = MongoClient()
collection = client["douban"]["tv1"]
data = list(collection.find())
# 要是想取第一行的数据，可以使用如下语句
t1 = data[0]
t1 = pd.Series(t1)
print(t1)
# 使用DataFrame读取
df = pd.DataFrame(data)
print(df)
# 结果是一个[2379 rows x 26 columns]的数组

# 如果要取特定的列
# 我们先取一行，打印一下看我们需要哪些信息
for i in data:
    print(i)
    break
# 打印结果如下：
> {'_id': ObjectId('59ba7f9b421aa91b08a43faa'), 'info': '王伟/潘粤明/王泷正/梁缘/剧情/犯罪/悬疑/2017-08-30(中国大陆)', 'original_price': None, 'release_date': '08.30', 'rating': {'count': 23043, 'max': 10, 'value': 9}, 'description': '', 'title': '白夜追凶', 'url': 'https://m.douban.com/movie/subject/26883064/', 'price': None, 'cover': {'url': 'https://qnmob2.doubanio.com/view/movie_poster_cover/lpst/public/p2483150767.jpg?imageView2/0/q/80/w/9999/h/400/format/jpg', 'width': 1200, 'shape': 'rectangle', 'height': 2134}, 'uri': 'douban://douban.com/tv/26883064', 'actions': ['可播放'], 'label': None, 'subtype': '', 'directors': ['王伟'], 'actors': ['潘粤明', '王泷正', '梁缘'], 'date': None, 'reviewer_name': '', 'forum_info': {'id': 'tv/26883064', 'participant_count': 4536, 'uri': 'douban://douban.com/tv/26883064/forum_topics'}, 'type': 'tv', 'id': '26883064', 'tv_category': 'chinese', 'tv_url_parameter': 'filter_tv_domestic_hot', 'total_num': None, 'parse_url': 'https://m.douban.com/rexxar/api/v2/subject_collection/filter_tv_domestic_hot/items?os=ios&for_mobile=1&start=0&count=50&_=0', 'total': 2123}
# 使用如下语句取特定列
for i in data:
	temp = {}
	temp["info"] = i["info"]
	temp["rating_count"] = i["rating"]["count"]
	temp["rating_value"] = i["rating"]["value"]
	temp["title"] = i["title"]
	temp["country"] = i["tv_category"]
	temp["actors"] = i["actors"]
	temp["directors"] = i["directors"]
	data_list.append(temp)
df = pd.DataFrame(data_list)
df.to_csv("./douban.csv")
# 这样就将以上的列提取了出来并储存在了当前文件夹的csv文件里
```

结果如下：

![](https://i.loli.net/2018/09/24/5ba86add18cdd.png)

此外，我们还可以借助describe()函数帮助我们统计常用的数值

```python
print(df.describe())
# 结果如下：
        rating_count  rating_value
count    2379.000000   2379.000000
mean     9079.419084      7.722783
std     16613.297194      1.068567
min       211.000000      2.800000
25%       807.000000      7.100000
50%      2573.000000      7.800000
75%      9256.000000      8.500000
max    170243.000000      9.800000
```

## 数组行排序

`sort_values()`函数可以帮我们对数组进行行排序：以上面的df数组为例，假如我们要找出评分最高的电视剧

```python
# by="rating_value"参数表示我们根据评分值进行排序，ascending=False表示逆序
df = df.sort_values(by="rating_value", ascending=False)
print(df.head(1))
# 结果是探险活宝第八季 9.8分
```

## 字符串方法

| 方法                  | 说明                                                         |
| --------------------- | ------------------------------------------------------------ |
| cat                   | 实现元素级字符串连接操作，可指定分隔符                       |
| contains              | 返回表示各字符串是否含有指定模式的布尔型数组                 |
| count                 | 模式的出现次数                                               |
| endswith、startswith  | 相当于对各个元素执行x.endswith(pattern)或x.startswith(pattern) |
| findall               | 计算各字符串的模式列表                                       |
| get                   | 获取各元素的第i个字符                                        |
| join                  | 根据指定的分隔符将Series中各元素的字符串连接起来             |
| len                   | 计算各字符串的长度                                           |
| lower、upper          | 转换大小写。相当于对各个元素执行x.lower()或x.upper()         |
| match                 | 根据指定的正则表达式对各个元素执行re.match                   |
| pad                   | 在字符串的左边、右边或左右两边添加空白符                     |
| center                | 相当于pad(side='both')                                       |
| repeat                | 重复值。例如，s.str.repeat(3)相当于对各个字符串执行x*3       |
| replace               | 用指定字符串替换找到的模式                                   |
| slice                 | 对Series中的各个字符串进行子串截取                           |
| split                 | 根据分隔符或正则表达式对字符串进行拆分                       |
| strip、rstrip、lstrip | 去除空白符，包含换行符。                                     |

### 数据合并

#### 使用join()函数进行合并（按行索引值相同进行合并）

```python
In [1]: import pandas as pd

In [2]: import numpy as np

In [7]: df1 = pd.DataFrame(np.ones((3, 4)), index=list("ABC"), columns=list("DEF
   ...: G"))

In [8]: df2 = pd.DataFrame(np.zeros((3, 2)), index=list("ABC"), columns=list("HI
   ...: "))

In [9]: df1.join(df2)
Out[9]: 
     D    E    F    G    H    I
A  1.0  1.0  1.0  1.0  0.0  0.0
B  1.0  1.0  1.0  1.0  0.0  0.0
C  1.0  1.0  1.0  1.0  0.0  0.0
# 这里我们看一下两个行数不同的数组进行合并会发生什么
In [10]: df3 = pd.DataFrame(np.zeros((4, 2)), index=list("ABCD"), columns=list("
    ...: HI"))
# 使用df1作为主数组则df3的第四行会被忽略
In [11]: df1.join(df3)
Out[11]: 
     D    E    F    G    H    I
A  1.0  1.0  1.0  1.0  0.0  0.0
B  1.0  1.0  1.0  1.0  0.0  0.0
C  1.0  1.0  1.0  1.0  0.0  0.0
# 使用df3作为主数组则df1会自动创建第四行且自动全部赋值为NaN
In [12]: df3.join(df1)
Out[12]: 
     H    I    D    E    F    G
A  0.0  0.0  1.0  1.0  1.0  1.0
B  0.0  0.0  1.0  1.0  1.0  1.0
C  0.0  0.0  1.0  1.0  1.0  1.0
D  0.0  0.0  NaN  NaN  NaN  NaN

```

#### 使用merge()函数进行合并（在指定列按元素值相同进行合并）

```python
In [3]: df1 = pd.DataFrame(np.ones((2, 3)), index=list("AB"), columns=list("CDE"
   ...: ))

In [5]: df2 = pd.DataFrame(np.zeros((2, 2)), index=list("FG"), columns=list("DI"
   ...: ))

In [6]: df1
Out[6]: 
     C    D    E
A  1.0  1.0  1.0
B  1.0  1.0  1.0

In [7]: df2
Out[7]: 
     D    I
F  0.0  0.0
G  0.0  0.0

In [8]: df1.merge(df2)
Out[8]: 
Empty DataFrame
Columns: [C, D, E, I]
Index: []

In [9]: df1.loc["A", "D"] = 0

In [10]: df1
Out[10]: 
     C    D    E
A  1.0  0.0  1.0
B  1.0  1.0  1.0

In [11]: df2
Out[11]: 
     D    I
F  0.0  0.0
G  0.0  0.0

In [12]: df1.merge(df2, on="D")
Out[12]: 
     C    D    E    I
0  1.0  0.0  1.0  0.0
1  1.0  0.0  1.0  0.0

In [13]: df2.merge(df1, on="D")
Out[13]: 
     D    I    C    E
0  0.0  0.0  1.0  1.0
1  0.0  0.0  1.0  1.0

```