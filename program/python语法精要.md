[TOC]

# 语法精要

主要内容：列表推导式、函数、zip、lambda、生成器、异常处理
代码编辑及调试工具：jupyter notebook(基于python3.6)
参考书籍：《利用python进行数据分析》

## 列表推导式

列表推导式通过一条简洁的表达式对一组元素进行过滤并生成一个新的列表

```python
strings = ['a','as','bat','car','dove','python']
```

```python
#过滤长度小于等于2的字符串，并将剩下的字符串转换成大写字母
[x.upper() for x in strings if len(x)>2]
#其语法含义如下
'''
result=[]
for x in strings:
    if len(x)>2:
        result.append(x.upper())
'''
```

类似有集合推导式和字典推导式

```python
#集合推导式
{len(x) for x in strings}
```

    {1, 2, 3, 4, 6}

```python
#字典推导式
{val:index for index,val in enumerate(strings)}#enumerate函数可逐个返回序列的(i,value)元组，其中i为索引
```

    {'a': 0, 'as': 1, 'bat': 2, 'car': 3, 'dove': 4, 'python': 5}

嵌套列表表达式

```python
some_tuples = [(1,2,3,5),(4,5,6),(7,8,9)]
```

```python
flattened = [x for tup in some_tuples for x in tup]
#其语法含义如下
'''
flattened = []
for tup in some_tuples:
    for x in tup:
        flattened.append(x)
'''
```

```python
flattened
```

    [1, 2, 3, 4, 5, 6, 7, 8, 9]

```python
flattened = [x for tup in some_tuples if len(tup)==3 for x in tup if x>4]
```

    [5, 6, 7, 8, 9]

## 函数

定义函数如下，x和y为位置参数，z为关键词参数（即有默认值的参数或可选参数），**关键词参数必须在位置参数后面**

```python
def my_function(x,y,z=1.5):
    if z>1:
        return z*(x+y)
    else:
        return z/(x+y)
```

```python
my_function(2,4,z=0.7)#关键词无需记住顺序，记得名字就行
```

    0.11666666666666665

```python
my_function(2,4)#关键词参数可缺省，位置参数不能缺省
```

    9.0

```python
my_function(2,4,3.5)#不指定关键词名称，按顺序给其赋值
```

    21.0

函数参数的工作方式是：位置参数打包成元组，关键词参数打包成字典，函数实际收到的是一个元组***args**和一个字典***kwargs**\
对于上述函数，其内部完成如下转换:\
x,y=args\
z=kwargs.get('z',z\_default\_value)\
我们再来看如下函数：

```python
def say_hello_then_call_f(f,*args,**kwargs):
    print('args is',args)
    print('kwargs is',kwargs)
    print("Hello! Now I'm going to call %s" % f)
    return f(*args,**kwargs)#此处的参数传递类似于元组拆包，如传入下面的g，则完成转换：x,y=args，z=kwargs.get('z',z_default_value)

def g(x,y,z=1):
    return (x+y)/z
```

```python
say_hello_then_call_f(g,1,2,z=5)
```

    args is (1, 2)
    kwargs is {'z': 5}
    Hello! Now I'm going to call <function g at 0x0000023D648FA730>
    0.6

函数可**返回多个值**，其实是返回元组，调用的时候通过**元组拆包**

```python
def f():
    a=5
    b=6
    c=7
    return a,b,c
```

```python
f()#返回的是一个元组
```

    (5, 6, 7)

```python
a,b,c=f()#元组拆包
```

```python
a
```

    5

python中**万物皆对象**，函数亦为对象

```python
states=['hello','hi ','#Hello!?#']
```

```python
import re #正则表达式模块
```

假设我们要对上述字符列表进行清洗，我们可能写出如下函数：

```python
def clean_strings(strings):
    result = []
    #做一系列操作
    for value in strings:
        value = value.strip()#移除首尾空格
        value = re.sub('[!?#]','',value)#移除标点符号
        value = value.title()#首字母大写
        result.append(value)
    return result
```

