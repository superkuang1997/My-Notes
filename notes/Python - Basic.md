# Python🐍



## 注释

```python
# 单行注释采用 # 开头。

'''
这是多行注释，使用单引号。
这是多行注释，使用单引号。
这是多行注释，使用单引号。
'''
```





## cmd命令

```sh
python [option] ... [-c cmd | -m mod | file | -] [arg] ...
```



```sh
# 将库中的python模块用作脚本去运行
python -m | -mod
```



# pip🐍 

## pip命令

```sh
# 安装依赖项
pip install -r requirement.txt 
# 安装最新版本的模块
pip install -U moddle  
```





```python
# --user 会将 Python 程序包安装到 `$HOME/.local` 路径下，其中包含三个字文件夹：bin，lib 和 share
# 这个路径默认不在$PYTH中
pip install --user package_name

# 安装路径在 '/root/.local/bin'
sudo pip install --user package_name
```

可以手动将以上目录加入环境变量：

```sh
export PATH=$HOME/.local/bin:$PATH
```





# 数据结构🐍 

## 列表

### 定义列表

```python
mylist = [3, 5, 7, 2, 20]
```





### 列表方法

append：直接添加

```python
> mylist.append([1,2,3])
> mylist
  [3, 5, 7, 2, 20, [1, 2, 3]]
```



extend：去除一个维度后再添加

```python
> mylist.extend([[[2, 3], 2], [6, 7]])
> mylist
	[3, 5, 7, 2, 20, [[2, 3], 2], [6, 7]]
```



pop：移出尾元素并返回

```python
> mylist.pop()
	20
```



remove：删除一个元素

```python
> mylist.remove(3)

> del mylist[3] 
```



clear：清空列表

```
> mylist.clear()
```



sort：排序列表

- key：可以接收一个 lambda 表达式

- reverse：升序 or 降序

```python
def func(x):
    if x > 10:
        return 0
    return 1

mylist = [3, 5, 7, 2, 20]
mylist.sort(key=lambda x: func(x), reverse=True)
mylist
```



reverse：反转

```python
> mylist.reverse()
> mylist
	[20, 2, 7, 5, 3]
```



index：索引

```python
> mylist.index(7)
  2
```



### 列表切片（magic）

#### 正序索引

```python
> mylist[::]
	[50, 70, 30, 20, 90, 10, 50]
  
> mylist[1:5:1]
	[70, 30, 20, 90]
  
> mylist[1:5:2]
	[70, 20]
 
```

<img src="http://store.secretcamp.cn/uPic/image-20211123153744663202111231537461637653066O8itEYO8itEY.png" alt="image-20211123153744663" style="zoom:40%;" />



#### 逆序索引

```python
> mylist[-6::1]
	[70, 30, 20, 90, 10, 50]
	
> mylist[-6:-1:1]
	[70, 30, 20, 90, 10]
```

<img src="http://store.secretcamp.cn/uPic/image-20211123154501880202111231545021637653502dK15uHdK15uH.png" alt="image-20211123154501880" style="zoom:40%;" />



#### 进阶操作

切片有三个参数，start、end、step

step > 0 时，start 默认为 0，end 默认为 len(mylist)

step < 0 时，start 默认为 len(mylist) - 1 , end 默认为 -1（含义上是 -1，实际上不能用 -1 表示）

```python
# 表示从index=-1开始，向右(正向)一直找到index=3，一个元素也找不到
> mylist[-1:3:1]
	[]

# 相当于 mylist[-1:3:-1]，表示从index=-1开始，向左(反向)找一直找到index=3
> mylist[:3:-1]
[50, 10, 90]
```





## 字典

### 定义字典



### 字典方法

#### get

根据键获取值，如果键不存在则返回default

```
dict.get (key, default=None)
```





#### setdefault

和 `get#` 类似，如果键不存在于字典中，将会添加键并将值设为默认值，如果键存在，则返回对应的值



#### pop

