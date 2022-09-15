# Numpy

## Array数组

### 数组方法

#### astype

`astype#` 方法用于转换 array 数组的数据类型

```python
np.zeros([2, 4])
-> array([[0., 0., 0., 0.],
       	  [0., 0., 0., 0.]])
       	  
np.zeros([2, 4]).astype(int)
-> array([[0, 0, 0, 0],
          [0, 0, 0, 0]])
```



#### sort

只能升序排序

```python
np.sort(arr, axis=-1, kind=None, order=None)
```

order 字段可以指定一个 field 去排序

```python
dtype = [('name', 'S10'), ('height', float), ('age', int)]
values = [('Arthur', 1.8, 41), ('Lancelot', 1.9, 38), ('Galahad', 1.7, 38)]
arr = np.array(values, dtype=dtype) 
np.sort(arr, order='height')   
```



### 属性

#### size

```python
np.array([[1, 2, 3, 4, 5], [6, 7, 8, 9, 10]]).size
-> 10
```



#### shape

```python
np.array([[1, 2, 3, 4, 5], [6, 7, 8, 9, 10]]).shape
-> (2, 5)
```



## Axis

### 二维数组分析

示例：

```
array([[ 7,  8,  5,  6,  4,  3],
       [ 1, 11,  1,  2,  6,  0],
       [ 4,  3,  5,  8,  7,  5],
       [ 5,  2, 11,  2,  4,  6]])
```



axis = 0 表示对最外层 [] 里的最大单位块做块与块之间的运算，同时移除最外层的 [] :

可见，运算是建立在 [ 7,  8,  5,  6,  4,  3] 以及  [ 1, 11,  1,  2,  6,  0] 这样的最外层 [] 里的最大单位块之间

```python
>> np.sum(arr, axis = 0)
	 array([1, 3, 1, 1, 0, 1])
```



axis= 1 表示对第二外层 `[]` 里的最大单位块做块与块之间的运算，同时移除第二外层 `[]` :

可见，运算是建立在 [ 7,  8,  5,  6,  4,  3] 的内部，即两层 [] 里的最大单位块之间，此时每个单元都是数字标量

```python
>> np.sum(arr, axis = 1)
	 array([5, 5, 1, 1])
```

<img src="http://store.secretcamp.cn/uPic/image-202104091151557862021040911515616179403160rBcWl0rBcWl.png" alt="image-20210409115155786" style="zoom: 33%;" />

### 多维数组分析

```python
> x = np.array([[[7,8,5,6],[4,3,1,11]],[[1,2,6,0],[4,3,5,8]],[[7,5,5,2],[11,2,4,6]]])
> x
array([[[ 7,  8,  5,  6],
        [ 4,  3,  1, 11]],
       [[ 1,  2,  6,  0],
        [ 4,  3,  5,  8]],
       [[ 7,  5,  5,  2],
        [11,  2,  4,  6]]])
> x.shape
 (3, 2, 4)
```

数组 x 有三个维度，分别对应 axis = 0、1、2，第一个维度是 3，第二个维度是 2，第三个维度是 4



## 索引

### 索引与切片

```python
points.shape = (100, 2)

# ':'代表第一维全要，然后取第二维的索引为0的值
points[:, 0].shape = (100, )
# ':'代表第一维全要，然后取第二维的索引为1的值
points[:, 1].shape = (100, )
```



### 布尔索引



### 神奇索引

```python
arr = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9], [10, 11, 12]])
-> array([[ 1,  2,  3],
          [ 4,  5,  6],
          [ 7,  8,  9],
          [10, 11, 12]])

arr[[2,3]]
-> array([[ 7,  8,  9],
          [10, 11, 12]])

arr[np.array([2,3])]
-> array([[ 7,  8,  9],
          [10, 11, 12]])
```



## 生成序列

### np.arange

```python
np.arange([start,] stop[, step,], dtype=None, *, like=None)

np.arange(1, 10)
-> array([1, 2, 3, 4, 5, 6, 7, 8, 9])

np.arange(-1, 1, 0.1)
-> array([-1.00000000e+00, -9.00000000e-01, -8.00000000e-01, -7.00000000e-01,
          -6.00000000e-01, -5.00000000e-01, -4.00000000e-01, -3.00000000e-01,
          -2.00000000e-01, -1.00000000e-01, -2.22044605e-16,  1.00000000e-01,
           2.00000000e-01,  3.00000000e-01,  4.00000000e-01,  5.00000000e-01,
           6.00000000e-01,  7.00000000e-01,  8.00000000e-01,  9.00000000e-01])
```



### linspace

间隔采样

```python
np.linspace(start, stop, num=50, endpoint=True, retstep=False, dtype=None, axis=0)

np.linspace(0, 10, 9)
> array([ 0.  ,  1.25,  2.5 ,  3.75,  5.  ,  6.25,  7.5 ,  8.75, 10.  ])
```



### np.zeros

```python
np.zeros((2,5))
-> array([[0., 0., 0., 0., 0.],
          [0., 0., 0., 0., 0.]])
```



### np.ones

```python
np.ones((2,5))
-> array([[1., 1., 1., 1., 1.],
          [1., 1., 1., 1., 1.]])
```



### np.empty

返回一个未初始化的数组

