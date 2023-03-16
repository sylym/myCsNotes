# 基本语法

## 基本操作

#### 补充

- pass写在语句块中，告诉程序该语句块中什么都不需要做（可以用于抽象类的函数中）

#### 注释

- 单行：用#开头表示
- 多行"""..."""

### 输入输出

- 输出：print()
- 输入：`text=input('提示文本')`
  - 参数用于显示，对用户提示，返回用户的输入内容（以字符串的格式返回）

### 编译执行

-  python是一种解释型脚本语言，和C/C++语言不同，C/C++程序从main函数开始执行，python程序从开始到结尾顺序执行。
- 可以用`if __name__ == '__main__':`表示main函数，即程序的入口

### 分文件编程

- 导入模块
  - `import name`导入name.py
  - 使用函数`name.f()`需要添加模块名称许作为前缀
- 导入函数
  - `from 文件名 import 函数名,...`可以一次导入多个函数
    - 用*替代函数名可以导入全部函数
  - 使用函数无需添加限定，可以直接使用

- 导入类：语法与函数类似

- as指定别名
  - import引入的函数或模块名称后面添加`as name`

### 文件读写

#### 读取

- 读取整个文件

  - ```python
    with open('path') as name:
        contents=name.read()
    ```

  - with关键字可以实现文件访问完成后自动关闭

    - 也可以用close()手动关闭
    - open得到的文件对象只可以在with内部使用

  - 这样读取的文件末尾会有一个空行，可以用rstrip去除

  - open模式：`open('path','模式')`

    - w写入
    - r读取（默认）
    - a附加
    - r+读写

  - 如果要写入的文件不存在，则会自动创建文件

- 逐行读取

  - ```python
    with open('path') as name:
        for line in name:
            ...
    ```

  - 提取出每一行，分别进行操作

    - 由于每行末尾有换行符，文件末尾会有一个空行，可以用rstrip去除	

  - 读取到列表`arr=name.readlines()`

#### 写入

- 全部写入`name.write(...)`
  - 不会自动在每次写入的字符串后添加换行符，可以手动添加/n
  - 只能写入字符串；类型

#### 使用json存储数据

- 先引入`import json`

- 写入

  - ```python
    with open('path.json','w') as name:
    	json.dump(写入内容, name)
    ```

  - 可以写入非字符串数据类型

- 读取

  - `    numbers=json.load(name)`

### 异常处理

- try-except-else系统

  - 程序发生异常时会抛出一个异常对象

  - 可以进行捕获

  - ```python
    try:
        print(5/0)#可能出现异常的部分
    except ZeroDivisionError:#捕获异常对象
        ...#发生异常进行的操作
    else:
    	....#不发生异常进行的操作
    ```

- 异常

  - ZeroDivisionError除数为零
  - FileNotFoundError找不到文件（open触发）

- 断言

  - ```python
    import unittest
    class test(unittest.TestCase):#继承
    	def name(self):#通过调用测试类内的测试函数来使用断言
            self.#调用继承的断言函数
    ```

  - ![IMG_20230107_214702](../media/IMG_20230107_214702.jpg)

## 数据类型

### 变量

- 给多个变量同时赋值
  - `a,b,c=0,0,0`
- python没有常量，通常可以用全部大写的名称来表示
- None表示变量没有值

### 字符串str

- 可以使用单引号或双引号括起来
- 不可修改：
  - 可以转化为列表再修改
  - 可以通过切片实现


#### 操作

- 内容修改（只是临时，不会对原字符串产生影响）
  - `title()`：转化为首字母大写
  - `upper()` `lower()`
  - `rstrip()`：去掉尾部的空格
    - `lstrip()`：去掉头部的空格
    - `strip()`：去掉头尾部的空格
  - `split()`以空格切割字符串返回列表
  - `join()`拼接字符串
    - ` 'sep'.join(seq)`
      - sep：分隔符。可以为空
      - seq：要连接的元素序列、字符串、元组、字典
  - `ord()`返回ascll码
  - `chr()`将数字转化为ascll或Unicode码
  - `reversed()`反转
  
