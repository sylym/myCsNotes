 # javascript语法基础

## 使用

- 一般写在最底部（一般再`/body`上面），保证html元素已经全部出现过，使脚本正确执行

#### 内部形式

- 直接写在script语句块内

#### 外部形式

- `<script src="demo.js"></script>`

## 输入输出

- 输入
  - alert(..)浏览器弹出警示框
  - console.log(..)浏览器控制台打印输出信息（测试使用）
- 输出
  - prompt(..)浏览器弹出输入框，用户输入（返回用户的输入）

## 变量

- 变量是松散类型的，可以用于保存任何类型的值，可以使用：`var const let`声明变量

### var

- `var 变量名;`可以在定义时直接赋值
  - 赋值时不仅可以改变值也可以改变类型（不推荐这么做）
- var声明有作用域限制，为函数作用域
  - 去掉var关键字直接声明变量可以实现在函数内声明全局变量
- 可以一次声明多个变量，用逗号隔开
- 所有变量声明都会被**提升**到函数开头，因此可以先使用变量，而声明写在后面

### let

- 作用域为块作用域（如一个if语句内）（比如在for循环中应该使用let）
- let声明**不会被提升**不能在声明语句之前使用变量
- let不允许重复声明一个变量（var和let也不能同时声重复明）

### const

- 与let接近
- 声明时必须初始化变量，不能再修改

### 数据类型

- typeof 变量名或typeof(变量名)返回表示变量类型的字符串
  - 对于一个未声明的变量也会返回undefined

- instancef检测是否为指定数据类型
  
  - 如`a instanceof Array`
  
- undefined未定义类型

  - 声明而没有初始化的变量的类型

- null表示空对象指针，typeof返回object

  - 变量将用于存储对象时建议用null初始化

- boolean布尔

- number

  - 使用八进制必须以0开头且后面的数字在0~7
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

  - 赋值可以使用单引号或双引号

    - 需要嵌套时可以外双内单（使用不同符号区别）

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

### object对象

- 对象是一组无序的相关属性和方法的集合，所有的事物都是对象

- 创建

  - 字面量创建（使用{}）

    - 采用键值对的形式存储，用逗号隔开

    - ```javascript
      var obj ={
          user:'张三',
          sex:'男'，
          sayHi:function(){}
      }
      ```

  - new

    - `var obj=new Object();`

  - 构造函数

    - ```javascript
      function 构造函数名(...){
          this.属性=值;
          this.方法=function(){}
      }//不需要返回值
      
      ```

    - 调用：`var 对象名=new 构造函数名(...);`使用new

- 属性调用

  - 对象名.属性名
  - 对象名['属性名']

- 追加属性

  - 给不存在的属性赋值会自动创建

- 遍历对象

  - `for(var k in obj){ obj[k]}`k是变量名字符串

- 属性和方法
  - constructor：创建当前对象的函数
  - hasOwnProperty(name)判断当前对象的实例上是否存在给定的属性（输入属性名的字符串）
  - isPrototypeof(object)：判断当前对象是否为另一个对象的原型
  - propertyIsEnumerable(name)：判断属性是否可以使用
  - toLocalString()：返回对象的字符串表示toString()
  - valueOf()返回对应的字符串或数值、布尔表示，通常与tostring一致

- 补充
  - 强制类型转化与c一致


## 内置对象	

### Math

- Math是一个静态对象，不需要创建就可以直接使用

- 可以用于调用一系列数学常数
  - PI

- `Math.max(...)`输出最大值（可以输入任意多个数据，用逗号分隔）
  - min
- floor()向下取整
- ceil()向上取整
- round()四舍五入
- abs()
- random()随机小数(0,1]
  - 范围内随机整数`Math.floor(Math.random()*(max-min+1))+min;`[min,max]

### Date

- Date是一个构造函数，需要创建对象后再使用
  - `var a=new Date()`默认获取当前时间
- 传入参数会创立指定时间的对象
  - 格式
    - 数字：2019，10，01（10表示的是11月，实际会比数字大1）
    - 字符串：'2019-10-1 8: 8:8'
- 数字表示时数字月份比实际月份小一
  - 星期几表示为0（周日）-6
- 获取毫秒数（从1970.1.1开始）(转化为数字时自动转化为毫秒数)
  - valueof()或getTime()或直接Date.now()静态成员
  - 时间做差可以得到毫秒
  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/Snipaste_2023-01-03_22-25-30.png" alt="Snipaste_2023-01-03_22-25-30" style="zoom: 67%;" />