```python
clean_strings(states)
```

    ['Hello', 'Hi', 'Hello']

为了考虑**函数的可扩展性**（比如我们后续可能增加一些额外的清洗操作），我们可设计如下函数：

```python
#定义一个清洗操作
def remove_putuation(value):
    '''
    移除字符串的标点符号
    '''
    return re.sub('[!?#]','',value)

#清洗函数，可传入一系列操作，对比之前的函数，现在增加清洗操作无需修改此函数
def clean_strings_pro(strings,ops):
    '''
    strings:处理对象为字符列表
    ops:记录操作列表
    '''
    result=[]
    for value in strings:
        for function in ops:
            value = function(value)
        result.append(value)
    return result
```

```python
clean_ops = [str.strip,remove_putuation,str.title]#设置一系列操作
clean_strings_pro(states,clean_ops)
```

    ['Hello', 'Hi', 'Hello']

```python
#将上述函数作为内置函数的参数，如map(用于在一组数据上应用一个函数)
list(map(remove_putuation,states))
```

    ['hello', 'hi ', 'Hello']

## zip

zip，顾名思义是压缩的意思；在python中体现在：zip函数可用于将多个序列（元组、列表等）中的元素进行配对，从而产生一个新的列表（python3是产生一个可迭代的对象）

```python
seq1=['foo','bar','baz']
seq2=[1,2,3]
zip(seq1,seq2)
```

    <zip at 0x23d6476dd88>

```python
list(zip(seq1,seq2))
```

    [('foo', 1), ('bar', 2), ('baz', 3)]

zip一个常见的用法是用于迭代多个序列

```python
for (a,b) in zip(seq1,seq2):
    print(a,b)
```

    foo 1
    bar 2
    baz 3

zip还可用于解压：

```python
pitchers = list(zip(seq1,seq2))
```

```python
pitchers
```

    [('foo', 1), ('bar', 2), ('baz', 3)]

```python
seq1,seq2 = zip(*pitchers)
```

```python
seq1
```

    ('foo', 'bar', 'baz')

```python
seq2
```

    (1, 2, 3)

注意上述\*\*\*\*\*的用法，通过1.2节函数参数的工作方式我们知道上述操作其本质还是解压，zip是接受多个序列的，\*pitchers会进行元组拆包：zip(\*pitchers)==zip(pitchers\[0],pitchers\[1])

## lambda

lambda表示声明一种匿名函数

```python
#写一个简单的函数
def short_function(x):
    return x*2
#利用lambda，其等价形式如下
equiv_anon = lambda x:x*2
```

```python
equiv_anon(2)
```

    4

函数有时候会作为参数传递，这时候利用lambda编写简单函数会很方便

```python
def apply_to_list(some_list,f):
    return [f(x) for x in some_list]
ints = [4,0,1,5,6]
apply_to_list(ints,lambda x:x*2)
```

    [8, 0, 2, 10, 12]

上述操作等价于编写\[x\*2 for x in ints],但通过lambda我们可以传入自定义的运算给apply\_to\_list

```python
strings=['foo','card','bar','aaaa']
```

```python
#列表的sort方法中key参数接收函数作为参数值
strings.sort(key=lambda x:len(set(list(x))))#根据字符的不重复个数从小到大排序
```

```python
strings
```

    ['aaaa', 'foo', 'bar', 'card']

## 生成器

生成器是构造迭代对象的一种简单方式，首先让我们了解一下迭代器（一种生成可迭代对象的通用方式）

```python
some_dict = {'a':1,'b':2,'c':3}
```

```python
#执行下面语句时，Python解释器会尝试从some_dict创建一个迭代器
for key in some_dict:
    print(key)
```

    a
    b
    c

```python
#显示地创建可迭代对象
dict_iterator = iter(some_dict)
```

```python
type(dict_iterator)
```

    dict_keyiterator