- 在字符串中使用变量的值：f字符串
  - `f"Hello,{first_name}"`
  - `f""`包住，会将或括号内的变量转化为字符串来显示
- 查找：`.find("要查找的内容"[，开始位置，结束位置])`
- ```python
  字符串.isalnum() 所有字符都是数字或者字母，为真返回 Ture，否则返回 False。
  
  字符串.isalpha() 所有字符都是字母，为真返回 Ture，否则返回 False。
  
  字符串.isdigit() 所有字符都是数字，为真返回 Ture，否则返回 False。
  
  字符串.islower() 所有字符都是小写，为真返回 Ture，否则返回 False。
  
  字符串.isupper() 所有字符都是大写，为真返回 Ture，否则返回 False。
  
  字符串.istitle() 所有单词都是首字母大写，为真返回 Ture，否则返回 False。
  
  字符串.isspace() 所有字符都是空白字符，为真返回 Ture，否则返回 False。
  
  ```
- 

### 数字

- 两个数相除时结果总会是**浮点数**，即使结果是一个整数
- 数字很大时可以使用下划线分隔来便于阅读，系统在存储数据时会自动去除
  - `age=14_000_000`
- int边界值
  - `sys.maxsize`最大值（inf）
  - `-sys.maxsize-1`最小值


### 布尔

- 首字母大写：True False

### 列表list

- 用`[,...,]`表示
- 索引值-1指向最后一个元素，-2表示倒数第二个元素...
- 末尾添加元素`append(val)`
  - 与del不同的的是pop会返回删除的内容
  - `extend()`方法向一个列表的尾部追加另一个列表的所有元素。(等同于直接用+相加)
- 插入元素`insert(p,val)`
- 删除元素
  - `pop(i)`不填写参数时默认删除最后一个元素
  - 删除元素`del arr[i]`删除第i个元素
  - `remove(val)`根据值删除元素，只删除符合的第一个值，并且也有返回值
- 列表解析：用for循环创建新列表
  - `arr=[value**2 for value in range(1,11)]`依次执行表达式创建元素
    - 可以用于创建二维列表
      - `arr = [[0] * (m) for _ in range(n)]`
  - 创建相同元素：[None]*26
- 截取片段：`arr[begin:end[:step]]`与range类似
  - begin默认为0，end默认为末尾可以省略
  - [-3:0]或[-3:]表示遍历最后三个元素
  - [::-1]实现颠倒
- 复制
  - 直接`arr2=arr`是浅拷贝
  - `arr2=arr[:]`可以实现深拷贝
- 元组
  - 不可变的列表称为元组
  - 使用()取代[]来定义
- 获取最大最小的元素列表
  - `nlargest(n , iterbale, key=None)`获取序列中最大的n个元素
  - `nsmallest(n , iterbale, key=None)`


#### 操作

- 排序
  - `sort()`：对列表进行修改（只能用于列表，是列表的成员函数）
    - 使用参数key实现自定义比较
      - `functools.cmp_to_key(my_compare)`也可以使用函数把比较函数转化为key
      - key表示以什么为基准来进行比较
      - `key = lambda x:(-x[0],x[1])`表示优先按照x[0]逆序比较，x[0]相同时按x[1]比较
      - lambda表达式：`add = lambda x,y:x+y`声明变量和和操作（返回值）
    - 传入参数`reverse=True`降序排列 
  - `sorted()`：不修改，以返回值的形式返回
- `reverse()`：颠倒数组
- `len()`：获取数组长度
- `min(arr)` `max(arr)` `sum(arr)`
- `set(arr)`：返回一个不包含重复元素的列表（集合）
- 查找元素
  - `listname.count(obj, start, end)`常用于查找元素是否存在
  - `listname.index(obj, start, end)`返回元素所在的下标，如果不存在则会报错


### 字典dict