```python
np.empty(shape, dtype=float, order='C')

shape : int or int 元组
dtype : 数组中元素的格式
order : 'C' 或 'F', 分别代表，行优先 row-major (C-style) 和列优先 column-major (Fortran-style)
```



### np.full

返回一个给定数字的数组

```

```



### np.titl

扩展数组

```python
n1 = np.arange(0, 30, 10)
> array([ 0, 10, 20])

np.tile(n1, (3,2))
> array([[ 0, 10, 20,  0, 10, 20],
         [ 0, 10, 20,  0, 10, 20],
         [ 0, 10, 20,  0, 10, 20]])
```



## 随机数

### np.random.rand

和 np.random.random 效果相同

生成 [0, 1] 之间的随机数

```python
np.random.rand(5)
-> array([0.82426507, 0.15981161, 0.27524656, 0.46256674, 0.3980079 ])
```



### np.random.randn

标准正态分布

```python
np.random.randn(d0, d1, ..., dn)

np.random.rand(5)
-> array([ 1.50266805,  0.66866102, -0.66158713,  0.43952222,  0.08274748])
```



### np.random.normal

正态分布

```python
np.random.normal(mean,scale,size)
```



### np.random.randint

生成范围内随机数

```python
np.random.randint(1, 20, 5)
-> array([ 5,  7, 19, 11,  8])
```



### np.random.uniform

均匀分布

```
np.random.uniform(low=0.0, high=1.0, size=None)
```



### np.random.choice

Generates a random sample from a given 1-D array

```python
arr = np.random.randint(1, 10, size=5)
-> array([6, 8, 2, 6, 5])

# replace=False 表示不放回抽样，p表示每个元素被抽取的概率，如果不传则为None，表示均匀抽取
np.random.choice(arr, size=3, replace=False, p=np.array([0.1, 0.2, 0.3, 0.4, 0]))
```





### np.random.poisson





## 拆分

### np.split

Split an array into multiple sub-arrays as views into `ary`.

```python
np.split(ary, indices_or_sections, axis=0)
Parameters
----------
ary : ndarray
    Array to be divided into sub-arrays.
indices_or_sections : int or 1-D array
    If `indices_or_sections` is an integer, N, the array will be divided
    into N equal arrays along `axis`.  If such a split is not possible,
    an error is raised.
    
Returns
-------
sub-arrays : list of ndarrays
    A list of sub-arrays as views into `ary`.  
    
----------    
arr = np.arange(10)
np.split(arr, 2)
-> [array([0, 1, 2, 3, 4]), array([5, 6, 7, 8, 9])]

np.split(arr, [3, 5, 8, 9])
-> [array([0, 1, 2]), array([3, 4]), array([5, 6, 7]), array([8]), array([9])]
```



## 堆叠合并

### np.hstack

Stack arrays in sequence horizontally (column wise).

沿水平方向合并



```python
arr1 = np.array([1, 2, 3, 4, 5])
arr2 = np.array([5, 7, 10, 8, 20])
np.hstack([arr1,arr2])

-> array([ 1,  2,  3,  4,  5,  5,  7, 10,  8, 20])

	(5,) + (5,) -> (10,)
```



### np.vstack

Stack arrays in sequence vertically (row wise).

沿垂直方向合并

```python
arr1 = np.array([1, 2, 3, 4, 5])
arr2 = np.array([5, 7, 10, 8, 20])
np.hstack([arr1,arr2])

-> array([[ 1,  2,  3,  4,  5],
      	 [ 5,  7, 10,  8, 20]])

	(5,) + (5,) -> (2,5)
```





### np.dstack

Stack arrays in sequence depth wise (along third dimension).

沿深度方向合并

```python
arr1 = np.array([1, 2, 3, 4, 5])
arr2 = np.array([5, 7, 10, 8, 20])
np.dstack([arr1, arr2])

-> array([[[ 1,  5],
       	 	 [ 2,  7],
       	 	 [ 3, 10],
         	 [ 4,  8],
        	 [ 5, 20]]])

	(5,) + (5,) -> (1, 5, 2)
```



### np.concatenate

```python
concatenate((a1, a2, ...), axis=0, out=None)

Join a sequence of arrays along an existing axis.

Parameters
----------
a1, a2, ... : sequence of array_like
    The arrays must have the same shape, except in the dimension
    corresponding to `axis` (the first, by default).
axis : int, optional
    The axis along which the arrays will be joined.  If axis is None,
    arrays are flattened before use.  Default is 0.
out : ndarray, optional
    If provided, the destination to place the result. The shape must be
    correct, matching that of what concatenate would have returned if no
    out argument were specified.
    
Returns
-------
res : ndarray
    The concatenated array.
    
a = np.array([[1, 2], [3, 4]])
b = np.array([[5, 6]])

np.concatenate([a, b], axis=0)
-> array([[1, 2],
          [3, 4],
          [5, 6]])

np.concatenate((a, b.T), axis=1)
-> array([[1, 2, 5],
          [3, 4, 6]])
```



## 各类运算

### np.sum

```python
arr = np.array([[1, 2, 3, 4, 5], [6, 7, 8, 9, 10]])

np.sum(arr, 0)
-> array([ 7,  9, 11, 13, 15])

np.sum(arr, 1)
-> array([15, 40])

np.sum(arr)
-> 55
```