删除字典给定键 key 所对应的值，返回值为被删除的值。key 值必须给出。否则，返回 default 值。

```
dict.pop(key [, default])
```



### 字典排序

sorted



## 元组

不可变的列表被称为元组。



### 定义元组





# 控制流🐍

## 三元表达式

```

```



## for





## while





# 参数传递🐍

## args

args 是 arguments 的缩写，表示位置参数。*args 必须放在 **kwargs 的前面，因为args类型是一个tuple，而kwargs则是一个字典dict，位置参数只能在关键字参数的前面。



## kwargs

kwargs 是 keyword arguments 的缩写，表示关键字参数。**kwargs则是将一个可变的关键字参数的字典传给函数实参，同样参数列表长度可以为0或为其他值。



## *args与**kwargs

args 和 kwargs 不仅可以在函数定义中使用，还可以在函数调用中使用。在调用时使用就相当于pack（打包）和unpack（解包），类似于元组的打包和解包。

### 函数定义中使用

```python
def test_kwargs(first, *args, **kwargs):
   print('Required argument: ', first)
   print(type(kwargs))
   # 遍历元组
   for v in args:  
      print('Optional argument (args): ', v)
   # 遍历字典
   for k, v in kwargs.items():  
      print('Optional argument %s (kwargs): %s' % (k, v))

test_kwargs(1, 2, 3, 4, name='alice', age=16)
```

```
————> run

Required argument:  1  
<class 'dict'>
Optional argument (args):  2
Optional argument (args):  3
Optional argument (args):  4
Optional argument name (kwargs): alice
Optional argument age (kwargs): 16
```



### 函数调用中使用

```python
def test_args_kwargs(name, age, sex):
    print("name:", name)
    print("age:", age)
    print("sex:", sex)


args = ("Kiana", 15, False)
kwargs = {"name": 'Alice', "age": 16, "sex": False}
test_args_kwargs(*args)  # 将元组解包作为参数
print('-' * 25)
test_args_kwargs(**kwargs)  # 将字典解包作为参数
```

```
————> run

name: Kiana
age: 15
sex: False
-------------------------
name: Alice
age: 16
sex: False
```



# 内置函数🐍

## help

用于查看函数或模块用途的详细说明。



## split



## sorted

该方法在副本上执行，并返回排序后的副本

```python
sorted(iterable, /, *, key=None, reverse=False)
```



## setdefault

如果键不存在于字典中，将会添加键并将值设为默认值。

```python
# 两种写法等价
dict.set_defalut("name", "kiana")
dict.get("name", "kiana")
```

## update

字典A的update函数把字典B的键/值对更新到字典A里

```python
>> dic = {'a': 1, 'b': 2}
>> dic.update({'c': 3})
{'a': 1, 'b': 2, 'c': 3}
```

## getattr

返回一个对象属性值，不存在则返回default

```python
class Student:
    def __init__(self, name):
        self.name = name
>> kiana = Student('kiana')
>> getattr(kiana, 'name', 0)
kiana
>> getattr(kiana, 'rank', 0)
0
```





## input





## map

`map()` 会根据提供的函数对指定序列做映射。

```python
# 输入转int
map(int, input.split())

# lambda
map(lambda x: x ** 2, [1, 2, 3, 4, 5])  # 使用 lambda 匿名函数

# 自定义函数
def square(x) :           
	  return x ** 2

map(square, [1,2,3,4,5])   
```



## format



## enumerate

遍历



## zip



# 魔法函数🐍



# datetime🐍

datetime 模块中包含 date、time 和 datetime 三种主要的时间类

<img src="http://store.secretcamp.cn/uPic/image-20220122192557671202201221925571642850757vZuKATvZuKAT.png" alt="image-20220122192557671" style="zoom:50%;" />

## 时间运算

```python
timedelta(days=0, seconds=0, microseconds=0, milliseconds=0, minutes=0, hours=0, weeks=0)
```



```python
# datetime运算可以得到timedelta
delta = datetime(2011,1,7) - datetime(2008,6,24,8,15)
```