- 初始化`map={键:值,...}`，赋值新增操作与c++类似
- 记录顺序与元素插入的顺序相同
- 创建空字典：使用{}`map={}`
- 删除元素：使用del关键字
- 使用下标访问不存在的元素时会报错
  - 可以使用`get()`不存在时返回None
- 排序：
  - 


#### 操作

- `.items()`：返回键值对列表
  - 可以用于实现按照下标编号访问`self.booked.items()[i - 1][1]`

- `.keys()`：返回键的列表（用for遍历字典时的默认行为）
- `.values()`：遍历所有值
- `clear()`：请空元素

### 集合set

- 可以用`set={键,...}`直接创建集合,或`setname = set(iteration)`

#### 操作

- 删除`remove(i)`元素不存在时会报错，`discard(i)`元素不存在时不会报错
- 使用 add() 方法添加的元素，只能是数字、字符串、元组或者布尔类型（True 和 False）值，不能添加列表、字典、集合这类可变的数据
- 不同set间可以使用&|-^运算

### 类型转化

- `int() ` `float()`

## 运算

- **表乘方（用10\*\*n代替1e9，1e9是分数）
- /会返回浮点数
  - //取整除法

## 作用域

- 没有块级作用域，局部变量以整个函数为作用域
- 局部变量：在函数内部定义的变量，它的作用域也仅限于函数内部，出了函数就不能使用了
  - nonlocal在封装函数中使用，使得变量可以在外部函数中使用

- 全局变量
  - 在函数外定义或者使用`global`修饰局部变量
  - 在函数内只能获取全局变量，对全局变量赋值会默认产生一个同名的局部变量
    - 可以使用global声明要求使用全局变量


## 操作流

- for
  - 遍历数组：`for x in arr:`
    - 生成的x是副本，不会对原列表修改
  - 计数`for i in range(1,5) `i取值1234
    - range(x,y)可以用于创建数组（不包含y）
    - `range(start, stop, [step])`倒序如`range(5,-1,-1)`
      - 注意不包括stop
  - 遍历字典：`for key, value in map.items)(): `
    - 按照插入顺序返回元素
    - 不加item时只取出键
  - enumerate(words)遍历列表、字典（与item类似，但可以用于列表）
    - 返回（下标，数值）
    - `for i, word in enumerate(words):`
- if
  - `if a==num:`
    - and表示&& or表示|| not表示！
  - 判断元素是否在列表中使用in关键字
    - 不在使用not in
  - elif表示else if
- 三元运算符`maxNum = a if a > b else b`
- while
  - `while a==num:`
  - `break` `continue`
- any
  - any() 函数用于判断给定的可迭代参数 iterable 是否全部为 False，则返回 False，如果有一个为 True，则返回 True。
  - `if any(l < end and start < r for l, r in self.booked):`


## 函数

- 定义：`def f():`
- 列表默认传参是浅拷贝
  - 可以传递副本`arr[:]`实现深拷贝
- 传递任意数目的实参
  - `def f(*arr)`会创建一个空元组并将所有实参存入
    - 常用名称*args
  - `def f(**arr)`创建一个字典用于存储数据，允许修改
- 可以用逗号分隔返回多个值
  - 如`a,b=dfs()`

### 拷贝

- 大多数情况下python中默认是浅拷贝
  - 使用`copy.deepcopy(x)`可以实现深拷贝


## 类

- 定义：`class Name:`类名通常首字母大写

- 类中方法（函数）的第一个参数为self由系统自动传入，是一个指向实例本身的引用
- self.前缀定义的变量可以供类中所有方法使用（类似成员变量）
- `def _init_()`表示构造函数，当创建类的新实例时会自动调用
  - 在这里定义（初始化）成员变量
- 创建实例：`a=A()`使用构造函数来生成r
  - 访问属性和方法`.`

### 继承

- `class Son(Father):`末尾添加括号
- `super().`前缀用于访问父类的方法和属性

# 标准库

## 容器

### Counter

