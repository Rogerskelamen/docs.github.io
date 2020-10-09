# python语法之——高级特性

### 切片

> 我们经常想要一次性从list或tuple中取多个值，而切片方便了我们的需求。

取前N个元素，也就是索引为0-(N-1)的元素，可以用循环：

```python
>>> r = []
>>> n = 3
>>> for i in range(n):
...     r.append(L[i])
... 
>>> r
['Michael', 'Sarah', 'Tracy']
```

Python提供了切片（Slice）操作符，能大大简化这种操作:

```python
>>> L[0:3]
['Michael', 'Sarah', 'Tracy']
```

`L[0:3]`表示，从索引`0`开始取，直到索引`3`为止，但不包括索引`3`。即索引`0`，`1`，`2`，正好是3个元素。
如果第一个索引是`0`，还可以省略：

```python
>>> L[:3]
['Michael', 'Sarah', 'Tracy']
```

类似的，既然Python支持`L[-1]`取倒数第一个元素，那么它同样支持倒数切片，试试：

```python
>>> L[-2:]
['Bob', 'Jack']
>>> L[-2:-1]
['Bob']
```

*<u>ps:记住倒数第一个元素的索引是`-1`。</u>*

**其实可以用splice实现更骚的操作**
前10个数，每两个取一个：

```python
>>> L[:10:2]
[0, 2, 4, 6, 8]
```

所有数，每5个取一个：

```python
>>> L[::5]
[0, 5, 10, 15, 20, 25, 30, 35, 40, 45, 50, 55, 60, 65, 70, 75, 80, 85, 90, 95]
```

甚至什么都不写，只写`[:]`就可以原样复制一个list：

```python
>>> L[:]
[0, 1, 2, 3, ..., 99]
```

> 扩展一波

- tuple也是一种list，唯一区别是tuple不可变。因此，tuple也可以用切片操作，只是操作的结果仍是tuple：	

```python
>>> (0, 1, 2, 3, 4, 5)[:3]
(0, 1, 2)
```

- 字符串`'xxx'`也可以看成是一种list，每个元素就是一个字符。因此，字符串也可以用切片操作，只是操作结果仍是字符串：

```python
>>> 'ABCDEFG'[:3]
'ABC'
>>> 'ABCDEFG'[::2]
'ACEG'
```

### 迭代

> 我们很容易看出，python的`for`循环和其他语言的不太一样，
> 这决定了python的`for`循环的循环抽象程度要高于C等其他语言的`for`循环。其中有一点就是：Python的`for`循环不仅可以用在list或tuple上，还可以作用在其他可迭代对象上。

***只要是可迭代对象，无论有无下标，都可以迭代，比如dict就可以迭代：***

```python
>>> d = {'a': 1, 'b': 2, 'c': 3}
>>> for key in d:
...     print(key)
...
a
c
b
```

*(显然这里的顺序有点和我们想的不一样，因为这是字典啊！)*
*<u>默认情况下，dict迭代的是key。如果要迭代value，可以用`for value in d.values()`，如果要同时迭代key和value，可以用`for k, v in d.items()`。</u>*

**不用说，字符串也是可以用for来迭代的**（因为字符串也是可迭代对象啊）

> 那么问题来了：如何判断一个对象是可迭代对象呢？
> 方法是通过collections模块的Iterable类型判断：

```python
>>> from collections import Iterable
>>> isinstance('abc', Iterable) # str是否可迭代
True
>>> isinstance([1,2,3], Iterable) # list是否可迭代
True
>>> isinstance(123, Iterable) # 整数是否可迭代
False
```

> 最后一个小问题，如果要对list实现类似Java那样的下标循环怎么办？Python内置的`enumerate`函数可以把一个list变成索引-元素对，这样就可以在`for`循环中同时迭代索引和元素本身：

```python
>>> for i, value in enumerate(['A', 'B', 'C']):
...     print(i, value)
...
0 A
1 B
2 C
```

### 列表生成式