# 面向对象🐍 

## 内建方法

### `__init__`方法

用来构造初始化函数,用来给类的实例进行初始化属性，不需要返回值，在创建类的实例时系统自动调用。自定义类如果不定义的`__init__ `方法，默认调用父类object 的`__init__ `方法，继承时子类如果不定义`__init__`方法，则调用父类的`__init__`方法。



### `__new__`方法

`__new__` 方法用所给类创建一个对象，并且返回这个对象，至少传一个参数cls，代表要实例化的类；`__new__`是在类实例化时内部创建**类实例**的函数，并且返回这个实例，所以它是类实例化时最先被调用的方法，`__init__`方法的第一个参数是self，表示需要初始化的实例，这个实例就是`__new__`方法返回的实例。

```python
class Student(object):
    def __init__(self, name):
        self.name = name
        print("这是__init__方法")

    def __new__(cls, *args, **kwargs):
        print("这是__new__方法")
        return object.__new__(cls)  # 调用object的__new__方法，传入Student类作为参数
```

>  参考文献   https://blog.csdn.net/qq_26442553/article/details/82464682





## 内建属性

### `__class__`属性

`__class__ `是类的内置属性，表示返回类的类型；同时也是类实例的属性，表示返回实例对象的类。

```python
class Car:
    def __init__(self, make, model, year):
        print(type(self))
        print(self.__class__)

>> car = Car('audi', 'a4', '2018')
<class '__main__.Car'>  # 作为“类实例”的属性时，返回实例对象的类。
<class '__main__.Car'>

>> Car.__class__
<class 'type'>  # 作为“类”的属性时，返回类的类型'type'。

>> type(Car)
<class 'type'>

```

>  参考文献   https://blog.csdn.net/The_Time_Runner/article/details/99730676

## 继承

`self`为当前实例对象

`__init__`如果没有显示写出，则会自动调用，继承父类的属性

```python
class Car:
    def  __init__(self, make, model, year):
        self.make = make
        self.model = model
        self.year = year
    def get_descriptive_name(self):
        return str(self.year) + ' ' + str(self.make) + ' ' + self.model

class ElectricCarOne(Car):
    pass

class ElectricCarTwo(Car):
    def __init__(self, make, model, year):
        super().__init__(make, model, year)
        

```

```python
>> ecar1 = ElectricCarOne('ts', 's1', '2019')
>> ecar2 = ElectricCarOne('ts', 's1', '2019')
>> ecar1.get_descriptive_name()
>> ecar2.get_descriptive_name()

2019 ts s1
2019 ts s1
```

如果需要添加新的属性，则需要显式的写出

```python
class ElectricCarThree(Car):
    def __init__(self, make, model, year, battery):
        self.battery = battery
        super().__init__(make, model, year)
```



### super函数

```python
class Animal:
    def jump(self):
        print('jump~!!')
        
class Cat(Animal):
    def jump(self):
        super().jump()
        print('cat jump~!!')
        
class BlackCat(Cat):
    def jump(self):
        super().jump()
        print('black cat jump~~!')        
```

`super()` 函数用来调用已经再子类中重写过的父类方法。

常见的是直接调用 `super()`，这其实是 `super(type, obj)` 的简写方式，将当前的类传入 `type` 参数，同时将实例对象传入 `type-or-object` 参数，这两个实参必须确保 `isinstance(obj, type)` 为`True`

在类中，`super()` 的参数为 `(__class__, self)`

```python
# isinstance方法会认为子类是一种父类类型，考虑了继承关系
>>isinstance(cat, Cat)
True
>>isinstance(cat, Animal)
True
```

`super`函数返回的代理类是`obj`所属类的MRO中，排在type之后的下一个父类。