### np.delete

```python
np.delete(arr, obj, axis=None)

Parameters
----------
arr : array_like
    Input array.
obj : slice, int or array of ints
    Indicate indices of sub-arrays to remove along the specified axis.
    
np.delete([1,2,3,4,5], [0,4])
-> array([2, 3, 4])
```



### np.cumsum

`numpy.cumsum(a, axis=None, dtype=None, out=None)`

axis=0，按照行累加。

axis=1，按照列累加。

axis不给定具体值，就把numpy数组当成一个一维数组。

```

```



### np.diff

计算离散差值

第一行： [2-1, 3-2, 4-3, 5-4]

第二行： [7-5, 10-7, 9-10, 20-8]

```python
arr = np.array([[1, 2, 3, 4, 5], [5, 7, 10, 8, 20]])
np.diff(arr)

-> array([[ 1,  1,  1,  1],
       	  [ 2,  3, -2, 12]])
```



### np.any

```python
arr = np.random.randint(0, 5, (4, 5))
arr = np.vstack([arr, np.zeros((1, 5)), np.ones((1, 5))])

-> [[0. 0. 3. 1. 0.]
	  [2. 2. 4. 3. 4.]
	  [1. 0. 4. 3. 2.]
	  [0. 3. 0. 3. 2.]
	  [0. 0. 0. 0. 0.]
	  [1. 1. 1. 1. 1.]] 
```

对矩阵所有元素做与操作，所有为 True 则返回 True

```python
np.any(arr)
-> True

np.any(arr, axis=0)
-> [ True  True  True  True  True]

np.any(arr, axis=1)
-> [ True  True  True  True False  True]
```



### np.all

```
arr = np.random.randint(0, 5, (4, 5))
arr = np.vstack([arr, np.zeros((1, 5)), np.ones((1, 5))])

-> [[0. 0. 3. 1. 0.]
	  [2. 2. 4. 3. 4.]
	  [1. 0. 4. 3. 2.]
	  [0. 3. 0. 3. 2.]
	  [0. 0. 0. 0. 0.]
	  [1. 1. 1. 1. 1.]] 
```

对矩阵所有元素做或操作，一个为 True 则返回 True

```python
np.any(arr)
-> False

np.any(arr, axis=0)
-> [False False False False False]

np.any(arr, axis=1)
-> [False True False False False True]
```



### np.argmin/argmax

```python
np.argmin(a, axis=None, out=None)
```

计算最小值/最大值的下标

如果不传 axis 参数，相当于计算展平数组，输出一个标量，相当于一维矩阵下标

```python
arr = np.array([[[11,  9,  3,  4,  5,  6,  7,  8],
                [9, 8, 11, 12, 13, 14, 15, 16],
                [17, 8, 19, 20, 21, 22, 23, 24],
                [25, 8, 27, 28, 29, 30, 31, 32]],
               [[33, 8, 35, 36, 37, 38, 39, 40],
                [41, 8, 43, 44, 45, 46, 47, 48],
                [49, 8, 51, 52, 53, 54, 55, 56],
                [57, 8, 59, 60, 61, 62, 63, 64]]])  shape=(2,4,8)

np.argmin(arr) 
>> 2

np.argmin(arr, 0)
>> array([[0, 1, 0, 0, 0, 0, 0, 0],
         [0, 0, 0, 0, 0, 0, 0, 0],
         [0, 0, 0, 0, 0, 0, 0, 0],
         [0, 0, 0, 0, 0, 0, 0, 0]])   shape=(4,8)

np.argmin(arr, 1)
>> array([[1, 1, 0, 0, 0, 0, 0, 0],
         [0, 0, 0, 0, 0, 0, 0, 0]])   shape=(2,8)

np.argmin(arr, 2)
>> array([[2, 1, 1, 1],
         [1, 1, 1, 1]])               shape=(2, 4)
```

根据观察可得，从哪一维度计算，最终结果的那个维度会消失



### np.round

 均匀的四舍五入到给定的小数位数

```
np.round(np.random.randint(0, 10, 8) + np.random.rand(8), 2)

array([9.96, 3.27, 8.69, 5.32, 4.07, 0.11, 5.54, 0.68])
```



### np.floor

```

```



### list运算

```python
arr = np.array([[1, 2, 3, 4, 5], [5, 7, 10, 8, 20]])
list = [1, 2, 3, 4, 5]
arr - list

-> array([[ 0,  0,  0,  0,  0],
       [ 4,  5,  7,  4, 15]])
```





## 条件运算

### np.where

```
np.where(condition, [x], [y])
```



1. np.where(条件)

```python
arr = np.arange(1,10)
np.random.shuffle(arr)
for i in range(5):
    x = np.arange(1,10)
    np.random.shuffle(x)
    arr = np.vstack([arr, x])
arr1 = arr
arr1[0,0] = 1
arr2 = np.hstack([np.zeros([arr.shape[0], 1]), arr, np.zeros([arr.shape[0], 1])]).astype(int)
```



如果不传 x 和 y，则返回值是一个元组，含义是返回的索引位置

第一位是沿着 axis=0 搜索，0,0 表示有两个 1 出现在第一行，后面的 1,2,3,4,5 表示后面几行各出现一个 1

