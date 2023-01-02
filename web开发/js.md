 # 语法基础

## 变量

- 变量是松散类型的，可以用于保存任何类型的值，可以使用：`var const let`声明变量

### var

- `var 变量名;`可以在定义时直接赋值
  - 赋值时不仅可以改变值也可以改变类型（不推荐这么做）
- var声明有作用域限制，为函数作用域
  - 去掉var关键字直接声明变量可以实现在函数内声明全局变量
- 可以一次声明多个变量，用逗号隔开
- 所有变量声明都会被提升到函数开头，因此可以先使用变量，而声明写在后面

### let

- 作用域为块作用域（如一个if语句内）（比如在for循环中应该使用let）
- let声明不会被提升不能在声明语句之前使用变量
- let不允许重复声明一个变量（var和let也不能同时声重复明）

### const

- 与let接近
- 声明时必须初始化变量，不能再修改

### 数据类型

- typeof 变量名或typeof(变量名)返回表示变量类型的字符串
  - 对于一个未声明的变量也会返回undefined

- undefined未定义类型

  - 声明而没有初始化的变量的类型

- null表示空对象指针，typeof返回object

  - 变量将用于存储对象时建议用null初始化

- boolean布尔

- number

  - 使用八进制必须以0kaitou且后面的数字在0~7
  - 使用十六进制必须以0x开头
  - 范围
    - 最小为`Number.MIN_VALUE`最大为`Number.MAX_VALUE`
    - 超出范围的值会被转化为`Infinity`（无穷），不能用于进一步的计算
  - NaN表示不是数值，作为计算出错的返回值
    - 任何涉及NaN的操作仍返回NaN，并且NaN不等于包括自身在内的所有值
    - 可以使用`isNaN()`来判断是否时NaN或是否可以转化为数值
  - 数值转化
    - Number()：undefined返回NaN；字符串，如果字符串只包含数字（以及开头的正负号）则会转化为十进制数，不满足条件的转化为NaN，如果是浮点数则会转化为浮点数，如果是十六进制则会自动转化为十进制存储；对象调用valueof()并按规则转化返回值，如果时NaN则调用toString()在进行转化；
    - parseInt()：从第一个非空字符开始转化，如果第一个字符不是数字或正负号则返回NaN，如1234blue会返回1234，并且会将浮点数转化为整数。
      - 添加额外参数可以指定为不同进制parseInt（“字符串”，进制数）
    - parseFloat()：会转化前面符合语法的部分为浮点数如22.34.5->22.34
      - 如果没有小数电则会返回整数
      - 只能解析十进制不能指定底数

- string

  - length返回字符串长度（如`s.length`）

  - toString()将其他类型的变量转化为string如（`let a = 11; a.toString();`）

    - 可以添加一个参数，表示返回值的进制
    - 除了null和undefined以外都可以转化，（会直接返回类型名）
    - 给任意变量加上一个“”空字符串也可以实现转化

  - 模板字面量：赋值时不用引号而是反引号\`\`

    - 会保留换行符和空格，可以跨行定义字符串。

    - 可以插值

      - `value+'to the'+exponent`等价于\`\${ value } to the \${ exponent }\`
      - ${}会自动执行toString()
      - {}内可以是一个变量也可以是函数或方法

    - 标签函数：会接收被插值记号分隔后的模板和每个表达式求值的结果

      - ```javascript
        let a = 6;
        let b = 9;
        function simpleTag(strings, aValExpression, bValExpression, sumExpression) {
          console.log(strings);// ["", " + ", " = ", ""]
          console.log(aValExpression);// 6
          console.log(bValExpression);// 9
          console.log(sumExpression);// 15
          return 'foobar';
        }
        let taggedResult = simpleTag`${ a } + ${ b } = ${ a + b }`;
        ```

      - 由于表达式参数数目可变，可以使用剩余操作符将数据收入一个数组

      - ```javascript
        function simpleTag(strings, ...expressions) {
          console.log(strings);
          for(const expression of expressions) {
            console.log(expression);
          }
          return 'foobar';
        }
        ```

  - 原始字符串，添加关键字String.raw可以输出字符串的原宿内容而不是转义之后的（如/n）

    - `console.log(String.raw 'first line\nsecond line'); `

- symbol符号

  - 确保对象属性使用唯一标识符，不会发生属性冲突
  - 初始化`let sym = Symbol();`
    - 也可以传入一个字符串作为描述，便于以后的调试`let fooSymbol = Symbol('foo');`
    - 输出`Symbol(foo)`（描述相同的symbol的值并不相同）
    - Symbol()不能作为构造函数（如`let myString = new String();`）使用来创建object对象
  - 全局符号注册表
    - 实现不同部分共享和重用符号实例
    - `let fooGlobalSymbol = Symbol.for('foo');`具有相同键（字符串）的symbol具有相同的值
    - 字符串为空则为`Symbol(undefined)`
    - `Symbol.keyFor()`查询全局注册表返回符号对应的键
  - 内置函数（略，看不懂）

- object对象

  - 一组数据和功能的集合，通过new来创建
  - 属性和方法
    - constructor：创建当前对象的函数
    - hasOwnProperty(name)判断当前对象的实例上是否存在给定的属性（输入属性名的字符串）
    - isPrototypeof(object)：判断当前对象是否为另一个对象的原型
    - propertyIsEnumerable(name)：判断属性是否可以使用
    - toLocalString()：返回对象的字符串表示toString()
    - valueOf()返回对应的字符串或数值、布尔表示，通常与tostring一致

## 操作符

- ++ --
  - 可以作用于任何类型
    - 字符串如果是有效的数值形式则会转化类型为为数值并操作
    - 如果字符串不是有效的数值则返回NaN
    - 布尔值会先转化为数值再进行操作
- +-(正负号)
  - 放在非数值类型前面会自动执行转化为数值
- \>\>有符号右移（不改变符号位）
  - \>\>\>无符号右移空格补0对于负数来说有较大的区别
- **指数
  - 如10**9
- `== !=`等于和不等于，会自动进行类型转化
  - `===  !==`全等和不全等，不会自动进行类型转化

## 语句

- for-in用于枚举对象中的非符号键属性
  - `for (property in expression)`
- for-of遍历可迭代对象的元素
  - `for (const el in [2,4,6,8]) `
  - 如果尝试遍历的元素不支持迭代则会报错
- 标签，可以用于给语句加标签
  - `start: for (let i = 0; i < count; i++)`
  - 也可以对一个空行标记`end:`
- 在break或continue后接一个标签名，可以实现跳转到指定位置（类似goto）

## 函数

- 函数定义

```javascript
function functionName(arg0, arg2,...){}
```

## 原始值与引用值

- 原始值就是简单的数据，引用值是由多个值构成的对象

  - 原始值不能有属性，引用值可以动态的添加属性

  - ```javascript
    let name = "Nicholas";
    name.age = 27;
    console.log(name.age);  // undefined
    let name2=new String("Nicholas");//创建object对象
    name.age = 27;
    console.log(name.age);  // 27
    ```

- 复制上类似深浅拷贝

- 参数传递

  - 原始值是值传递
  - 引用值是引用传递（修改具有全局影响性）

# 扩展

### 严格模式

- 编译器会处理一些不规范的/不安全的活动
- 若对整个脚本使用，在开头添加`"use strict";`
- 若只想对单个函数执行，可以写在对应的函数体内部