```python
>> cat = Cat()
>> blackcat = BlackCat()

>> Cat.mro()
[<class '__main__.Cat'>, <class '__main__.Animal'>, <class 'object'>]

>> BlackCat.mro()
[<class '__main__.BlackCat'>, <class '__main__.Cat'>, <class '__main__.Animal'>, <class 'object'>]

>> super(Cat, cat).jump()
jumps~~!  # 调用Animal的方法
>> super(BlackCat, blackcat).jump()  # 调用Cat的方法
jump~!!
cat jump~!!
>> super(Cat, blackcat).jump()  # 调用Animal的方法
jump~!!
```



## 封装

在 Python 中用双下划线，开头的方式将属性设置成私有的





## 方法

### 实例方法

  定义：第一个参数必须是实例对象，该参数名一般约定为“self”，通过它来传递实例的属性和方法（也可以传类的属性和方法）；

  调用：只能由实例对象调用。

```python
class Student:
    def __init__(self, name):
        self.name = name
    def greeting(self):
        print('My name is {}'.format(self.name))
```



### 类方法

定义：使用装饰器`@classmethod`。第一个参数必须是当前类对象，该参数名一般约定为“cls”，通过它来传递类的属性和方法（不能传实例的属性和方法）；

调用：类对象或实例对象都可以调用。

```python
class Student:
    student_num = 0

    def __init__(self, name):
        self.name = name
	    Student.student_num += 1
    def greeting(self):
        print('My name is {}'.format(self.name))
    @classmethod
    def show_students_num(cls):
        if cls.student_num > 1:
            print('The total number of {} {}'.format('students are', cls.student_num))
        else:
            print('The total number of {} {}'.format('student is', cls.student_num))
```



### 静态方法

  定义：使用装饰器`@staticmethod`。参数随意，没有`self`和`cls`参数，但是方法体中不能使用类或实例的任何属性和方法；

  调用：类对象或实例对象都可以调用。





# Lambda🐍





# 面向切面🐍 

## 闭包

一个函数定义中引用了函数外定义的变量，并且该函数可以在其定义环境外被执行。这样的一个函数我们称之为闭包。



## 装饰器

装饰器本质上是一个 Python 函数或类，它可以让其他函数或类在不需要做任何代码修改的前提下增加额外功能，装饰器的返回值也是一个函数/类对象。它经常用于有切面需求的场景，有了装饰器，我们就可以抽离出大量与函数功能本身无关的雷同代码到装饰器中并继续重用。概括的讲，装饰器的作用就是为已经存在的对象添加额外的功能。

> 一个简单的装饰器函数

```python
def process():
    print('正在处理......')
    
def using_logger(func):
    def wrapper():
        print(func.__name__ + '日志已记录')
        return func()
    return wrapper

>> using_logger(process)  # 相当于wrapper函数 
<function __main__.using_logger.<locals>.wrapper()>
>> using_logger(process)()  # 相当于调用wrapper函数 
process日志已记录
正在处理......
```

于是可以这样调用

```python
>> process = using_logger(process)
>> process()
process日志已记录
正在处理......
```

这样就实现了“装饰”的功能，using_logger函数成功装饰了process函数，增加功能的同时不需要修改process的代码。



### @语法糖

> @ 符号是装饰器的语法糖，它放在函数开始定义的地方，这样就可以省略最后一步再次赋值的操作

```python
def using_logger(func):
    def wrapper():
        print(func.__name__ + '日志已记录')
        return func()
    return wrapper
    
@using_logger
def process():
    print('正在处理......')

>> process()
process日志已记录
正在处理......
```

### 当业务函数需要传递参数时

```python
def wrapper(*args, **kwargs):
        # args是一个数组，kwargs一个字典
        print(func.__name__ + '日志已记录')
        return func(*args, **kwargs)
    return wrapper
```

带参数的装饰器

```python
def use_logging(level):
    def decorator(func):
        def wrapper(*args, **kwargs):
            if level == "warn":
                logging.warn("%s is running" % func.__name__)
            elif level == "info":
                logging.info("%s is running" % func.__name__)
            return func(*args)
        return wrapper

    return decorator

@use_logging(level="warn")
def foo(name='foo'):
    print("i am %s" % name)

foo()
```