第二位是沿着 axis=1 搜索，0,5 表示第一行出现了两个 1 ，分别在 0,5 的位置，后面的 1,8,5,8,6 分别对应每一行 1 出现的索引位置。 

> 这里的返回值不会有结构化信息，会直接展平维度，例如沿axis=1时是不知道返回的索引是针对哪一行的，只知道针对所有行而言出现了几次。

```python
arr1
-> array([[1, 8, 7, 5, 3, 1, 6, 2, 4],
          [3, 1, 5, 8, 2, 4, 9, 6, 7],
          [8, 9, 4, 7, 5, 6, 2, 3, 1],
          [3, 8, 2, 7, 5, 1, 4, 9, 6],
          [7, 5, 6, 9, 2, 4, 8, 3, 1],
          [7, 3, 4, 2, 5, 6, 1, 9, 8]])

np.where(arr1 == 1)
-> (array([0, 0, 1, 2, 3, 4, 5]), array([0, 5, 1, 8, 5, 8, 6]))
```



2. np.where(条件，条件成立，条件不成立)

```python
a = np.array([[1, 2, 3, 4, 5]])
b = np.array([[6, 7, 8, 9, 10]])
cond = np.array([True, False, True, False, True])
np.where(cond, a, b)

-> array([[1, 7, 3, 9, 5]])
```



返回值是一个元组，分别表示两个索引方向 axis=0 and axis=1



```python
np.where(arr==5)

-> (array([0, 0, 1, 2, 3, 4]), array([0, 5, 3, 3, 2, 0]))
```



## 数组操作

### np.unique

```python
np.unique(np.array([[1, 2, 3, 3, 3], [1, 2, 4, 5, 6]]))

-> array([1, 2, 3, 4, 5, 6])
```



### np.squeeze

除去多维数组中，维数为1的维度

(1,3) -> (3,)

```python
np.squeeze(np.array([[1],[2],[3]]))

-> array([1, 2, 3])
```





## 维度操作

### np.ravel/np.flatten

将多维数组降到一维，但是两者的区别是返回拷贝还是返回视图，`np.flatten#` 返回一份拷贝，对拷贝所做修改不会影响原始矩阵，而 `np.ravel#` 返回的是视图，修改时会影响原始矩阵。



## 列表运算



## 格式化打印



# Pandas

## 基本操作

### df.rename

```python
df.rename(
    mapper: 'Renamer | None' = None,
    *,
    index: 'Renamer | None' = None,
    columns: 'Renamer | None' = None,
    axis: 'Axis | None' = None,
    copy: 'bool' = True,
    inplace: 'bool' = False,
    level: 'Level | None' = None,
    errors: 'str' = 'ignore',
) -> 'DataFrame | None'
```



直接修改法：

```
df.columns = ['a', 'b', 'c', 'd', 'e']
```



## 索引



## 遍历



### iterrows

`df.iterrows()` 生成一个二元组，第一位是索引，第二位是 series

```python
for i in df.iterrows():
    print(i)
```



## 集合运算

### pd.merge

```python
pd.merge(
    left: 'DataFrame | Series',
    right: 'DataFrame | Series',
    how: 'str' = 'inner',
    on: 'IndexLabel | None' = None,
    left_on: 'IndexLabel | None' = None,
    right_on: 'IndexLabel | None' = None,
    left_index: 'bool' = False,
    right_index: 'bool' = False,
    sort: 'bool' = False,
    suffixes: 'Suffixes' = ('_x', '_y'),
    copy: 'bool' = True,
    indicator: 'bool' = False,
    validate: 'str | None' = None,
) -> 'DataFrame'
```



```python
# 交集
pd.merge(df1,df2,on=['name', 'age', 'sex'])
# 并集
pd.merge(df1,df2,on=['name', 'age', 'sex'], how='outer')
```



### pd.concat

```python
pd.concat(
    objs: 'Iterable[NDFrame] | Mapping[Hashable, NDFrame]',
    axis: 'Axis' = 0,
    join: 'str' = 'outer',
    ignore_index: 'bool' = False,
    keys=None,
    levels=None,
    names=None,
    verify_integrity: 'bool' = False,
    sort: 'bool' = False,
    copy: 'bool' = True,
) -> 'DataFrame | Series'
```



## 输出

### to_csv

```python
pd.core.frame.DataFrame.to_csv(
    self,
    path_or_buf: 'FilePath | WriteBuffer[bytes] | WriteBuffer[str] | None' = None,
    sep: 'str' = ',',
    na_rep: 'str' = '',
    float_format: 'str | None' = None,
    columns: 'Sequence[Hashable] | None' = None,
    header: 'bool_t | list[str]' = True,
    index: 'bool_t' = True,
    index_label: 'IndexLabel | None' = None,
    mode: 'str' = 'w',
    encoding: 'str | None' = None,
    compression: 'CompressionOptions' = 'infer',
    quoting: 'int | None' = None,
    quotechar: 'str' = '"',
    line_terminator: 'str | None' = None,
    chunksize: 'int | None' = None,
    date_format: 'str | None' = None,
    doublequote: 'bool_t' = True,
    escapechar: 'str | None' = None,
    decimal: 'str' = '.',
    errors: 'str' = 'strict',
    storage_options: 'StorageOptions' = None,
) -> 'str | None'
```



### to_excel

