# python语法之——print,input

> ### print是最常见的输出方式，里面可以接字符串等数据类型

### 关于字符串

* `print()`里的字符串一般用`,`连接，然后`,`会被解释成空格，两字符串连接之后中间会出现空格。

  ***ps：变量和字符串用`,`连接也是这种情况。***

* 如果用`+`连接，则会出现和其他解释型语言*（如JS）*一样的现象——字符串拼接，不会再有空格。

* 如果字符串不是写在`''`或是`""`中的，是写在`"""..."""`中的，里面有转行的话，输出也会自动转行

> ### 用input可以实现最一般的输入操作

input用法：用一个变量接收`input()`的返回值，其返回值即为用户输入的数据





# python语法之——双“除”狂喜

> ### 因为python是脚本语言，所以没有变量类型的声明，于是乎python的除法出现了两种（分别对应于浮点和整形）

* 用`/`的除法算式除法结果为浮点型
* 用`//`的除法算式其结果为整形（自动取整）

1. ***注意：Python的整数没有大小限制，而某些语言的整数根据其存储长度是有大小限制的，例如Java对32位整数的范围限制在`-2147483648`-`2147483647`。***
2. ***Python的浮点数也没有大小限制，但是超出一定范围就直接表示为`inf`（无限大）。***





# python语法之——python字符串

> ##### previously:在最新的Python 3版本中，字符串是以Unicode编码的

**对于单个字符的编码，Python提供了`ord()`函数获取字符的整数表示，`chr()`函数把编码转换为对应的字符**

> 那要不那么占空间的字符串怎么办呢？（一个字符一个字节）

**Python对`bytes`类型的数据用带`b`前缀的单引号或双引号表示**

```python
x = b'ABC'
```

以Unicode表示的`str`通过`encode()`方法可以编码为指定的`bytes`

```python
>>> 'ABC'.encode('ascii')
b'ABC'
>>> '中文'.encode('utf-8')
b'\xe4\xb8\xad\xe6\x96\x87'
```

*这里说明一下：纯英文的`str`可以用`ASCII`编码为`bytes`，内容是一样的，含有中文的`str`可以用`UTF-8`编码为`bytes`。*

反过来，如果我们从网络或磁盘上读取了字节流，那么读到的数据就是`bytes`。要把`bytes`变为`str`，就需要用`decode()`方法：

```python
>>> b'ABC'.decode('ascii')
'ABC'
>>> b'\xe4\xb8\xad\xe6\x96\x87'.decode('utf-8')
'中文'
```

*如果`bytes`中只有一小部分无效的字节，可以传入`errors='ignore'`忽略错误的字节(作为第二个参数)*

#### 要计算`str`包含多少个字符，可以用`len()`函数：

```python
>>> len('ABC')
3
>>> len('中文')
2
```

> 由于Python源代码也是一个文本文件，所以，当你的源代码中包含中文的时候，在保存源代码时，就需要务必指定保存为UTF-8编码。当Python解释器读取源代码时，为了让它按UTF-8编码读取，我们通常在文件开头写上这两行：
>
> ```python
> #!/usr/bin/env python3
> # -*- coding: utf-8 -*-
> ```

### 字符串的格式化

和C语言一样，`%`运算符就是用来格式化字符串的。在字符串内部，`%s`表示用字符串替换，`%d`表示用整数替换，有几个`%?`占位符，后面就跟几个变量或者值，顺序要对应好。如果只有一个`%?`，括号可以省略。

##### 常用占位符

| **占位符** | **替换内容** |
| ---------- | ------------ |
| %d         | 整数         |
| %f         | 浮点数       |
| %s         | 字符串       |
| %x         | 十六进制整数 |

其中，格式化整数和浮点数还可以指定是否补0和整数与小数的位数(类似于C语言)

***ps:有些时候，字符串里面的`%`是一个普通字符怎么办？这个时候就需要转义，用`%%`来表示一个`%`***

> ##### 另一种格式：`format`
>
> 它会用传入的参数依次替换字符串内的占位符`{0}`、`{1}`……，不过这种方式写起来比%要麻烦得多：
>
> ```python
> >>> 'Hello, {0}, 成绩提升了 {1:.1f}%'.format('小明', 17.125)
> 'Hello, 小明, 成绩提升了 17.1%'
> ```





# python语法之——列表(shu zu)，元组