大部分接受列表之类的对象的函数也都可以接受任何可迭代对象，比如min、max、sum、list、tuple

```python
list(dict_iterator)
```

    ['a', 'b', 'c']

创建生成器

```python
def squrares(n=10):
    print('Generating squares from 1 to %d' % (n**2))
    for i in range(1,n+1):
        yield i**2
```

```python
gen = squrares()#调用生成器，没有代码被执行
```

```python
gen
```

    <generator object squrares at 0x000002589EFFB990>

```python
#生成器以一种延迟的方式(即可迭代对象)返回一个值序列
for x in gen:
    print(x)
```

    Generating squares from 1 to 100
    1
    4
    9
    16
    25
    36
    49
    64
    81
    100

```python
list(gen)#作为迭代对象传给函数
```

    Generating squares from 1 to 100
    [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

生成器表达式，类似于列表推导式的构造

```python
gen = (x**2 for x in range(10))#简洁地创建一个生成器
```

```python
gen
```

    <generator object <genexpr> at 0x000002589EFFBD58>

```python
type(gen)
```

    generator

```python
list(gen)
```

    [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

```python
#生成器表达式可作为参数传递（可省略外层括号）
sum((x**2 for x in range(10)))
```

    285

```python
sum(x**2 for x in range(10))
```

    285

```python
#利用生成器产生元组列表
gen=((i,i**2) for i in range(5))
```

```python
list(gen)
```

    [(0, 0), (1, 1), (2, 4), (3, 9), (4, 16)]

```python
dict((i,i**2) for i in range(5))
```

    {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

## 异常处理

优雅地处理异常是构建健壮程序的重要环节

```python
float('1.2345')
```

    1.2345

```python
float('something')
```

    ---------------------------------------------------------------------------

    ValueError                                Traceback (most recent call last)

    <ipython-input-79-439904410854> in <module>()
    ----> 1 float('something')


    ValueError: could not convert string to float: 'something'

```python
float((1,2))
```

    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-84-a7437c28cf3f> in <module>()
    ----> 1 float((1,2))


    TypeError: float() argument must be a string or a number, not 'tuple'

```python
#处理所有异常
def attempt_float(x):
    try:
        return float(x)
    except:
        return x
```

```python
attempt_float('1.2345')
```

    1.2345

```python
attempt_float('something')
```

    'something'

```python
attempt_float((1,2))
```

    (1, 2)

```python
#只处理ValueError
def attempt_float(x):
    try:
        return float(x)
    except ValueError:
        return x
```

```python
attempt_float((1,2))
```

    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-86-a1f7e5239136> in <module>()
    ----> 1 attempt_float((1,2))


    <ipython-input-85-e51706b69e39> in attempt_float(x)
          2 def attempt_float(x):
          3     try:
    ----> 4         return float(x)
          5     except ValueError:
          6         return x


    TypeError: float() argument must be a string or a number, not 'tuple'

```python
#同时处理多个异常
def attempt_float(x):
    try:
        return float(x)
    except(ValueError,TypeError):
        return x
```

```python
attempt_float((1,2))
```

    (1, 2)

```python
#处理多个异常分支,最多处理一个分支，通用except必须放在最后
def attempt_float(x):
    try:
        return float(x)
    except ValueError:
        print('ValueError')
    except:
        print('Error')
```

```python
attempt_float('d')
```

    ValueError

```python
attempt_float((1,2))
```

    Error

```python
#不想处理异常,此时必须有finally(finally和except必须存在一个)
def attempt_float(x):
    try:
        return float(x)
    finally:
        print('end')
```

```python
#设置处理成功时显示一些代码,else必须在except出现后
#使用 else 子句比把所有的语句都放在 try 子句里面要好，这样可以避免一些意想不到的、而except又没有捕获的异常。
def attempt_float(x):
    try:
        x= float(x)
    except:
        print('fail')
    else:
        x+=2
        print('success')
    return x
```

```python
attempt_float('3')
```

    success
    5.0