```python
writer = pd.ExcelWriter(r'./name.xlsx')
df.to_excel(writer, sheet_name='sheet1', index=True,header=True)
writer.save()
```



## excel处理

必须安装先 openpyxl 依赖按照，否则报错

```sh
pip install openpyxl
```





```python
pd.read_excel(
    io,
    sheet_name=0,
    header=0,  # 指定作为列名的行，默认0，即取第一行
    names=None,
    index_col=None,  # 指定某列为索引列
    usecols=None,
    squeeze=False,
    dtype: 'DtypeArg | None' = None,
    engine=None,
    converters=None,
    true_values=None,
    false_values=None,
    skiprows=None,
    nrows=None,
    na_values=None,
    keep_default_na=True,
    na_filter=True,
    verbose=False,
    parse_dates=False,
    date_parser=None,
    thousands=None,
    comment=None,
    skipfooter=0,
    convert_float=None,
    mangle_dupe_cols=True,
    storage_options: 'StorageOptions' = None,
)
```



# Matplotlib

## outdated pylab

不建议使用 `matplotlib.pylab`

导入 `matplotlib.pylab` 相当于导入了`matplotlib.pyplot`、`numpy`、 `numpy.fft`、
`numpy.linalg`、 `numpy.random` ，这是为了模拟 MATLAB 风格，但这种风格是不好的。

```python
"""
warning:
   Since heavily importing into the global namespace may result in unexpected
   behavior, the use of pylab is strongly discouraged. Use `matplotlib.pyplot`
   instead.

`pylab` is a module that includes `matplotlib.pyplot`, `numpy`, `numpy.fft`,
`numpy.linalg`, `numpy.random`, and some additional functions, all within
a single namespace. Its original purpose was to mimic a MATLAB-like way
of working by importing all functions into the global namespace. This is
considered bad style nowadays.
"""
```



## 绘画相关



### cm 颜色图谱 

#### 所有颜色图谱

```python
cmaps = [('Perceptually Uniform Sequential',
                            ['viridis', 'inferno', 'plasma', 'magma']),
         ('Sequential',     ['Blues', 'BuGn', 'BuPu',
                             'GnBu', 'Greens', 'Greys', 'Oranges', 'OrRd',
                             'PuBu', 'PuBuGn', 'PuRd', 'Purples', 'RdPu',
                             'Reds', 'YlGn', 'YlGnBu', 'YlOrBr', 'YlOrRd']),
         ('Sequential (2)', ['afmhot', 'autumn', 'bone', 'cool',
                             'copper', 'gist_heat', 'gray', 'hot',
                             'pink', 'spring', 'summer', 'winter']),
         ('Diverging',      ['BrBG', 'bwr', 'coolwarm', 'PiYG', 'PRGn', 'PuOr',
                             'RdBu', 'RdGy', 'RdYlBu', 'RdYlGn', 'Spectral',
                             'seismic']),
         ('Qualitative',    ['Accent', 'Dark2', 'Paired', 'Pastel1',
                             'Pastel2', 'Set1', 'Set2', 'Set3']),
         ('Miscellaneous',  ['gist_earth', 'terrain', 'ocean', 'gist_stern',
                             'brg', 'CMRmap', 'cubehelix',
                             'gnuplot', 'gnuplot2', 'gist_ncar',
                             'nipy_spectral', 'jet', 'rainbow',
                             'gist_rainbow', 'hsv', 'flag', 'prism'])]
```



#### 使用颜色图谱

传递参数时，指定颜色图谱 cmap=plt.cm.Blues，然后指定c=序列

```python
plot(...c=[i for i in range(points.shape[0])], cmap=plt.cm.Blues)
```





#### 等距选择颜色图谱

在画图时设置 c 和 cmap 参数后，每次画图颜色都是从 cmap 中随机选取的，可以固定这些颜色

```sh
# 选取4种颜色 
cmap = plt.cm.get_cmap('viridis', 4)


cmap = plt.cm.get_cmap('Dark2')
```



### rcParams 配置文件

运行配置（run configuration，rc）

`matplotlib.rc` 和 `matplotlib.pyplot.rc` 是同一个对象

```python
# 查看所有默认属性，修改属性只需要修改字典对应的值即可
matplotlib.rcParams

# 恢复默认属性
matplotlib.rcdefaults()

# 指定默认字体
matplotlib.rcParams['font.sans-serif'] = ['FangSong'] 

# 解决保存图像是负号'-'显示为方块的问题
matplotlib.rcParams['axes.unicode_minus'] = False 
```





### sytle 整体绘图风格 

#### 所有风格

```python
# 查看所有风格
plt.style.available

['Solarize_Light2',
 '_classic_test_patch',
 'bmh',
 'classic',
 'dark_background',
 'fast',
 'fivethirtyeight',
 'ggplot',
 'grayscale',
 'seaborn',
 'seaborn-bright',
 'seaborn-colorblind',
 'seaborn-dark',
 'seaborn-dark-palette',
 'seaborn-darkgrid',
 'seaborn-deep',
 'seaborn-muted',
 'seaborn-notebook',
 'seaborn-paper',
 'seaborn-pastel',
 'seaborn-poster',
 'seaborn-talk',
 'seaborn-ticks',
 'seaborn-white',
 'seaborn-whitegrid',
 'tableau-colorblind10']
```



