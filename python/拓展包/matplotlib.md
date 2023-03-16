# Matplotlib

- 用于绘制2d图像

- 导入

  - ```python
    import matplotlib.pyplot as plt
    plt.rcParams["font.sans-serif"]=["SimHei"] #设置字体
    plt.rcParams["axes.unicode_minus"]=False #该语句解决图像中的“-”负号的乱码问题
    ```

    

### 图形构成

- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316110127313.png" alt="image-20230316110127313" style="zoom:50%;" />
- Figure：指整个图形，可以把它理解成一张画布，它包括了所有的元素，比如标题、轴线等；
- Axes：绘制 2D 图像的实际区域，也称为轴域区，或者绘图区；
- Axis：指坐标系中的垂直轴与水平轴，包含轴的长度大小（图中轴长为 7）、轴标签（指 x 轴，y轴）和刻度标签；
- Artist：在画布上看到的所有元素都属于 Artist 对象，比如文本对象（title、xlabel、ylabel）、Line2D 对象（用于绘制2D图像）等。

# 常用API

## 属性设置

- plt.xlabel()：设置x轴的标签。
  - 使用：`plt.xlabel("xxx")`
- plt.ylabel()：设置y轴标签。
- plt.title：设置图的标题。
- plt.xlim():设置x轴的起始坐标
- plt.ylim():设置y轴的起始坐标。
- plt.legend()：显示图例，即图中表示每条曲线的标签和样式的矩形区域。

## 常用操作

- `plt.plot()`用于画图，它可以绘制点和线, 并且对其样式进行控制。

  - ```python
    plt.plot([x], y, [format_string], **kwargs)
    ```

    - `x` 传入 x 轴坐标，`y` 传入对应 y 轴坐标。
    - `format_string` 传入格式化字符串。
      - 包括三部分, "颜色", "点型", "线型"。
      - 颜色如c清；r红；w白；k黑...
      - 点型：<img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316131748398.png" alt="image-20230316131748398" style="zoom:50%;" />
      - 线型：`:`点线；`-.`点画线：`--`短划线；`-`实线
      - 使用时按顺序写在一起`plt.plot(x, y2, 'ro-', ms=3)`
    - `**kwargs` 传入关键字参数控制线的粗细等内容。
      - lw线宽
      - ms点直径
    
  -  plot 函数允许多次调用，以在同一张图上绘制多个线条。当你调用 plot 函数时，它会将你提供的数据添加到当前图形的图层中。
  
  - 在调用 show 函数之前，你可以在同一个图形上进行多次绘制操作，包括绘制多个子图、绘制多条曲线等。当你调用 show 函数时，Matplotlib会将所有已添加的图层组合起来，并显示在同一个窗口中。
  
  - 例
  
    - ```python
      import matplotlib.pyplot as plt
      import numpy as np
      
      # 创建数据
      x = np.linspace(0, 10, 100)
      y1 = np.sin(x)
      y2 = np.cos(x)
      
      # 绘制两条线
      plt.plot(x, y1)
      plt.plot(x, y2)
      
      # 添加图例和标题
      plt.legend(['Sin', 'Cos'])
      plt.title('Sin and Cos Waves')
      
      # 显示图形
      plt.show()
      ```

- `grid()`网格线

- `subplot()`子图

  - ```python
    x = np.array([0, 6])
    y = np.array([0, 100])
    
    plt.subplot(2, 4, 1)
    plt.plot(x,y)
    plt.title("plot 1")
    ```

  - 表示构建2行4列的子图，这是第一个（按从左到右从上到下的顺序编号）

    - 1234
    - 5678

## 不同类型的图

### scatter散点图

#### 示例

- ```python
  x = np.array([1, 2, 3, 4, 5, 6, 7, 8])
  y = np.array([1, 4, 9, 16, 7, 11, 23, 18])
  
  sizes = np.array([20,50,100,200,500,1000,60,90])
  colors = np.array(["red","green","black","orange","purple","beige","cyan","magenta"])
  
  plt.scatter(x, y, c=colors, s=sizes)
  plt.show()
  ```

  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316133314919.png" alt="image-20230316133314919" style="zoom:50%;" />

### bar柱状图

#### 示例

- ```python
  x = np.array(["Runoob-1", "Runoob-2", "Runoob-3", "C-RUNOOB"])
  y = np.array([12, 22, 6, 18])
  
  plt.bar(x, y,  color = ["#4CAF50","red","hotpink","#556B2F"], width=0.5)
  plt.show()
  ```

  - <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316133441916.png" alt="image-20230316133441916" style="zoom:50%;" />

### pie饼图

#### 示例

- ```python
  y = np.array([35, 25, 25, 15])
  
  plt.pie(y,
          labels=['A','B','C','D'], # 设置饼图标签
          colors=["#d5695d", "#5d8ca8", "#65a479", "#a564c9"], # 设置饼图颜色
          explode=(0, 0.2, 0, 0), # 第二部分突出显示，值越大，距离中心越远
          autopct='%.2f%%', # 格式化输出百分比
         )
  
  plt.show()
  ```

- <img src="https://thdlrt.oss-cn-beijing.aliyuncs.com/image-20230316135158471.png" alt="image-20230316135158471" style="zoom:50%;" />
