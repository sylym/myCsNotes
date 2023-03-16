## Numpy

- `import numpy as np`

### 基本用法

- 它提供了 `ndarray`，一个同构（数组内数据类型相同）的 n 维数组对象，以及对其进行有效操作的方法。
- 创建`tmp = np.array(l, dtype='str')`
  - dtype为可选择参数用于指代数组的类型

#### 方法

- `.dtype`显示数组内的数据类型
- `.astype('str')`指定数据类型（以str为例），不会修改原数组，需要用赋值的方式改变
- `.zeros((x,y))`x,y二维向量且全是0
- `numpy.arrange(min,max,step)`生成等差数列
- `.reshape(x,y)`一维数组转化为xy矩阵，y=-1时表示自动计算匹配的数目
- `.sum()`求和
- `.T`转置矩阵
- `.sort()`原地排序，矩阵也可以
- `numpy.ext(a)`生成一个新数组，每个元素是e^ai^

## Pandas

- Pandas 是基于 NumPy 的一种工具，该工具是为解决数据分析任务而创建的。Pandas 纳入了大量库和一些标准的数据模型，提供了高效地操作大型数据集所需的工具。
- pandas 有两个主要的数据结构，`Series` 和 `DataFrame`

- `import pandas as pd`

### Series

- 是一种一维数据结构

- 创建`s = pd.Series([1,2,3])`
  - 也可以直接传入一个字典，直接绑定索引
- `s = pd.Series([1,2,3], index=['a', 'b', 'c'])`绑定索引

### DataFrame

- 是一种表格数据结构，我们可以把 DataFrame 看作 Series 组成（series作为列来存在）的字典

- ```python
  dic = {
      'name': ['张三', '李四', '王五'],
      'age': [18, 19, 20],
      'gender': ['male', 'female', 'female']
  }
  df = pd.DataFrame(dic, index = list('abc'))
  ```

- <img src="../../../../../../iCloudDrive/iCloud~com~coderforart~iOS~MWeb/media/image-20230309110743191.png" alt="image-20230309110743191" style="zoom: 67%;" />

#### 成员

- `.info`查看数据类型

- `df.iloc[i]`按行访问（不包含表头name age gender）

  - `df[i]`表示按照列进行访问

- `df.loc[['a', 'b'], ['name', 'gender']]`通过 ***索引\*** 对数据进行索引

  - 提取指定的行列组成的子矩阵
  - <img src="../../../../../../iCloudDrive/iCloud~com~coderforart~iOS~MWeb/media/image-20230309111540517.png" alt="image-20230309111540517" style="zoom:67%;" />
  - 对数据筛选`df.loc[(df.age < 20) & (df.gender == 'male')]`条件之间使用&进行连接
  - 可以实现一些复杂的搜索，如搜索最大值`df_cleansed.loc[df.price==df['price'].max()]`

- 导入表格`read_csv` 函数，它接受一个文件名或一个 URL 作为参数，并返回一个 DataFrame 对象

  - ```python
    import pandas as pd
    df = pd.read_csv("./data.csv") # 读取本地文件
    df = pd.read_csv("https://example.com/nba.csv") # 读取网络文件
    print(df)
    ```

- `.head(n)` 提取前n行`.tail(n)`提取后n行

- `groupby('')`依据某一项对数据进行分组

  - ```python
    car_Manufacturers = df_cleansed.groupby('company')
    # 依据company对数据进行分组
    toyotaDf = car_Manufacturers.get_group('toyota')
    # 从分组后的数据得到新的DataFrame
    ```

  - 对类别进行技术`df_cleansed['company'].value_counts()`

- 排序`carsDf = df_cleansed.sort_values(by=['price', 'horsepower'], ascending=False)`

  - ascending设定为False表示颠倒排序