#### 使用风格

```python
plt.style.use('ggplot')
```



### marker 点样式

#### 基本样式

```
============================== ====== =========================================
marker                         symbol description
============================== ====== =========================================
``"."``                        |m00|  point
``","``                        |m01|  pixel
``"o"``                        |m02|  circle
``"v"``                        |m03|  triangle_down
``"^"``                        |m04|  triangle_up
``"<"``                        |m05|  triangle_left
``">"``                        |m06|  triangle_right
``"1"``                        |m07|  tri_down
``"2"``                        |m08|  tri_up
``"3"``                        |m09|  tri_left
``"4"``                        |m10|  tri_right
``"8"``                        |m11|  octagon
``"s"``                        |m12|  square
``"p"``                        |m13|  pentagon
``"P"``                        |m23|  plus (filled)
``"*"``                        |m14|  star
``"h"``                        |m15|  hexagon1
``"H"``                        |m16|  hexagon2
``"+"``                        |m17|  plus
``"x"``                        |m18|  x
``"X"``                        |m24|  x (filled)
``"D"``                        |m19|  diamond
``"d"``                        |m20|  thin_diamond
``"|"``                        |m21|  vline
``"_"``                        |m22|  hline
``0`` (``TICKLEFT``)           |m25|  tickleft
``1`` (``TICKRIGHT``)          |m26|  tickright
``2`` (``TICKUP``)             |m27|  tickup
``3`` (``TICKDOWN``)           |m28|  tickdown
``4`` (``CARETLEFT``)          |m29|  caretleft
``5`` (``CARETRIGHT``)         |m30|  caretright
``6`` (``CARETUP``)            |m31|  caretup
``7`` (``CARETDOWN``)          |m32|  caretdown
``8`` (``CARETLEFTBASE``)      |m33|  caretleft (centered at base)
``9`` (``CARETRIGHTBASE``)     |m34|  caretright (centered at base)
``10`` (``CARETUPBASE``)       |m35|  caretup (centered at base)
``11`` (``CARETDOWNBASE``)     |m36|  caretdown (centered at base)
``"None"``, ``" "`` or  ``""``        nothing
``'$...$'``                    |m37|  Render the string using mathtext.
                                      E.g ``"$f$"`` for marker showing the
                                      letter ``f``.
``verts``                             A list of (x, y) pairs used for Path
                                      vertices. The center of the marker is
                                      located at (0, 0) and the size is
                                      normalized, such that the created path
                                      is encapsulated inside the unit cell.
path                                  A `~matplotlib.path.Path` instance.
``(numsides, 0, angle)``              A regular polygon with ``numsides``
                                      sides, rotated by ``angle``.
``(numsides, 1, angle)``              A star-like symbol with ``numsides``
                                      sides, rotated by ``angle``.
``(numsides, 2, angle)``              An asterisk with ``numsides`` sides,
                                      rotated by ``angle``.
```



## 创建画图

```python
# 创建画布
plt.figure(num=None, 
           figsize=None, 
           dpi=None, 
           facecolor=None,  # 背景颜色
           edgecolor=None,  # 边界颜色
           frameon=True)



```



## 绘图



### pyplot 基本绘图

👉 基本API

```python
# 绘图
plt.plot(*args,
         scalex=True,
         scaley=True,
         data=None,
         **kwargs)
```





```python
# 利用API绘图
fig = plt.figure(figsize=(6,3))
plt.plot(x,y1)
plt.grid(linestyle='--',linewidth=1,alpha=0.3)
plt.title('abc',fontsize=25)
plt.show()
```



👉 面向对象绘图（推荐~）

主要是调用 `AxesSubplot` 对象的各种方法，在 API 调用绘图中，一些函数形式为 `plt.title#` ，对应的 `AxesSubplot` 对象的方法就为 `ax.set_title#`  ，也有一些调用形式为 `plt.text#` ，对应的方法为 `ax.text#`

```python
# 创建figure对象
fig = plt.figure(figsize=(6,3))
# 在figure对象上添加子图
ax1 = fig.add_subplot(1,1,1)
# 在子图上进行绘制
ax1.plot(x,y1)
ax1.grid(color='r',linestyle='--',linewidth=1,alpha=0.3)
plt.show()
```



```python
# 显示绘图结果
plt.show(close=None,
         block=None)
```





### scatter 散点图

```python
plt.scatter(x,
					  y,  
            s=None,          # marker size 散点大小
            c=None,          # color  可以传入列表，建立颜色映射
            marker=None,     # The marker style
            cmap=None,       # colormap 
            norm=None,       # 数据标准化
            vmin=None,     	 # 结合norm使用
            vmax=None,       # 结合norm使用
            alpha=None,     
            linewidths=None, # 轮廓线尺寸
            edgecolors=None, # 数据点轮廓的颜色
            data=None,
            **kwargs         # matplotlib.collections.Collection属性
            )


```



```python
points = np.random.randn(100,2)
plt.scatter(points[:,0],points[:,1],s=100,c=order,cmap = plt.cm.Blues)
order = [i for i in range(points.shape[0])]
plt.show()
```



### imshow 热图绘制

```

```





## Line2D参数

