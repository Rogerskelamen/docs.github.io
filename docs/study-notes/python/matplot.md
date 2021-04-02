# 使用matplotlib库的心得

> 我常常使用一个库的时候还又要看看网上的文档（*比如`matplotlib`和`pandas`之类的常用库*），于是在此记录我使用过的`matplotlib`心得。

首先是引入`matplotlib`库（首先你得用`pip`安装一下这个库）:

```python
import matplotlib.pyplot as plt
# 一般情况下我们都是使用plt来当作我们引入的画图对象
```

## 开始使用

一般情况下，我们都是使用`plt`的`plot()`方法来画出我们想要的图案，然而这个函数中最重要的两个参数就是俩列表——分别代表着X轴上的所有数据和Y轴上的所有数据。

```python
x = range(20)
y = range(5, 100, 5)
plt.plot(x, y)
plt.show()
# 一定不要忘了加上show()来显示我们的图象
```

*然后一个图就画好了*

## 画散点图

在我们画好了一个图之后，我们发现`plot`自动将所有的数据连接到了一起，这是因为`plot`默认画的是折线图。但是我们要画散点图怎么办呢？

### 使用`plot`自带的参数来控制

可以使用`'o'`来代表“点”（默认情况下的参数是`'-'`）：

```python
plt.plot(x, y, 'o')
plt.show()
```

### 使用`scatter()`函数

```python
plt.scatter(x, y)
plt.show()
```
*建议使用这种方式*

#### 对`scatter`函数的扩展

常用参数：

- `s`

用于更改点的大小，默认大小是30（好像）。

- `alpha`

用于更改点的透明度，默认不透明

## 更改画布的大小

实际上可以在初始化`figure`的时候设置大小

```python
plt.figure(figsize=(6, 6.5))
```

注意，这里的画面大小其实是 600 * 650的。

## 将多个子图画到一个画布上

可以使用`subplot()`函数

**主要有三个参数：前两个决定页面布局，第一个参数是代表行，第二个代表列，第三个参数代表这一行的第几个。**

参考[这篇文章](https://blog.csdn.net/gatieme/article/details/61416645)


## 其他注意的记录

***还是多一嘴，一定要在最后加上`plt.show()`!***

*ps：每个`plt`调用的方法都可以返回一个plt图像的对象*

- `plt.plot(color=)`

用于选中是什么颜色

- `plt.xlabel()`

显示X轴标签

- `plt.ylabel()`

显示Y轴标签

- `plt.xlim()`

设置X轴坐标范围，同理还有`ylim()`就不说了

- `plt.sca()`

将当前的图像绘制对象变成参数中的对象，和`subplot`配合使用。