- 格式化
  - ![Snipaste_2023-01-03_21-44-28](https://thdlrt.oss-cn-beijing.aliyuncs.com/Snipaste_2023-01-03_21-44-28.png)

### 数组对象Array

- 创建 
  -    new创建`var arr=new Array();`
    - new Array(n)表示有n个空元素的数组
      - new Array(a,b,...)等价与字面值初始化
  - 数组字面量创建[]
    - `var arr=[...]`也可以创建空数组
- 成员函数
  - `isArray(obj)`判断是否是数组
  - `reverse()`反转数组（会对原数组操作，并且也会返回新数组）
  - `sort()`排（序会对原数组操作，并且也会返回新数组）
  - 查找元素（找不到时返回-1）
    - `indexOf(查找元素,[起始位置])`
    - `lastIndeOf()`
  - 数组转化为字符串
    - `toString()`逗号分隔数组每一项
    - `join('分隔符')`自定义分隔符
  - 长度获取`数组名.length`（得到的是左值）
- 增加元素
  - 修改length长度
    - 通过增加length长度会自动实现数组扩容（自动填充为undefined）
  - 修改索引号
    - 指定为定义的索引号会自动新增（类似map）
    - `arr[arr.length]`实现类似push_back的效果
  - push(...)末尾添加一或多个元素（返回值为数组长度）
  - unshift(...)向开头添加
- 删除元素
  - pop()删除最后一个元素
  - shift()删除第一个元素
- 数组中的元素数据类型可以不同

### 字符串对象

- 成员函数
  - 查找元素（找不到时返回-1）
    - `indexOf(查找元素,[起始位置])`
    - `lastIndeOf()`
  - 返回指定位置字符
    - `[index]`
    - `charAt(index)`返回字符
    - `charCodeAt(index)`返回ascll码
  - 截取`substr(start,length)`
    - `slice(start,end)`
  - 替换`replace('被替换的字符','替换为的字符')`
    - 只会替换匹配的第一个字符
  - 转化为数组`split('分隔符')`
    - 表示依据什么分隔字符串
  - `toUpperCase()`转化大写
    - `toLowerCase()`转化小写

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
  - `for (const el of [2,4,6,8]) `
  - 如果尝试遍历的元素不支持迭代则会报错
- 标签，可以用于给语句加标签
  - `start: for (let i = 0; i < count; i++)`
  - 也可以对一个空行标记`end:`
- 在break或continue后接一个标签名，可以实现跳转到指定位置（类似goto）

## 函数

- 函数定义

```javascript
function functionName(arg0, arg2,...){}//法一
var 变量名=function(){}//法二（匿名函数）类似函数指针
变量名()//调用上与函数类似
```

- 形参与实参数目不匹配
  - 实参多余形参时会自动抛弃多余的实参
  - 实参少于形参时形参会不初始化（即为undefined）
- 参数传递

  - 原始值是值传递
  - 引用值是引用传递（修改具有全局影响性）
- 没有返回值时返回undefined
- arguments用来获得不定数目的参数传递
  - 展示形式是一个伪数组，但不支持push和pop
- 立即执行函数：不需要调用立马能够自己执行的函数
  - 作用：创立了一个独立作用域

  - 语法
    - `(function(){})();`第二个小括号表示调用，可以在里面填写形参
    - `(function(){}());`


## 原始值与引用值

- 原始值就是简单的数据，引用值（new创建）是由多个值构成的对象

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

# Web API

## DOM页面文档对象模型

### 简介

![Snipaste_2023-01-03_23-11-19](https://thdlrt.oss-cn-beijing.aliyuncs.com/Snipaste_2023-01-03_23-11-19.png)

<img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/notestream.png"  />

- 先从上到下执行捕获，再从下到上执行冒泡，事件监听函数按照这个顺序依次执行

### 获取页面元素

- 操作时都是element.开头选定元素

- 通过id获取`getElementById(id)`

  - 前面需要限定词

    - 在文件作用域内寻找`document.getElementById(id)`

    - `父元素.getElementById('标签名')`父元素必须是一个单独元素，不能是集合

    - ```javascript
      var ol=document.getElementsByTag('ol');
      var li=ol[0].document.getElementsByTag('li');
      ```

  - id是一个字符串

  - 匹配成功时返回element对象(object)，没有找到时返回null

  - 由于文档从上到下加载，所以要**先出现id**再写script

- 通过标签名获取`getElementsByTagName(id)`

  - 返回带有指定标签名的元素集合（伪数组）
  - 找不到元素时返回空的伪数组

- 通过类名获取`getElementsByClassName('类名')`

- 通过选择器获取`querySelector('选择器')`

  - 返回选择器选择的元素中的第一个
  - 通过选择器获取`querySelectorAll('选择器')`
    - 返回全部

- 获取特殊元素标签

  - `document.body`获取body元素
  - `document.documentElement`获取html元素

### 节点操作 

- 利用父子兄节点层级关系获取元素
- 节点至少具有nodeType节点类型，nodeName节点名称，nodeValue节点值这三个基本属性
  - 元素节点nodeType为1，属性节点为2，文本节点(包含空格换行等)为3
- 父级节点`node.parentNode`用于快速拿到一个节点的父级节点
  - 找不到时返回空
- 获取子节点`node.childNodes`
  - 可能会获取文本额外节点（如果出现了换行及空格）
    - 因此如果只想要元素节点，需要通过nodeType属性筛选
  - `node.children`获取全部子元素节点
  - `firstChild`及`lastChild`也包括了文字节点
  - `firstElementChild`及`lastElementChild`只获取元素节点
- 兄弟节点
  - `node.nextSibling`及`node.previousSibling`返回下/上一个兄弟节点，包含文本节点
  - `node.nextElementSibling`及`node.previousElementSibling`不含文本节点 
- 创建节点（元素 ）
  - `document.creatElement('tagName')`创建tagName指定的HTML元素
- 添加节点
  - 创建完成的节点还有添加到页面中才会显示
    - `node.appendChild(child)`将一个节点添加到指定父节点列表的末尾
    - `node.insterBefore(child,指定元素)`在指定元素前面插入 
- 删除元素
  - `node.removeChild(child)`从node父节点中删除child子节点
- 复制节点 
  - `node.cloneNode()`生成的克隆节点可以使用var接收再添加到页面
    - 括号内为空或false仅复制标签不复制内容
    - 为true时还会复制内容


### 操作元素

- 改变元素内容（元素内文字）
  - `innerText`从起始位置到终止位置的内容，去除html标签以及空格和换行
  - `innerHTML`从起始位置到终止位置的全部内容，包括html标签，保留空格及换行
    - 可以用于向一个元素内添加标签（元素）
    - 如果需要创建多个重复元素（循环拼接）效率比较低
  - 比如修改为`<strong>...</strong>`Text会直接已字符串的形式输出标签，html则会转化文字为粗体
    - 读取时text会直接略过标签
  - 获取的是左值，可以直接修改
- 改变元素属性
  - 直接修改如`imd.src="";`
- 获取自定义属性`element.getAttribute('属性');`
  - 设置自定义属性`element.setAttribute('属性','值');`
  - 移除属性`element.removeAttribute('属性');`
  - 推荐自定义属性命名以data-开头`data-index`
    - h5新增获取方式`element.dataset.index`或`element.dataset['index']`
    - 如果属性内有多个-则要转化为驼峰命名法如`index-list-name`->`listName`

- 修改元素样式属性（css）
  - 通过style属性修改
    - `this.style.backgroundColor = 'purple'; `
    - 属性使用驼峰命名法而不是使用-
    - 属于行内样式
  - 使用className
    - 为元素添加类名（从而获得额外的css属性）
    - 会覆盖原先的类名
- 批量操作
  - 可以批量选择，通过选择实现

- 补充
  - 获取复选框的选中状态：checked属性（true表示选中）
  - tab任务栏切换，通过display：hidden（block）隐藏/显示来实现切换
  - 设置焦点`element.focus();`



### 事件

- 三部分组成：事件源、事件类型、事件处理程序

  - ```javascript
    var btn=document.getElementById('btn');
    btn.onlick=function(){//onlick(点击事件)
        alert('点秋香');
    }
    ```

  - 先获取事件源，然后注册事件，添加事件处理程序

  - 事件处理程序内this指向事件源

- 鼠标事件 

  - ![](https://thdlrt.oss-cn-beijing.aliyuncs.com/mouse.png)
  
  - mouseenter和mouseover都在鼠标移动到元素上时触发，区别是mouseover在鼠标经过自身盒子时会触发，经过子盒子时会再次触发，而mouseenter只在经过自身盒子时触发（因为mouseenter不会冒泡）

- 表单事件

  - onfucus获得焦点（如搜索框被点击）
  - nblur失去焦点

- 注册事件

  - 传统注册方式，如`btn.onclick=function(){}`
    - 一个元素同一个事件只能设置一个处理函数，后面的处理函数会覆盖前面的处理函数
  - 方法监听注册方式
    - `addEventListener('type',listern[,useCapture])`
      - userCapture为true表示捕获阶段，不填或false表示冒泡阶段
      - `btn.addEventListerner('click',function(){})`
      - type：事件类型字符串如click，mouseover（前面不带on）
      - listern：事件处理函数
    - 同一元素同一事件可以注册多个监视器，按注册顺序依次执行

- 删除事件

  - 传统方式：`element.onclick=null;`置为null

  - 监听方式：添加函数时不能使用匿名函数了，否则无法删除

  - ```javascript
    btn.addEventListener)('click',fn)
    function fn(){
        ...
    }
    btn.removeEventListener('click,fn');//解绑
    ```

- 事件对象

  - function内增加参数`function(event)`参数名称可以自定义不一定是event
  - 事件对象由系统自动创建不需要传递参数，是一系列与事件相关数据的集合
  - 属性：
    - ![](https://thdlrt.oss-cn-beijing.aliyuncs.com/event.png)

  - e.target是触发事件的对象，this是绑定事件的对象
  - 阻止默认行为：让链接不跳转或者提交按钮不提交
    - `event.preventDefault();`
  - 阻止事件冒泡 ：不再继续向上传播

- 鼠标事件对象属性

  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/mouseevent.png" style="zoom: 67%;" />
  - client：坐标不会因为窗口滑动而变化，是相对目前看到的窗口的坐标
  - page：随滑动发生变化，是相对于整个文档页面最上端的距离

- 键盘事件

  -  执行顺序 down press up![](https://thdlrt.oss-cn-beijing.aliyuncs.com/keyevent.png)
    - keyup keydown不区分大小写（事件对象返回相同）keypress区分

  - 事件对象属性
    - keyCode返回ascll值

- 事件委托
  - 给父节点添加事件处理函数可以避免给多个子节点重复添加绑定，利用冒泡原理影响每个子节点
- 补充
  - 禁用右键菜单：将contextmenu事件设置为阻止默认行为
  - 禁止选中文字：selectstart设置为阻止默认行为

## BOM浏览器对象模型

### 概念

- 提供独立于内容与**浏览器**窗口进行交互的对象，顶级对象是windows
- 定义在全局作用域中的变量函数都会变为window对象的属性和方法
- 是js访问浏览器窗口的接口

<img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/bom.png" style="zoom:67%;" />

### 事件

- 窗口加载事件

  - `window.onload`当文档内容完全加载完成时触发事件
  - DOMCountLoaded事件仅当dom加载完成时，不包括样式表、图片等
  - 可以把js代码用`window.addEventListerner('load',function(){})`包住，即当页面全部加载完成之后再运行脚本，这样就不需要在元素出现之后再写脚本,可以把脚本写在页面最前面

- 调整窗口大小事件

  - onresize事件

  - window.innerWidth获取当前屏幕的宽度

- 定时器

  - `var name=[window.]setTimeout(调用函数，[延迟毫秒数]);`在定时器到期后执行调用函数
    - 可以给定时器起名字，便于之后的操作
    - 停止定时器`[window.]clearTimeout(name);`
  - `setInterval(回调函数,[间隔的毫秒数]);`**重复调用**一个函数，每隔一段时间就调用一次
    - 清楚`clearInterval()`

### location对象

- 用于获取或设置窗体的url（统一资源定位符），并且可以用于解析url
  - ![](https://thdlrt.oss-cn-beijing.aliyuncs.com/url.png)

- 属性：
  - ![](https://thdlrt.oss-cn-beijing.aliyuncs.com/location.png)

- 通过url传递参数
  - 使用input提交按钮后表单数据会被保存在url的参数部分（使用form指定跳转地址，跳转后的网页可以通过html获取信息）
- 方法
  - ![](https://thdlrt.oss-cn-beijing.aliyuncs.com/lmethod.png)
  - assign()会记录历史可以实现后退功能被

### navigator对象

- 包含有关浏览器的信息，如判断是手机端还是pc端
- userAgent属性获取用户终端信息 

### history对象

- 与浏览器的历史记录进行交互
- 方法(实际上就是模拟浏览器上的前景后退按钮)
  - ![](https://thdlrt.oss-cn-beijing.aliyuncs.com/hmethod.png)

## 网页特效

### 元素偏移量offset

- 常用属性（均使用element.来调用）（注意返回值吗，没有单位）
  - `offsetParent`：返回带有定位的父级元素（没有则返回播放body）
  - `offsetTop` `offsetLeft`：返回相对带有定位的父元素上、左方的偏移
  - `offsetWidth` `offsetHeight`：返回自身包括padding、边框、内容区的宽度、高度
- ![](https://thdlrt.oss-cn-beijing.aliyuncs.com/offset.png)

### 元素可视区client

- 用于获取元素可视区的相关信息，如元素的边框大小，元素大小
- ![](https://thdlrt.oss-cn-beijing.aliyuncs.com/client.png)

### 元素滚动scroll

- 与滚动条有关系
- ![](https://thdlrt.oss-cn-beijing.aliyuncs.com/scroll.png)

- scroll是内容实际宽高，包括溢出的部分
- 被卷去的部分指的是由于滚动条滚动被隐藏的部分
- onscroll事件：拖动滚动条时触发
- 页面被卷去的大小
  - 头部：`window.pageYOffset`
  - 左侧：`window.pageXOffset`
- 窗口滚动操作：`window.scroll(x,y)`不用跟单位，瞬时移动  

### 动画

- 原理：通过定时器打点动画
- 简单动画函数封装
  - 只需要传入参数：动画对象，动画内容（如移动距离、移动速度）
  - 实现给不同元素开辟不同计时器：可以把计时器作为传入元素的属性存在
    - `obj.timer=setInterval(...);`
- 缓动动画
  - 算法：（目标值-现在的位置）/10作为每次移动的步长
- 节流阀：当上一个动画执行完成后再去执行下一个操作，让事件无法连续触发
  - 思路：利用回调函数（对动画函数传入一个函数用于在动画函数执行完毕后执行），和一个变量来锁住和解锁函数

## 本地存储

- 将数据存储在本地浏览器中，只能存储字符串

### sessionStorage

- 特点
  - 生命周期为关闭浏览器窗口
  - 在同一窗口（页面）下数据可以共享
  - 以键值对的形式存储
- 存储数据：`sessionStorage.setItem(key,value)`
  - 同一个key会覆盖存储
- 获取数据：`sessionStorage.getItem(key)`
  - 可以装进if潘多数据是否存在（能否获取）
- 删除数据：`sessionStorage.removeItem(key)`
- 删除所有数据：`sessionStorage.claear()`

### localStorage

- 特点
  - 生命周期永久生效，除非手动删除，否则关闭页面也会存在
  - 可以多窗口共享数据
  - 以键值对的形式存储
- 操作一致

## 数据可视化

- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/echarts.png" style="zoom:80%;" />

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="echarts.js"></script>//引入文件
    <style>
        .box {
            width: 400px;
            height: 400px;
            background-color: pink;
        }
    </style>
</head>
<body>
    <div class="box"></div>//准备容器
    <script>
        var myChart = echarts.init(document.querySelector('.box'));//实例化对象
        var option = {//指定配置项和数据
            title: {
                text: 'ECharts 入门示例'
            },
            tooltip: {},
            legend: {
                data: ['销量']
            },
            xAxis: {
                data: ['衬衫', '羊毛衫', '雪纺衫', '裤子', '高跟鞋', '袜子']
            },
            yAxis: {},
            series: [
                {
                    name: '销量',
                    type: 'bar',
                    data: [5, 20, 36, 10, 10, 20]
                }
            ]
        };
        myChart.setOption(option);//将配置项实例给对象
    </script>
</body>
</html>
```

### 选择不同样式的图表

- [官方示例](https://echarts.apache.org/examples/zh/index.html)

### 配置设置

- [配置文档](https://echarts.apache.org/zh/option.html#title)
- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/ec.png" style="zoom: 67%;" />

- ![](https://thdlrt.oss-cn-beijing.aliyuncs.com/ttol.png)

- color与serious后面跟数组`[]`其余后面跟对象`{}`

# 扩展

### 预解析

- 所有var和function声明会被提升到作用域最前面
  - 变量提升：只提升声明，不提升赋值（提升区域内为undefined）

### 严格模式

- 编译器会处理一些不规范的/不安全的活动
- 若对整个脚本使用，在开头添加`"use strict";`
- 若只想对单个函数执行，可以写在对应的函数体内部

### 调试

- 浏览器中调试
  - f12选择sources在代码中调试

### this指向

- this的指向在函数定义的时候是确定不了的，只有函数执行的时候才能确定this到底指向谁，一般情况下this的最终指向的是那个调用它的对象现阶段，我们先了解一下几个this指向

  - 全局作用域或者普通函数中this指向全局对象window（注意定时器里面的this指向window）

  - 方法调用中谁调用this指向谁

  - 构造函数中this指向构造函数的实例

### 同步与异步

- js执行机制：先执行执行栈中的同步任务，把异步任务（回调函数）放入任务队列中，执行栈中的同步任务执行完成之后系统才会按次序执行任务队列中的异步任务，异步任务进入执行栈，开始执行

![](https://thdlrt.oss-cn-beijing.aliyuncs.com/dif.png)