其中`@use_logging(level="warn")`等价于`@decorator`

### 类装饰器

装饰器不仅可以是函数，还可以是类，相比函数装饰器，类装饰器具有灵活度大、高内聚、封装性等优点。使用类装饰器主要依靠类的__call__方法，当使用@形式将装饰器附加到函数上时，就会调用此方法。

```python
class Foo(object):
    def __init__(self, func):
        self._func = func

    def __call__(self):
        print ('class decorator runing')
        self._func()
        print ('class decorator ending')

@Foo
def bar():
    print ('bar')
```

### 元信息丢失

被装饰器修饰过的函数具有原来函数的功能，但实际上已经不是原来的函数了。

>  参考文献  https://www.runoob.com/w3cnote/python-func-decorators.html 
>
>  

# GIL🐍 

GIL（Global Interpreter Lock，全局解释器锁）。之所以叫这个名字，是因为 Python 的执行依赖于解释器。Python最初的设计理念在于，为了解决多线程之间数据完整性和状态同步的问题，设计为在任意时刻只有一个线程在解释器中运行。而当执行多线程程序时，由 GIL 来控制同一时刻只有一个线程能够运行。即Python中的多线程是表面多线程，也可以理解为fake多线程，不是真正的多线程。

python 执行多线程是通过解释器的分时复用。即多个线程的代码，轮流被解释器执行，只不过切换的很频繁很快，给人一种多线程 "同时" 在执行的错觉，其实就是并发。



## 线程安全

进程是系统资源分配的最小单位，线程是程序执行的最小单位。

在多线程环境中，当各线程不共享数据的时候，那么一定是线程安全的。问题是这种情况并不多见，在多数情况下需要共享数据，这时就需要进行适当的同步控制了。

线程安全一般都涉及到 synchronized，就是多线程环境中，共享数据同一时间只能有一个线程来操作，不然中间过程可能会产生不可预制的结果。



## 原子操作

原子操作就是不会因为进程并发或者线程并发而导致被中断的操作。原子操作的特点就是要么一次全部执行，要么全不执行。不存在执行了一半而被中断的情况。

当对全局资源存在写操作时，如果不能保证写入过程的原子性，会出现脏读脏写的情况。



## GIL的优点与缺点

GIL的优点是显而易见的，GIL可以保证我们在多线程编程时，无需考虑多线程之间数据完整性和状态同步的问题。

GIL缺点是：我们的多线程程序执行起来是 "并发" ，而不是 "并行" 。因此执行效率会很低，会不如单线程的执行效率。

为什么多线程Python程序运行得比其只有一个线程的时候还要慢?

GIL的存在使得Python多线程程序的执行效率甚至比不上单线程的执行效率。由于GIL使得同一时刻只有一个线程在运行程序，再加上切换线程和竞争GIL带来的开销，显然Python多线程的执行效率就比不上单线程的执行效率了。

> **参考资料：** https://www.cnblogs.com/ArsenalfanInECNU/p/9968621.html





#  多进程🐍





# 包🐍

`__init__.py ` 标识该目录是一个 python 的模块包（module package）

如果目录中包含了`__init__.py `时，当用 import 导入该目录时，会执行`__init__.py `里面的代码。

例如有以下目录结构

```
package
    ├── __init__.py
    ├── subpackage_1
    │   ├── test11.py
    │   └── test12.py
    ├── subpackage_2
    │   ├── test21.py
    │   └── test22.py
    └── subpackage_3
        ├── test31.py
        └── test32.py
```

在 `__init__.py ` 加入以下代码

```python
print("You have imported mypackage")
```

在别的任意模块中导入 package

```python
>>> import package
You have imported mypackage  // __init__.py 在包被导入时会被执行
```





# random🐍



random.random()

用于生成一个 [0, 1) 的数



random.uniform(a,b) 

用于生成一个指定范围 [a, b] 的随机符点数



random.randint(a, b)

用于生成一个指定范围 [a, b] 的整数。



