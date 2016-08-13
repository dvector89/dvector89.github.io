---
layout:	post
title:	Python绘图指南
category:	tech
description:	Python绘图指南
---
以前在学校的时候，画图都是用的MATLAB。工作之后，几乎没有用MATLAB的机会了，取而代之的是Python。Python最有名的绘图库就是matplotlib了。matplotlib的用法比较繁杂。从其官网的example和gallery栏目所包含的样例数目就可以领略到了。  

一、 默认绘图及框架  
---
为了以后画图不再手忙脚乱的翻文档，为了以后不再强行照猫画虎，为了减少测试图像效果的次数...... 我决定了解下matplotlib画图的大体框架。首先从一个简单的例子开始:  

```python
import numpy as np
from matplotlib.figure import Figure
from matplotlib.backends.backend_agg import FigureCanvasAgg as FigureCanvas

X = np.linspace(-np.pi, np.pi, 256, endpoint=True)
C = np.cos(X)

fig = Figure(figsize=(8, 8))
ax = fig.add_axes((0.1, 0.1, 0.8, 0.8))
line, = ax.plot(X, C)

canvas = FigureCanvas(fig)
canvas.print_figure('../pic/cos.jpg')
```
生成的图片如下：  
![](/images/cos.jpg)

具体分析下这个程序：  
（1）第5、6两行用来生成要展示的数据，即变量的映射关系。  
![](/images/matplotlib.png)  
（2）第8~10三行在计算机内部生成图的对象实例。如上图所示，在matplotlib中实际的图用类Axes表示，一个或多个Axes对象都保存在Figure类的对象中，Figure类的对象相当于一个容器。程序第8行生成了一个长宽为8英寸(inch)的Figure对象实例。第9行在fig中加入了一个Axes对象实例ax，第10行即在ax中载入了变量的余弦映射关系。  
（3）上一步已经在计算机内部完成了图的绘制。但图形是要展示给人看的，所以要以某种形式让图形以文件的方式保存起来（当然也可以直接在屏幕上显示出来）。第12行生成了一个FigureCanvas类对象canvas，并让fig与canvas产生关联。最后一行是canvas执行图片保存命令，将图片存储成文件。

二、 加入title、xlabel、ylabel
---  
以上绘制的图像可以看出表示的是横纵坐标的余弦曲线关系。但是要表示实际世界的物理量目前这个图就有些不足：  
（1）缺少横纵坐标的物理量单位和物理量说明  
（2）缺少曲线的主题和意图说明
例如，这个图表示的弹簧的简谐振动。横轴表示时间t，单位为s；纵轴表示弹簧的振幅amplitude，单位为cm。那么就要给ax对象设置title、xlabel、ylabel等信息。新的程序如下：  

```python
import numpy as np
from matplotlib.figure import Figure
from matplotlib.backends.backend_agg import FigureCanvasAgg as FigureCanvas

X = np.linspace(-np.pi, np.pi, 256, endpoint=True)
C = np.cos(X)

fig = Figure(figsize=(8, 8))
ax = fig.add_axes((0.1, 0.1, 0.8, 0.8))
line, = ax.plot(X, C)
ax.set_title("Simple Harmonic Motion")
ax.set_xlabel("time (s)")
ax.set_ylabel("amplitude (cm)")

canvas = FigureCanvas(fig)
canvas.print_figure('cos_1.jpg')
```
图的效果如下：  
![](/images/cos_1.jpg)  

三、 设置左边区间
---
这时的图已经具有物理意义了。但我们发现纵轴的区间范围为[-1.0, 1.0]，相对曲线有点小。调整横纵轴的区间范围就要设置xlim和ylim了。 
 
```python
import numpy as np
from matplotlib.figure import Figure
from matplotlib.backends.backend_agg import FigureCanvasAgg as FigureCanvas

X = np.linspace(-np.pi, np.pi, 256, endpoint=True)
C = np.cos(X)

fig = Figure(figsize=(8, 6))
ax = fig.add_axes((0.1, 0.1, 0.8, 0.8))
line, = ax.plot(X, C)
ax.set_title("Simple Harmonic Motion")
ax.set_xlabel("time (s)")
ax.set_ylabel("amplitude (cm)")
ax.set_xlim((X.min()*1.1, X.max()*1.1))
ax.set_ylim((C.min()*1.1, C.max()*1.1))

canvas = FigureCanvas(fig)
canvas.print_figure('../pic/cos_2.jpg')
```
效果为：  
![](/images/cos_2.jpg)  