* ## 列表（list）

  | 函数       | 作用                 |
  | ---------- | -------------------- |
  | `len()`    | list长度             |
  | `append()` | 追加元素到末尾       |
  | `insert()` | 插入到指定的位置     |
  | `pop()`    | 要删除指定位置的元素 |

  ***ps：和C语言不一样，list里面的元素的数据类型也可以不同***
  ***ps：可以套娃***

  

* ## 元组（tuple）

  <u>*tuple和list非常类似，但是tuple一旦初始化就不能修改。*</u>
  <u>*它也没有append()，insert()这样的方法你可以正常地使用`classmates[0]`，`classmates[-1]`，但不能赋值成另外的元素。*</u>

  > Q:不可变的tuple有什么意义？

  ==A: 因为tuple不可变，所以代码更安全。==

  ### A Important Trap

  如果要定义一个空的tuple，可以写成`()`：

  ```python
  >>> t = ()
  >>> t
  ()
  ```

  这没有问题，但是，要定义一个只有1个元素的tuple，如果你这么定义：

  ```python
  >>> t = (1)
  >>> t
  1
  ```

  ==warning==：定义的不是tuple，是`1`这个数！这是因为括号`()`既可以表示tuple，又可以表示数学公式中的小括号，这就产生了歧义，因此，Python规定，这种情况下，按小括号进行计算，计算结果自然是`1`。

  所以，只有1个元素的tuple定义时必须加一个逗号`,`，来消除歧义：

  ```python
  >>> t = (1,)
  >>> t
  (1,)
  ```

  > ### 探究tuple“不变”
  >
  > tuple所谓的“不变”是说，tuple的每个元素，指向永远不变。即指向`'a'`，就不能改成指向`'b'`，指向一个list，就不能改成指向其他对象，但指向的这个list本身是可变的！





#  python语法之——循环语句

> 相较于其他的编程语言python的循环语句有些不一样*（if条件语句就差不多和其他的类似，除了一个elif语法）*，**它并非像常见的语言那样 `for(i = 0; i < n; i ++)`**，（话说它根本就没有`()`这种东西），而是**用list或是tuple来指定性的进行循环**，具体如下。

1. 第一种`for x in ...`循环就是把每个元素代入变量`x`，然后执行缩进块的语句

   ```python
   sum = 0
   for x in [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]:
       sum = sum + x
   print(sum)
   ```

2. 第二种循环是while循环，只要条件满足，就不断循环，条件不满足时退出循环。

   ```python
   sum = 0
   n = 99
   while n > 0:
       sum = sum + n
       n = n - 2
   print(sum)
   ```

   **这种设计使得while循环在python中的比重要远大于其在其他语言中。**

> 还有我们熟悉的break和continue也是和常规用法一样





# python语法之——字典（dict）

> 这里的python字典，类似于其他语言中的map，是一种非常强大的数据类型。其以键值对（key—value）的形式存储数据，无序排列。

**<u>和JS有所不同的是，python的dict的键（key）也要写上类似于字符串形式的引号。</u>其实也可以写其他类型的key，但字符串用的最多。**

*ps：想要为dict添加一个元素，可以用这种形式：*

```python
>>> d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
>>> d['Adam'] = 67
>>> d['Adam']
67
```

***注：dict型的数据查找是最快的***

查找元素是否在字典中：通过`in`

```python
>>> 'Thomas' in d
False
```

#### 一些dict的内置函数：

| 函数  | 用法                                                         |
| :---- | ------------------------------------------------------------ |
| get() | 通过dict提供的`get()`方法，如果key不存在，可以返回`None`，或者自己指定的value |
| pop() | 删除一个key                                                  |

和list比较，dict有以下几个特点：

1. 查找和插入的速度极快，不会随着key的增加而变慢；
2. 需要占用大量的内存，内存浪费多。

而list相反：

1. 查找和插入的时间随着元素的增加而增加；
2. 占用空间小，浪费内存很少。