```python
class matplotlib.lines.Line2D(xdata, ydata, linewidth=None, linestyle=None, color=None, marker=None, markersize=None, markeredgewidth=None, markeredgecolor=None, markerfacecolor=None, markerfacecoloralt='none', fillstyle=None, antialiased=None, dash_capstyle=None, solid_capstyle=None, dash_joinstyle=None, solid_joinstyle=None, pickradius=5, drawstyle=None, markevery=None, **kwargs)
```

使用的最频繁的 `plt.plot()` 绘图方法的返回值就是一个 Line2D 的一个实例化对象，`plt.plot()` 中有一个关键字参数列表 `**kwargs ` ，这里面有很多关于线 line 的定制化设计参数，通过修改对应的关键字就可以创建出不同类型的线。

```python
line = plt.plot([1,4,2,3], linewidth=5)
line

> [<matplotlib.lines.Line2D at 0x7fc47c952160>]
```



| 全名             | 参数                          | 说明                                                       |
| ---------------- | ----------------------------- | ---------------------------------------------------------- |
| fmt              | 'bo-' -> bule + 'o' + '-'     | 快速定义color、marker、linestyle                           |
| color            | 'b'、'g'、'r' 等              | 颜色                                                       |
| c (color)        |                               | 颜色序列，必须和cmap一起使用，此时不能用color表示，只能用c |
| size             |                               |                                                            |
| fontsize         |                               |                                                            |
| linewidth \| lw  | float or None                 | 线宽                                                       |
| alpha            | float or None                 | 透明度                                                     |
| edgecolor \| ec  | color or None or 'auto'       | 数据点轮廓                                                 |
| facecolor \| fc  | color or None                 |                                                            |
| linestyle \| ls  | '-'、'--'、'-.'、':' 等       | 线段风格                                                   |
| marker           | '.'、','、'o'、'+' 等         | 数据点                                                     |
| markersize \| ms | float                         | 数据点大小                                                 |
| data             | (2, N) array or two 1D arrays | 数据                                                       |
|                  |                               |                                                            |







## 绘制子图

绘制方法一：

```python
# 在每个子图对象上分别绘制
fig = plt.figure(figsize=(12,6))
ax1 = fig.add_subplot(2,2,1)
ax2 = fig.add_subplot(2,2,2)
ax3 = fig.add_subplot(2,2,3)
ax4 = fig.add_subplot(2,2,4)

ax1.plot(x,y1)
ax2.plot(x,y2,c='b')
ax3.plot(x,y1,'r--')
ax4.scatter(x,y2,s=5)

plt.show()
```



绘制方法二：

```python
# 调用 plt.subplots #返回figure和axes对象，axes对象拥有所有子图对象的信息
fig, axes = plt.subplots(2,2,sharey=True,figsize=(12,3))
axes = np.ravel(axes)  #  (2,2)---〉(4,)

axes[0].plot(x,y1,'b-',label='cos x',linewidth=1)
axes[1].plot(x,y2,'k-',label='sin x',linewidth=1)

axes[0].set_xlabel('x',size=15)
axes[0].set_ylabel('Sin',size=15)
axes[0].legend(loc='best',fontsize='x-large')

axes[1].set_xlabel('x',size=15)
axes[1].set_ylabel('Cos',size=15)
axes[1].legend(loc='upper left',fontsize='x-large')


fig.subplots_adjust(wspace=0.1,hspace=0.1) 

plt.show()
```



## 设置标题

```python
# 设置标题
plt.title(label,
          fontdict=None,
          loc=None,
          pad=None,
          *,
          y=None,
          **kwargs)
```



## 设置网格线

```python
# 背景网格线
plt.grid(visible=None,
         which='major',
         axis='both',
         **kwargs)

visible : bool or None, optional

which : {'major', 'minor', 'both'}, optional
    The grid lines to apply the changes on.

axis : {'both', 'x', 'y'}, optional
    The axis to apply the changes on.

**kwargs : `.Line2D` properties
    Define the line properties of the grid, e.g.::

        grid(color='r', linestyle='-', linewidth=2)
```



## 设置刻度

相关API：

```python
# type(ax) = matplotlib.axes._subplots.AxesSubplot
# type(ax.xaxis) = matplotlib.axis.XAxis
ax.set_xticks(ticks, labels=None, *, minor=False, **kwargs) 
ax.xaxis.set_ticks(ticks, labels=None, *, minor=False, **kwargs)
```



案例：

```python
fig = plt.figure(figsize=(6,3))
ax1 = plt.subplot(1,1,1)
ax1.plot(x,y1)
ax1.grid(color='r',linestyle='--',linewidth=1, alpha=0.3)

# 方法一：ax是AxesSubplot对象，调用AxesSubplot对象的set_xticks#方法
ax1.set_xticks(np.arange(-5,5),minor=True)  # 如果minor=False,则只显示主刻度

# 方法二：xaxis是XAxis对象，调用XAxis对象的set_ticks#方法
ax1.xaxis.set_ticks(np.arange(-5,5),minor=True) 与上式效果等价

ax1.set_yticks([-1,0,1])  
ax1.yaxis.tick_right() # 刻度显示在右侧

plt.show()
```



## 设置标签

```python
plt.legend
```



## 设置字体

想要使用指定字体，有两种方式：