列表生成式即List Comprehensions，是Python内置的非常简单却强大的可以用来创建list的生成式。

举个例子，要生成list `[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]`可以用`list(range(1, 11))`：

```python
>>> list(range(1, 11))
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

但如果要生成`[1x1, 2x2, 3x3, ..., 10x10]`怎么做？

```python
>>> [x * x for x in range(1, 11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
# 所以刚才的那个也可以写成：
>>> [x for x in range(1, 11)]
```

for循环后面还可以加上if判断，这样我们就可以筛选出仅偶数的平方：

````python
>>> [x * x for x in range(1, 11) if x % 2 == 0]
[4, 16, 36, 64, 100]
````

还可以使用两层循环，可以生成全排列：

```python
>>> [m + n for m in 'ABC' for n in 'XYZ']
['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']
```

`for`循环其实可以同时使用两个甚至多个变量，比如`dict`的`items()`可以同时迭代key和value：

```python
>>> d = {'x': 'A', 'y': 'B', 'z': 'C' }
>>> for k, v in d.items():
...     print(k, '=', v)
...
y = B
x = A
z = C
```

因此，列表生成式也可以使用两个变量来生成list：

```python
>>> d = {'x': 'A', 'y': 'B', 'z': 'C' }
>>> [k + '=' + v for k, v in d.items()]
['y=B', 'x=A', 'z=C']
```

**<u>在一个列表生成式中，`for`前面的`if ... else`是表达式，而`for`后面的`if`是过滤条件，不能带`else`。</u>**

### 生成器

> 如果列表元素可以按照某种算法推算出来，那我们是否可以在循环的过程中不断推算出后续的元素呢？这样就不必创建完整的list，从而节省大量的空间。在Python中，这种一边循环一边计算的机制，称为生成器：generator

我们看一个斐波拉契数列的例子：

```python
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1
    return 'done'
```

这就是定义generator的另一种方法。如果一个函数定义中包含`yield`关键字，那么这个函数就不再是一个普通函数，而是一个generator：

```python
>>> f = fib(6)
>>> f
<generator object fib at 0x104feaaa0>
```

想要展示这个列表，使用`for`循环，因为generator也是可迭代对象：

```python
>>> g = (x * x for x in range(10))
>>> for n in g:
...     print(n)
... 
0
1
4
9
16
25
```

这里，最难理解的就是generator和函数的执行流程不一样。函数是顺序执行，遇到`return`语句或者最后一行函数语句就返回。而变成generator的函数，在每次调用`next()`的时候执行，遇到`yield`语句返回，再次执行时从上次返回的`yield`语句处继续执行。调用该generator时，首先要生成一个generator对象，然后用`next()`函数不断获得下一个返回值：

```python
>>> o = odd()
>>> next(o)
step 1
1
>>> next(o)
step 2
3
>>> next(o)
step 3
5
>>> next(o)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

### 迭代器

现在我们已经知道了，可以直接作用于`for`循环的数据类型有以下几种：

一类是集合数据类型，如`list`、`tuple`、`dict`、`set`、`str`等；

一类是`generator`，包括生成器和带`yield`的generator function。

这些可以直接作用于`for`循环的对象统称为可迭代对象：`Iterable`。
可以使用`isinstance()`判断一个对象是否是`Iterable`对象。

> 而生成器不但可以作用于`for`循环，还可以被`next()`函数不断调用并返回下一个值，直到最后抛出`StopIteration`错误表示无法继续返回下一个值了。
>
> 可以被`next()`函数调用并不断返回下一个值的对象称为迭代器：`Iterator`。

**<u>生成器都是`Iterator`对象，但`list`、`dict`、`str`虽然是`Iterable`，却不是`Iterator`。</u>**

把`list`、`dict`、`str`等`Iterable`变成`Iterator`可以使用`iter()`函数：

```python
>>> isinstance(iter([]), Iterator)
True
>>> isinstance(iter('abc'), Iterator)
True
```

> 那么，为什么`list`、`dict`、`str`等数据类型不是`Iterator`？

这是因为Python的`Iterator`对象表示的是一个数据流，Iterator对象可以被`next()`函数调用并不断返回下一个数据，直到没有数据时抛出`StopIteration`错误。可以把这个数据流看做是一个有序序列，但我们却不能提前知道序列的长度，只能不断通过`next()`函数实现按需计算下一个数据，所以`Iterator`的计算是惰性的，只有在需要返回下一个数据时它才会计算。*（看看就好）*





# python语法之——函数式编程

**函数式编程的一个特点就是，==允许把函数本身作为参数传入另一个函数，还允许返回一个函数！==**

## 高阶函数

> 变量可以指向函数

如果一个变量指向了一个函数，那么，可否通过该变量来调用这个函数？用代码验证一下：

```python
>>> f = abs
>>> f(-10)
10
```

成功！说明变量`f`现在已经指向了`abs`函数本身。直接调用`abs()`函数和调用变量`f()`完全相同。

> 函数名也是变量

如果把`abs`指向其他对象，会有什么情况发生？

```python
>>> abs = 10
>>> abs(-10)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'int' object is not callable
```

把`abs`指向`10`后，就无法通过`abs(-10)`调用该函数了！因为`abs`这个变量已经不指向求绝对值函数而是指向一个整数`10`！
***ps：由于`abs`函数实际上是定义在`import builtins`模块中的，所以要让修改`abs`变量的指向在其它模块也生效，要用`import builtins; builtins.abs = 10`。***

> 传入函数

**既然变量可以指向函数，函数的参数能接收变量，那么一个函数就可以接收另一个函数作为参数，<u>这种函数就称之为高阶函数。</u>**

比如，一个最简单的高阶函数：

```python
def add(x, y, f):
    return f(x) + f(y)
```

==conclusion==:编写高阶函数，就是让函数的参数能够接收别的函数。

### map/reduce

- #### map

  我们先看map。`map()`函数接收两个参数，一个是函数，一个是`Iterable`，`map`将传入的函数依次作用到序列的每个元素，并把结果作为新的`Iterator`返回。

  现在，我们用Python代码说明：

  ```python
  >>> def f(x):
  ...     return x * x
  ...
  >>> r = map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])
  >>> list(r)
  [1, 4, 9, 16, 25, 36, 49, 64, 81]
  ```

  `map()`传入的第一个参数是`f`，即函数对象本身。由于结果`r`是一个`Iterator`，`Iterator`是惰性序列，因此通过`list()`函数让它把整个序列都计算出来并返回一个list。

- #### reduce

  再看`reduce`的用法。`reduce`把一个函数作用在一个序列`[x1, x2, x3, ...]`上，这个函数必须接收两个参数，`reduce`把结果继续和序列的下一个元素做累积计算，其效果就是：
  `reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)`

  比方说对一个序列求和，就可以用`reduce`实现：

  ```python
  >>> from functools import reduce
  >>> def add(x, y):
  ...     return x + y
  ...
  >>> reduce(add, [1, 3, 5, 7, 9])
  25
  ```

  这个例子本身没多大用处，但是，如果考虑到字符串`str`也是一个序列，对上面的例子稍加改动，配合`map()`，**我们就可以写出把`str`转换为`int`的函数**：

  ```python
  >>> from functools import reduce
  >>> def fn(x, y):
  ...     return x * 10 + y
  ...
  >>> def char2num(s):
  ...     digits = {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}
  ...     return digits[s]
  ...
  >>> reduce(fn, map(char2num, '13579'))
  13579
  ```

**简单来说，<u>map是将可迭代对象一个个元素用一个函数处理之后返回，而reduce是将可迭代对象的一个个元素两两处理之后迭代返回。</u>**

### filter

> 顾名思义，这玩意是用来过滤序列的

和`map()`类似，`filter()`也接收一个函数和一个序列。和`map()`不同的是，`filter()`把传入的函数依次作用于每个元素，然后根据返回值是`True`还是`False`决定保留还是丢弃该元素。

例如，在一个list中，删掉偶数，只保留奇数，可以这么写：

```python
def is_odd(n):
    return n % 2 == 1

list(filter(is_odd, [1, 2, 4, 5, 6, 9, 10, 15]))
# 结果: [1, 5, 9, 15]
```

注意到**`filter()`函数返回的是一个`Iterator`**，也就是一个惰性序列，所以要强迫`filter()`完成计算结果，需要<u>用`list()`函数获得所有结果并返回list。</u>

### sorted

> 顾名思义，这玩意是用来排序的

Python内置的`sorted()`函数就可以对list进行排序：

```python
>>> sorted([36, 5, -12, 9, -21])
[-21, -12, 5, 9, 36]
```

此外，`sorted()`函数也是一个高阶函数，它还可以接收一个`key`函数来实现自定义的排序，例如按绝对值大小排序：

```python
>>> sorted([36, 5, -12, 9, -21], key=abs)
[5, 9, -12, -21, 36]
```

key指定的函数将作用于list的每一个元素上，并根据key函数返回的结果进行排序。对比原始的list和经过`key=abs`处理过的list：

```python
list = [36, 5, -12, 9, -21]

keys = [36, 5,  12, 9,  21]
```

然后`sorted()`函数按照keys进行排序，并按照对应关系返回list相应的元素：

```ascii
keys排序结果 => [5, 9,  12,  21, 36]
                |  |    |    |   |
最终结果     => [5, 9, -12, -21, 36]
```

所以它实际上还是用内置的排序规则进行排序，只不过==我们用函数将原列表进行了数据处理==，它用的新列表排序罢了。

## 返回函数

**高阶函数除了可以接受函数作为参数外，还可以把函数作为结果值返回。**

我们来实现一个可变参数的求和。通常情况下，求和的函数是这样定义的：

```python
def calc_sum(*args):
    ax = 0
    for n in args:
        ax = ax + n
    return ax
```

但是，如果不需要立刻求和，而是在后面的代码中，根据需要再计算怎么办？可以不返回求和的结果，而是返回求和的函数：

```python
def lazy_sum(*args):
    def sum():
        ax = 0
        for n in args:
            ax = ax + n
        return ax
    return sum
```

当我们调用`lazy_sum()`时，返回的并不是求和结果，而是求和函数：

```python
>>> f = lazy_sum(1, 3, 5, 7, 9)
>>> f
<function lazy_sum.<locals>.sum at 0x101c6ed90>
```

调用函数`f`时，才真正计算求和的结果：

```python
>>> f()
25
```

相当于这时才进行了计算的运算。

***注意：我们这个lazy_sum其实是运用到了一种叫做闭包的方法（<u>内部函数`sum`可以引用外部函数`lazy_sum`的参数和局部变量</u>）***

### 闭包

> 没什么好说的，就如JS一样，如果返回函数有定义循环变量，那它**绝对不是“当时那个状态”的变量**。
>
> 所以。。

*warning:*==返回函数不要引用任何循环变量，或者后续会发生变化的变量。==

> 就如JS中用立即执行函数解决那样，我要是偏要用循环变量怎么办？

方法是再创建一个函数，用该函数的参数绑定循环变量当前的值，无论该循环变量后续如何更改，已绑定到函数参数的值不变：

```python
def count():
    def f(j):
        def g():
            return j*j
        return g
    fs = []
    for i in range(1, 4):
        fs.append(f(i)) # f(i)立刻被执行，因此i的当前值被传入f()
    return fs
```

## 匿名函数

> 终于要讲lambda了

当我们在传入函数时，有些时候，不需要显式地定义函数，直接传入匿名函数更方便。
还是以`map()`函数为例，计算f(x)=x2时，除了定义一个`f(x)`的函数外，还可以直接传入匿名函数：

```python
>>> list(map(lambda x: x * x, [1, 2, 3, 4, 5, 6, 7, 8, 9]))
[1, 4, 9, 16, 25, 36, 49, 64, 81]
```

关键字`lambda`表示匿名函数，冒号前面的`x`表示函数参数。
*匿名函数有个限制，就是只能有一个表达式，不用写`return`，返回值就是该表达式的结果。*

用匿名函数有个好处，因为函数没有名字，不必担心函数名冲突。此外，匿名函数也是一个函数对象，也可以把匿名函数赋值给一个变量，再利用变量来调用该函数：

```python
>>> f = lambda x: x * x
>>> f
<function <lambda> at 0x101c6ef28>
>>> f(5)
25
```

同样，也可以把匿名函数作为返回值返回，比如：

```python
def build(x, y):
    return lambda: x * x + y * y
```

## 装饰器

> ps:函数对象有一个`__name__`属性，可以拿到函数的名字(只是忘说了)

***引入：***我们有一个`now()`函数：

```python
>>> def now():
...     print('2015-3-25')
```

现在，假设我们要增强`now()`函数的功能，比如，在函数调用前后自动打印日志，,<u>但又不希望修改`now()`函数的定义</u>，**这种在代码运行期间动态增加功能的方式，称之为“装饰器”（Decorator）。**

本质上，decorator就是一个返回函数的高阶函数。所以，我们要定义一个能打印日志的decorator，可以定义如下：

```python
def log(func):
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)
    return wrapper
```

这上面的`log()`就是一个装饰器了，我们要借助Python的@语法，把decorator置于函数的定义处：（@是一个语法糖）

```python
@log
def now():
    print('2015-3-25')
```

于是，调用`now()`函数，不仅会运行`now()`函数本身，还会在运行`now()`函数前打印一行日志：

```python
>>> now()
call now():
2015-3-25
```

把`@log`放到`now()`函数的定义处，相当于执行了语句：

```python
now = log(now)
```

如果decorator本身需要传入参数，那就需要编写一个返回decorator的高阶函数，写出来会更复杂。比如，要自定义log的文本：

```python
def log(text):
    def decorator(func):
        def wrapper(*args, **kw):
            print('%s %s():' % (text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator
```

这个3层嵌套的decorator用法如下：

```python
@log('execute')
def now():
    print('2015-3-25')
    
>>> now()
execute now():
2015-3-25
```

和两层嵌套的decorator相比，3层嵌套的效果是这样的：

```python
>>> now = log('execute')(now)
```

## 偏函数

> Python的`functools`模块提供了很多有用的功能，其中一个就是偏函数（Partial function）。要注意，这里的偏函数和数学意义上的偏函数不一样。

和默认参数的函数一样，偏函数也可以降低函数调用的难度。
for example:
`int()`函数可以把字符串转换为整数，当仅传入字符串时，`int()`函数默认按十进制转换：

```python
>>> int('12345')
12345
```

但`int()`函数还提供额外的`base`参数，默认值为`10`。如果传入`base`参数，就可以做N进制的转换：

```python
>>> int('12345', base=8)
5349
>>> int('12345', 16)
74565
```

假设要转换大量的二进制字符串，每次都传入`int(x, base=2)`非常麻烦，于是，我们想到，可以定义一个`int2()`的函数，默认把`base=2`传进去：

```python
def int2(x, base=2):
    return int(x, base)
```

这样，我们转换二进制就非常方便了：

```python
>>> int2('1000000')
64
>>> int2('1010101')
85
```

`functools.partial`就是帮助我们创建一个偏函数的，不需要我们自己定义`int2()`，可以直接使用下面的代码创建一个新的函数`int2`：

```python
>>> import functools
>>> int2 = functools.partial(int, base=2)
>>> int2('1000000')
64
```

所以，简单总结`functools.partial`的作用就是，**<u>把一个函数的某些参数给固定住（也就是设置默认值）</u>**，返回一个新的函数，调用这个新函数会更简单。

*注意到上面的新的`int2`函数，仅仅是把`base`参数重新设定默认值为`2`，但也可以在函数调用时传入其他值：*

```python
>>> int2('1000000', base=10)
1000000
```