**所以，dict是用空间来换取时间的一种方法。**
**dict在Python代码中几乎无处不在，正确使用dict非常重要，需要牢记的第一条就是dict的key必须是==不可变对象==。** *(源于Hash算法）*

<u>在Python中，字符串、整数等都是不可变的，因此，可以放心地作为key。而list是可变的，就不能作为key：</u>

```python
>>> key = [1, 2, 3]
>>> d[key] = 'a list'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unhashable type: 'list'
```





# python语法之——set

> 在python中，set和dict类似，也是一组key的集合，但不存储value。由于key不能重复，所以，在set中，没有重复的key。（个人认为是python中最特殊的数据存储类型）

要创建一个set，需要提供一个list作为输入集合：

```python
>>> s = set([1, 2, 3])
>>> s
{1, 2, 3}
```

注意，传入的参数`[1, 2, 3]`是一个list，而显示的`{1, 2, 3}`只是告诉你这个set内部有1，2，3这3个元素，显示的顺序也不表示set是有序的。。
重复元素在set中自动被过滤：

```python
>>> s = set([1, 1, 2, 2, 3, 3])
>>> s
{1, 2, 3}
```

通过`add(key)`方法可以添加元素到set中，可以重复添加，但不会有效果：

```python
>>> s.add(4)
>>> s
{1, 2, 3, 4}
>>> s.add(4)
>>> s
{1, 2, 3, 4}
```

通过`remove(key)`方法可以删除元素：

```python
>>> s.remove(4)
>>> s
{1, 2, 3}
```

set可以看成数学意义上的无序和无重复元素的集合，因此，两个set可以做数学意义上的交集、并集等操作：

```python
>>> s1 = set([1, 2, 3])
>>> s2 = set([2, 3, 4])
>>> s1 & s2
{2, 3}
>>> s1 | s2
{1, 2, 3, 4}
```

**set和dict的唯一区别仅在于没有存储对应的value，但是，set的原理和dict一样，所以，同样==不可以放入可变对象==，因为无法判断两个可变对象是否相等，也就无法保证set内部“不会有重复元素”。**





# python语法之——函数

#### 先记录一些数学函数

| 函数名 | 作用                             |
| ------ | -------------------------------- |
| abs()  | 取绝对值                         |
| hex()  | 十进制转十六进制                 |
| max()  | 返回参数中最大的值，参数个数不限 |

python还提供了类型转换的函数*(类似于C中的强制类型转换符）*，比如转换成Boolean类型：

```python
>>> bool('')
False
```

> 鉴于已经写了这么多的python函数了，此处不再具体介绍怎么定义python函数

#### 空函数：

如果想定义一个什么事也不做的空函数，可以用`pass`语句：

```python
def nop():
    pass
```

`pass`语句什么都不做，那有什么用？实际上`pass`可以用来作为占位符，比如现在还没想好怎么写函数的代码，就可以先放一个`pass`，让代码能运行起来。

### 返回多个值

**和其他语言不一样，python函数可以返回多个值**==（这只是假象）==

比如在游戏中经常需要从一个点移动到另一个点，给出坐标、位移和角度，就可以计算出新的坐标：

```python
import math

def move(x, y, step, angle=0):
    nx = x + step * math.cos(angle)
    ny = y - step * math.sin(angle)
    return nx, ny
```

*<u>看起来是返回了两个值，**但实际上是返回了一个tuple值！**（还是一个值）在语法上，**返回一个tuple可以省略括号，而多个变量可以同时接收一个tuple，按位置赋给对应的值**，所以，看起来就像是返回了多个值</u>*

### 默认参数的函数

> python的默认参数的写法并没有什么特殊的，但值得一提的是它的参数顺序

有多个默认参数时，调用的时候，既可以按顺序提供默认参数，**也可以不按顺序提供部分默认参数。<u>当不按顺序提供部分默认参数时，需要把参数名写上。</u>**如：`enroll('Adam', 'M', city='Tianjin')`，意思是，`city`参数用传进去的值，其他默认参数继续使用默认值。

- ### A Big Trap

  先定义一个函数，传入一个list，添加一个`END`再返回：

  ```python
  def add_end(L=[]):
      L.append('END')
      return L
  ```

  当你正常调用时，结果似乎不错：

  ```python
  >>> add_end([1, 2, 3])
  [1, 2, 3, 'END']
  >>> add_end(['x', 'y', 'z'])
  ['x', 'y', 'z', 'END']
  ```

  当你使用默认参数调用时，一开始结果也是对的：

  ```python
  >>> add_end()
  ['END']
  ```

  但是，再次调用`add_end()`时，结果就不对了：

  ```python
  >>> add_end()
  ['END', 'END']
  >>> add_end()
  ['END', 'END', 'END']
  ```

  > 怎么回事？

  **A**: Python函数在定义的时候，默认参数`L`的值就被计算出来了，即`[]`，因为默认参数`L`也是一个变量，它指向对象`[]`，每次调用该函数，如果改变了`L`的内容，则下次调用时，默认参数的内容就变了，不再是函数定义时的`[]`了。

  ==定义默认参数要牢记一点：默认参数必须指向不变对象！==

### 可变参数

关于传入不定个数的参数的函数，我们首先想到的是**利用list或tuple传进来**，然后函数体里用for循环：`for n in numbers:`

但是调用的时候，需要先组装出一个list或tuple：`([])`,`(())`
为了更方便的直接`(,,,)`，**我们在参数前面加了一个`*`号，**`(*numbers)`

```python
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum
```

如果已经有一个list或者tuple，要调用一个可变参数怎么办？
**A**：Python允许你在list或tuple前面加一个`*`号，把list或tuple的元素变成可变参数传进去:

```python
>>> nums = [1, 2, 3]
>>> calc(*nums)
14
```

**就像是可变参数语法的反向应用。**

### 关键字参数

可变参数允许你传入0个或任意个参数，这些可变参数在函数调用时自动组装为一个tuple。而关键字参数允许你传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict。

```python
def person(name, age, **kw):
    print('name:', name, 'age:', age, 'other:', kw)
```

> 关键字参数有什么用？

***它可以扩展函数的功能。***比如，在`person`函数里，我们保证能接收到`name`和`age`这两个参数，但是，如果调用者愿意提供更多的参数，我们也能收到。试想你正在做一个用户注册的功能，除了用户名和年龄是必填项外，其他都是可选项，利用关键字参数来定义这个函数就能满足注册的需求。

和可变参数类似，也可以先组装出一个dict，然后，把该dict转换为关键字参数传进去：*(此处只展示简化写法)*

```python
>>> extra = {'city': 'Beijing', 'job': 'Engineer'}
>>> person('Jack', 24, **extra)
name: Jack age: 24 other: {'city': 'Beijing', 'job': 'Engineer'}
```

`**extra`表示把`extra`这个dict的所有key-value用关键字参数传入到函数的`**kw`参数，`kw`将获得一个dict，注意`kw`获得的dict是`extra`的一份拷贝，对`kw`的改动不会影响到函数外的`extra`。

如果要**限制关键字参数的名字**，就可以用命名关键字参数，例如，只接收`city`和`job`作为关键字参数。这种方式定义的函数如下：

```python
def person(name, age, *, city, job):
    print(name, age, city, job)
```

和关键字参数`**kw`不同，命名关键字参数需要一个特殊分隔符`*`，`*`后面的参数被视为命名关键字参数。

如果函数定义中已经有了一个可变参数，后面跟着的命名关键字参数就不再需要一个特殊分隔符`*`了：

```python
def person(name, age, *args, city, job):
    print(name, age, args, city, job)
```

但是需要注意的是：**命名关键字参数必须传入参数名**，这和位置参数（普通的参数）不同。如果没有传入参数名，调用将报错：

```python
>>> person('Jack', 24, 'Beijing', 'Engineer')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: person() takes 2 positional arguments but 4 were given
```

***由于调用时缺少参数名`city`和`job`，Python解释器把这4个参数均视为位置参数，但`person()`函数仅接受2个位置参数。***

### 参数组合

> 既然python有如此多的参数类型，（必选参数、默认参数、可变参数、关键字参数和命名关键字参数）
> 那么参数的相互组合绝对是一个麻烦事

在Python中定义函数，*这5种参数都可以组合使用*。但是请注意，参数定义的顺序必须是：<u>必选参数、默认参数、可变参数、命名关键字参数和关键字参数。</u>

比如定义一个函数，包含上述若干种参数：

```python
def f1(a, b, c=0, *args, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'kw =', kw)

def f2(a, b, c=0, *, d, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'd =', d, 'kw =', kw)
```

**在函数调用的时候，Python解释器自动按照参数位置和参数名把对应的参数传进去。**

最神奇的是通过一个tuple和dict，你也可以调用上述函数：

```python
>>> args = (1, 2, 3, 4)
>>> kw = {'d': 99, 'x': '#'}
>>> f1(*args, **kw)
a = 1 b = 2 c = 3 args = (4,) kw = {'d': 99, 'x': '#'}
>>> args = (1, 2, 3)
>>> kw = {'d': 88, 'x': '#'}
>>> f2(*args, **kw)
a = 1 b = 2 c = 3 d = 88 kw = {'x': '#'}
```

于是乎一种神奇的现象出现了：<u>对于任意函数，都可以通过类似`func(*args, **kw)`的形式调用它，无论它的参数是如何定义的。</u>

*warning:* ==虽然可以组合多达5种参数，但不要同时使用太多的组合，否则函数接口的可理解性很差。==