```python
import matplotlib.font_manager as fm
# 方法二  字典中需要传入字体的英文名称，必须是matplotlib的内置字体
my_font1 = {'family': 'FangSong', 'weight': 'bold',
            'size': 12, 'style': 'italic'}

# 查看matplotlib所有的内置字体
[f.name for f in fm.FontManager().ttflist]

# 方法二  传入字体的路径，导入font_manager
my_font2 = fm.FontProperties(fname=r'C:\Windows\Fonts\方正清刻本悦宋简.TTF')
```



```python
fig = plt.figure(figsize=(12, 3))
ax1 = plt.subplot(1, 2, 1)
ax2 = plt.subplot(1, 2, 2)

ax1.plot(x, y1)
ax2.plot(x, y2)

ax1.set_title('这里是标题', fontdict={'fontproperties': my_font1}, size=20)
ax2.set_title('这里是标题', fontdict=my_font2, size=20)

plt.show()
```



## 设置图例

```python
plt.legend(*args, **kwargs)


loc : 位置
prop :the font property字体参数
fontsize:字体大小
```



## 调整图边距

bottom、 top、 left、right 的范围是 0-1

bottom=0，top=1，left=0，right=1 表示完全去白边，保留白边需要增加 bottom、left 的值，减少 top、right 的值

```python
fig.subplots_adjust(bottom=0.05, top=0.95, left=0.05, right=0.95, hspace=0, wspace=0)
```





## 绘制箭头

```python
# 绘制一条路线，(x,y) -> (x + dx, y + dy)
plt.arrow(x,   		 # 起始x坐标
          y,				# 起始y坐标
          dx,			  # x偏移量
          dy, 		  # y偏移量
          **kwargs)

----------
x, y : float
    The x and y coordinates of the arrow base.

dx, dy : float
    The length of the arrow along x and y direction.

width : float, default: 0.001
    Width of full arrow tail.

length_includes_head : bool, default: False
    True if head is to be counted in calculating the length.

head_width : float or None, default: 3*width
    Total width of the full arrow head.

head_length : float or None, default: 1.5*head_width
    Length of arrow head.

shape : {'full', 'left', 'right'}, default: 'full'
    Draw the left-half, right-half, or full arrow.

overhang : float, default: 0
    Fraction that the arrow is swept back (0 overhang means
    triangular shape). Can be negative or greater than one.

head_starts_at_zero : bool, default: False
    If True, the head starts being drawn at coordinate 0
    instead of ending at coordinate 0
```





## 绘制路线

```python
_locations = [(4, 4), (4, 4), (4, 4), (4, 4), (4, 4),
              (4, 4), (2, 0),  (8, 0), (0, 1), (1, 1), (5, 2),
              (7, 2), (3, 3), (6, 3), (5, 5), (8, 5), (1, 6),
              (2, 6), (3, 7), (6, 7), (0, 8), (7, 8)]

plt.figure(figsize=(10, 10))
x = [l[0] for l in _locations]
y = [l[1] for l in _locations]
plt.plot(x[:6], y[:6], 'g*', ms=30, label='depot')
plt.plot(x[6:], y[6:], 'ro', ms=15, label='customer')
plt.grid(True)
plt.legend(loc='lower left')

way = [[0, 12, 18, 17, 16, 4, 14, 10, 11, 13, 5],
       [0, 6, 9, 8, 20, 3], [0, 19, 21, 15, 7, 2]]

# 设置色彩图谱
cmap = plt.cm.rainbow
# 设置色彩范围
cNorm = colors.Normalize(vmin=0, vmax=len(way))
# 建立映射
scalarMap = cmx.ScalarMappable(norm=cNorm, cmap=cmap)

for k in range(0, len(way)):
    # 选择一条路线
    way0 = way[k]
    colorVal = scalarMap.to_rgba(k)
    for i in range(0, len(way0) - 1):
        start = _locations[way0[i]]
        end = _locations[way0[i+1]]
        plt.arrow(start[0], start[1], end[0]-start[0], end[1]-start[1],
                  length_includes_head=True, head_width=0.2, lw=1,
                  color=colorVal)
plt.show()
```





## 日期处理

```python
fig.autofmt_xdate()    # 自动旋转日期格式
```



## 保存绘图

```python
plt.savefig(*args, **kwargs)

Parameters
----------
fname :

dpi : float or 'figure', default: :rc:`savefig.dpi`
    The resolution in dots per inch.  If 'figure', use the figure's
    dpi value.

format : str  # 文件类型，例如'png', 'pdf', 'svg'，也可以在fname中指定
    
bbox_inches : # 如果设置为'tight', 会去掉白边

pad_inches :  # 配合bbox_inches='tight'使用，向外部扩展白边
 
```





# Sklearn

## cdist

结果表示 x1 数组中各个元素点与 x2 数组中各个元素点的距离，例如第一行表示x1 数组中第一个元素点和 x2 数组中各个元素点的距离。

```python
from scipy.spatial.distance import cdist
import numpy as np
x1 = np.array([(1,3),(2,4),(5,6)])
x2 = [(3,7),(4,8),(6,9)]
cdist(x1,x2,metric='euclidean')

>>  array([[ 4.47213595,  5.83095189,  7.81024968],
           [ 3.16227766,  4.47213595,  6.40312424],
           [ 2.23606798,  2.23606798,  3.16227766]])
```





## kmeans