- Counter 是 dict 字典的子类，Counter 拥有类似字典的 key 键和 value 值
- 如果要使用 Counter，必须要进行实例化，在实例化的同时可以为构造函数传入参数来指定不同类型的元素来源（从 string（字符为list列表的元素）、list 和 tuple 这些可迭代对象中获取元素。）。
  - `x = Counter()`
  - 自动统计列表中个元素的数目
- 如果在 Counter 中查找一个不存在的元素，不会产生异常，而是会返回 0

#### 方法

- elements 方法：可以通过 list 或者其它方法将迭代器中的元素输出，输出的结果为对应出现次数的元素。
  - ` print(list(c.elements()))`
- `most_common([n])`是 Counter 最常用的方法，返回一个出现次数从大到小的前 n 个元素的列表。
  - most_common()[]
- subtract：将两个 Counter 对象中的元素对应的计数相减。
  - `c.subtract(d)`：两个字典相减

### collections.deque

- 初始化`q = collections.deque([])`
- 添加元素`q.append"()` `q.appendleft()`
  - 按照列表顺序添加多个元素：`extend([])` `extendleft()`
- 删除元素`q.pop()` `q.popleft()`
- 获取元素，直接下标访问，如`q[0]`

### collections.heapq堆

- 建立堆：（默认为小顶堆，可以通过取负数实现大顶堆）

  - `heapq.heappush(heap, item)`：向堆内添加元素，如果不是堆会自动转化为堆
  - `heapq.heapify(list)`:将列表转化为堆

- `heapq.heappop(heap)`弹出并返回元素

- 可以使用下标访问

- 不支持自定义比较，可以用类来包装数据，在类中重载比较运算符号

  - ```python
    class  P():
        def __init__(self,a,b):
            self.a = a
            self.b = b
        def __lt__(self, other):#重载<运算符
            if self.b<other.b:
                return True
            else:
                return False
    ```

### queue

- `from queue import PriorityQueue`需要引入

- 分类
  - queue.Queue	先进先出队列
  - queue.LifoQueue	后进先出队列（栈）
  - queue.PriorityQueue	优先级队列
  - queue.deque	双线队列
  - 使用时可以不带queue.前缀
- 初始化：`deque([])`使用列表初始化

#### 方法

- `put()`放数据
- `get()`	取数据
- `empty()`	如果队列为空，返回True,反之False
- `qsize()`	显示队列中真实存在的元素长度
- `pop()`
  - deque还有popleft
  - 会返回pop的值

### 有序容器sortedcontainers

- `SortedList` `SortedDict` `SortedSet`
- 导入：`from sortedcontainers import SortedList`
- 操作：
  - 初始化：`arr = SortedList([[初始数据列表]], [ket..])`
  - 操作与set类似

## 算法

### 二分

- `bisect(listname,num)`相当于C++中的upper_bound
- `bisect_left(listname,num)相当于C++中的lower_bound`

### re正则

- [正则表达式](../扩展/正则表达式.md)

- 引入`import re`
- `re.findall(pattern,string ,flags=0)`:搜索所有满足条件的字符串
- `re.match(pattern,string ,flags=0)`:从第一个字符开始匹配模式，匹配失败返回None
- `re.search(pattern,string ,flags=0)`:搜索第一个满足条件的字符串，查找到第一个停止
- `re.sub(pattern,repl,string ,count=0,flags=0)`:替换满足条件的字符串
- 正则表示`r".*"`添加前缀i

#### 例题

- [2299. 强密码检验器 II](https://leetcode.cn/problems/strong-password-checker-ii/)

- ```python
  import re
  class Solution:
      def strongPasswordCheckerII(self, password: str) -> bool:
          return re.match(r"^(?=.*\d)(?=.*[a-z])(?=.*[A-Z])(?=.*[!@#$%^&*()\-+])(?!.*(.)\1+).{8,}$", password) != None
  ```

### 记忆化搜索

- 函数前添加语句`@cache`
  - 结合tuple实现记忆化搜索

## numpy数学库