四、 设置坐标轴位置
---
上面的图中都有一个框，一个框为4条线。在matplotlib中是Axes的四个spines，按方位分别是bottom、top、left、right。图中的4个spines都有刻度线，其中bottom和left默认还有数字。在数学图形中，往往只是两条坐标轴即可。代码可调整为：  

```python
import numpy as np
from matplotlib.figure import Figure
from matplotlib.backends.backend_agg import FigureCanvasAgg as FigureCanvas

X = np.linspace(-np.pi, np.pi, 256, endpoint=True)
C = np.cos(X)

fig = Figure(figsize=(8, 6))
ax = fig.add_axes((0.1, 0.1, 0.8, 0.8))
line, = ax.plot(X, C)
ax.set_title("Simple Harmonic Motion")
ax.set_xlabel("time (s)")
ax.set_ylabel("amplitude (cm)")
ax.set_xlim((X.min()*1.1, X.max()*1.1))
ax.set_ylim((C.min()*1.1, C.max()*1.1))

ax.spines['right'].set_color('none')
ax.spines['top'].set_color('none')
ax.xaxis.set_ticks_position('bottom')
ax.yaxis.set_ticks_position('left')
ax.spines['left'].set_position(('data', 0))
ax.spines['bottom'].set_position(('data', 0))

ax.xaxis.set_label_coords(1.05, 0.5)
ax.yaxis.set_label_coords(0.4, 0.1)

canvas = FigureCanvas(fig)
canvas.print_figure('../pic/cos_3.jpg')
```
效果为：
![](/images/cos_3.jpg)  

五、 显示多条曲线和图例
---
如果要在一个axes中显示多条曲线呢？

```python
import numpy as np
from matplotlib.figure import Figure
from matplotlib.backends.backend_agg import FigureCanvasAgg as FigureCanvas

X = np.linspace(-np.pi, np.pi, 256, endpoint=True)
C = np.cos(X)
S = np.sin(X)

fig = Figure(figsize=(8, 6))
ax = fig.add_axes((0.1, 0.1, 0.8, 0.8))
line1, = ax.plot(X, C)
line2, = ax.plot(X, S)
ax.set_title("Simple Harmonic Motion")
ax.set_xlabel("time (s)")
ax.set_ylabel("amplitude (cm)")
ax.set_xlim((X.min()*1.1, X.max()*1.1))
ax.set_ylim((C.min()*1.1, C.max()*1.1))

ax.spines['right'].set_color('none')
ax.spines['top'].set_color('none')
ax.xaxis.set_ticks_position('bottom')
ax.yaxis.set_ticks_position('left')
ax.spines['left'].set_position(('data', 0))
ax.spines['bottom'].set_position(('data', 0))

ax.xaxis.set_label_coords(1.05, 0.5)
ax.yaxis.set_label_coords(0.4, 0.1)

fig.legend((line1, line2), ('cos', 'sin'), 'upper right')

canvas = FigureCanvas(fig)
canvas.print_figure('../pic/cos_sin.jpg')
```
效果如下：  
![](/images/cos_sin.jpg)  
此外，图中的text都可以含有公式（只需用$包围起来即可）。  

参考：  
（1）[http://www.cnblogs.com/vamei/archive/2013/01/30/2879700.html](http://www.cnblogs.com/vamei/archive/2013/01/30/2879700.html)  
（2）[http://www.labri.fr/perso/nrougier/teaching/matplotlib/#simple-plot](http://www.labri.fr/perso/nrougier/teaching/matplotlib/#simple-plot)  
（3）[http://matplotlib.org/faq/usage_faq.html#what-is-a-backend](http://matplotlib.org/faq/usage_faq.html#what-is-a-backend)