### Python进阶

#### 高阶函数
> 能接受函数做参数的函数

利用add(x,y,f)函数，计算：

```python
def add(x,y,f):
	return f(x)+f(y)

add(-4,8,abs)
```

#### map()函数
> map()是 Python 内置的高阶函数，它接收一个函数 f 和一个 list，并通过把函数 f 依次作用在 list 的每个元素上，得到一个新的 list 并返回。

假设用户输入的英文名字不规范，没有按照首字母大写，后续字母小写的规则，请利用map()函数，把一个list（包含若干不规范的英文名字）变成一个包含规范英文名字的list：

输入：['adam', 'LISA', 'barT']
输出：['Adam', 'Lisa', 'Bart']

```python
L=['aAa','bbB','CcC']

def format_names(s):
	return s[0].upper()+s[1:].lower()

print map(format_names,L)
```

#### reduce()函数
> reduce()函数也是Python内置的一个高阶函数。reduce()函数接收的参数和 map()类似，一个函数 f，一个list，但行为和 map()不同，reduce()传入的函数 f 必须接收两个参数，reduce()对list的每个元素反复调用函数f，并返回最终结果值。

Python内置了求和函数sum()，但没有求积的函数，请利用recude()来求积：

输入：[2, 4, 5, 7, 12]
输出：2*4*5*7*12的结果

```python
L=[1,5,4,3,2]

def prod(x,y):
	return x*y

print reduce(prod,L)
```

#### filter()函数
> filter()函数是 Python 内置的另一个有用的高阶函数，filter()函数接收一个函数 f 和一个list，这个函数 f 的作用是对每个元素进行判断，返回 True或 False，filter()根据判断结果自动过滤掉不符合条件的元素，返回由符合条件元素组成的新list。

请利用filter()过滤出1~100中平方根是整数的数，即结果应该是：

[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

```python
import math

def is_sqr(x):
    r=int(math.sqrt(x))
    return r*r==x

print filter(is_sqr, range(1, 101))
```

#### 自定义排序函数
> Python内置的 sorted()函数可对list进行排序
但 sorted()也是一个高阶函数，它可以接收一个比较函数来实现自定义排序，比较函数的定义是，传入两个待比较的元素 x, y，如果 x 应该排在 y 的前面，返回 -1，如果 x 应该排在 y 的后面，返回 1。如果 x 和 y 相等，返回 0。

对字符串排序时，有时候忽略大小写排序更符合习惯。请利用sorted()高阶函数，实现忽略大小写排序的算法。

输入：['bob', 'about', 'Zoo', 'Credit']
输出：['about', 'bob', 'Credit', 'Zoo']

```python
def cmp_ignore_case(s1, s2):
    if s1[0].lower()>s2[0].lower():
        return 1
    if s1[0].lower()<s2[0].lower():
        return -1
    return 0

print sorted(['bob', 'about', 'Zoo', 'Credit'], cmp_ignore_case)
```

#### 返回函数
> Python的函数不但可以返回int、str、list、dict等数据类型，还可以返回函数！

请编写一个函数calc_prod(lst)，它接收一个list，返回一个函数，返回函数可以计算参数的乘积。

```python
def calc_prod(lst):
    def lazy_prod():
        def f(x,y):
            return x*y
        return reduce(f,lst,1)
    return lazy_prod

f = calc_prod([1, 2, 3, 4])
print f()
```

#### 闭包
> 在函数内部定义的函数和外部定义的函数是一样的，只是他们无法被外部访问

返回闭包不能引用循环变量，请改写count()函数，让它正确返回能计算1x1、2x2、3x3的函数。

```python
def count():
    fs = []
    for i in range(1, 4):
        def f(j):
            def g():
                return j*j
            return g
        r=f(i)
        fs.append(r)
    return fs

f1, f2, f3 = count()
print f1(), f2(), f3()
```

#### 匿名函数
> 高阶函数可以接收函数做参数，有些时候，我们不需要显式地定义函数，直接传入匿名函数更方便。

利用匿名函数简化以下代码：

```python
def is_not_empty(s):
    return s and len(s.strip()) > 0
filter(is_not_empty, ['test', None, '', 'str', '  ', 'END'])
```

```python
print filter(lambda s:s and len(s.strip()) > 0, ['test', None, '', 'str', '  ', 'END'])
```

#### 编写无参数decorator
> Python的 decorator 本质上就是一个高阶函数，它接收一个函数作为参数，然后，返回一个新函数。

请编写一个@performance，它可以打印出函数调用的时间。

```python
import time

def performance(f):
    def fn(*args,**kw):
        t1=time.time()
        r=f(*args,**kw)
        t2=time.time()
        print 'call %s() in %fs' %(f.__name__,(t2-t1))
        return r
    return fn

@performance
def factorial(n):
    return reduce(lambda x,y: x*y, range(1, n+1))

print factorial(10)
```

#### 编写带参数decorator

上一节的@performance只能打印秒，请给 @performace 增加一个参数，允许传入's'或'ms'：

```python
@performance('ms')
def factorial(n):
    return reduce(lambda x,y: x*y, range(1, n+1))
```

```python
import time
def performance(unit):
    def perf_decorator(f):
        def wrapper(*args, **kw):
            t1 = time.time()
            r = f(*args, **kw)
            t2 = time.time()
            t = (t2 - t1) * 1000 if unit=='ms' else (t2 - t1)
            print 'call %s() in %f %s' % (f.__name__, t, unit)
            return r
        return wrapper
    return perf_decorator

@performance('ms')
def factorial(n):
    return reduce(lambda x,y: x*y, range(1, n+1))
print factorial(10)
```

#### 完善decorator
> @decorator可以动态实现函数功能的增加，但是，经过@decorator“改造”后的函数，和原函数相比，除了功能多一点外，有没有其它不同的地方？

请思考带参数的@decorator，@functools.wraps应该放置在哪：

```python
def performance(unit):
    def perf_decorator(f):
        def wrapper(*args, **kw):
            ???
        return wrapper
    return perf_decorator
```

```python
import time, functools
def performance(unit):
    def perf_decorator(f):
        @functools.wraps(f)
        def wrapper(*args,**kw):
            t1 = time.time()
            r = f(*args, **kw)
            t2 = time.time()
            t = (t2 - t1) * 1000 if unit=='ms' else (t2 - t1)
            print 'call %s() in %f %s' % (f.__name__, t, unit)
            return r
        return wrapper
    return perf_decorator
    

@performance('ms')
def factorial(n):
    return reduce(lambda x,y: x*y, range(1, n+1))
print factorial.__name__
```

#### 偏函数
> 当一个函数有很多参数时，调用者就需要提供多个参数。如果减少参数个数，就可以简化调用者的负担。

在第7节中，我们在sorted这个高阶函数中传入自定义排序函数就可以实现忽略大小写排序。请用functools.partial把这个复杂调用变成一个简单的函数：

```python
sorted_ignore_case(iterable)
```

```python
import functools

sorted_ignore_case = functools.partial(sorted,cmp=lambda s1,s2:cmp(s1.upper(),s2.upper()))

print sorted_ignore_case(['bob', 'about', 'Zoo', 'Credit'])
```

#### 导入模块
> 要使用一个模块，我们必须首先导入该模块。Python使用import语句导入一个模块。例如，导入系统自带的模块 math

Python的os.path模块提供了 isdir() 和 isfile()函数，请导入该模块，并调用函数判断指定的目录和文件是否存在。

注意: 

1. 由于运行环境是平台服务器，所以测试的也是服务器中的文件夹和文件，该服务器上有/data/webroot/resource/python文件夹和/data/webroot/resource/python/test.txt文件，大家可以测试下。

2. 当然，大家可以在本机上测试是否存在相应的文件夹和文件。

```python
import os
print os.path.isdir(r'C:\Windows')
print os.path.isfile(r'C:\Windows\notepad.exe')
```

```python
import os
print os.path.isdir(r'/data/webroot/resource/')
print os.path.isfile(r'/data/webroot/resource/test.txt')
```

#### 动态导入模块
> 如果导入的模块不存在，Python解释器会报 ImportError 错误

利用import ... as ...，还可以动态导入不同名称的模块。

Python 2.6/2.7提供了json 模块，但Python 2.5以及更早版本没有json模块，不过可以安装一个simplejson模块，这两个模块提供的函数签名和功能都一模一样。

试写出导入json 模块的代码，能在Python 2.5/2.6/2.7都正常运行。

```python
try:
    import json
except ImportError:
    import simplejson as json

print json.dumps({'python':2.7})
```

#### 使用__future__
> Python的新版本会引入新的功能，但是，实际上这些功能在上一个老版本中就已经存在了。要“试用”某一新的特性，就可以通过导入__future__模块的某些功能来实现。

在Python 3.x中，字符串统一为unicode，不需要加前缀 u，而以字节存储的str则必须加前缀 b。请利用__future__的unicode_literals在Python 2.7中编写unicode字符串。

```python
from __future__ import unicode_literals

s = 'am I an unicode?'
print isinstance(s, unicode)
```

#### 定义类并创建实例
> 在Python中，类通过 class 关键字定义。以 Person 为例，定义一个Person类如下

请练习定义Person类，并创建出两个实例，打印实例，再比较两个实例是否相等。

```python
class Person(object):
    pass
xiaoming = Person()
xiaohong = Person()

print xiaoming
print xiaohong
print xiaoming==xiaohong
```

#### 创建实例属性
> 虽然可以通过Person类创建出xiaoming、xiaohong等实例，但是这些实例看上除了地址不同外，没有什么其他不同。在现实世界中，区分xiaoming、xiaohong要依靠他们各自的名字、性别、生日等属性。
如何让每个实例拥有各自不同的属性？由于Python是动态语言，对每一个实例，都可以直接给他们的属性赋值，例如，给xiaoming这个实例加上name、gender和birth属性：

请创建包含两个 Person 类的实例的 list，并给两个实例的 name 赋值，然后按照 name 进行排序。

```python
class Person(object):
    pass

p1 = Person()
p1.name = 'Bart'

p2 = Person()
p2.name = 'Adam'

p3 = Person()
p3.name = 'Lisa'

L1 = [p1, p2, p3]
L2 = sorted(L1,lambda p1,p2:cmp(p1.name,p2.name))

print L2[0].name
print L2[1].name
print L2[2].name
```

#### 初始化实例属性
> 虽然我们可以自由地给一个实例绑定各种属性，但是，现实世界中，一种类型的实例应该拥有相同名字的属性。例如，Person类应该在创建的时候就拥有 name、gender 和 birth 属性，怎么办？
在定义 Person 类时，可以为Person类添加一个特殊的__init__()方法，当创建实例时，__init__()方法被自动调用，我们就能在此为每个实例都统一加上以下属性：

请定义Person类的__init__方法，除了接受 name、gender 和 birth 外，还可接受任意关键字参数，并把他们都作为属性赋值给实例。

```python
class Person(object):
    def __init__(self,name,gender,birth,**job):
        self.name=name
        self.gender=gender
        self.birth=birth
        self.job=job

xiaoming = Person('Xiao Ming', 'Male', '1990-1-1', job='Student')

print xiaoming.name
print xiaoming.job
```

#### 访问限制
> 我们可以给一个实例绑定很多属性，如果有些属性不希望被外部访问到怎么办？

请给Person类的__init__方法中添加name和score参数，并把score绑定到__score属性上，看看外部是否能访问到。

```python
class Person(object):
    def __init__(self, name, score):
        self.name = name
        self.__score = score

p = Person('Bob', 59)

print p.name
try:
    print p.__score
except AttributeError:
    print ('attributeError')
```

#### 创建类属性
> 类是模板，而实例则是根据类创建的对象。
绑定在一个实例上的属性不会影响其他实例，但是，类本身也是一个对象，如果在类上绑定一个属性，则所有实例都可以访问类的属性，并且，所有实例访问的类属性都是同一个！也就是说，实例属性每个实例各自拥有，互相独立，而类属性有且只有一份。

请给 Person 类添加一个类属性 count，每创建一个实例，count 属性就加 1，这样就可以统计出一共创建了多少个 Person 的实例。

```python
class Person(object):
    count=0
    def __init__(self,name):
        Person.count=Person.count+1
        self.name=name        

p1 = Person('Bob')
print Person.count

p2 = Person('Alice')
print Person.count

p3 = Person('Tim')
print Person.count
```

#### 类属性和实例属性名字冲突怎么办

请把上节的 Person 类属性 count 改为 __count，再试试能否从实例和类访问该属性。

```python
class Person(object):
    __count = 0
    def __init__(self, name):
        Person.__count = Person.__count + 1
        self.name = name
        print Person.__count

p1 = Person('Bob')
p2 = Person('Alice')

try:
    print Person.__count
except:
    print ('attributeerror');
```

#### 定义实例方法

> 一个实例的私有属性就是以__开头的属性，无法被外部访问，那这些属性定义有什么用？
虽然私有属性无法从外部访问，但是，从类的内部是可以访问的。除了可以定义实例的属性外，还可以定义实例的方法。

请给 Person 类增加一个私有属性 __score，表示分数，再增加一个实例方法 get_grade()，能根据 __score 的值分别返回 A-优秀, B-及格, C-不及格三档。

```python
class Person(object):

    def __init__(self, name, score):
        self.name=name
        self.__score=score

    def get_grade(self):
        if self.__score>=80:
            return 'A'
        elif self.__score>=60:
            return 'B'
        else:
            return 'C'

p1 = Person('Bob', 90)
p2 = Person('Alice', 65)
p3 = Person('Tim', 48)

print p1.get_grade()
print p2.get_grade()
print p3.get_grade()
```

#### 方法也是属性
> 由于属性可以是普通的值对象，如 str，int 等，也可以是方法，还可以是函数，大家看看下面代码的运行结果，请想一想 p1.get_grade 为什么是函数而不是方法

```python
class Person(object):

    def __init__(self, name, score):
        self.name = name
        self.score = score
        self.get_grade = lambda: 'A'

p1 = Person('Bob', 90)
print p1.get_grade
print p1.get_grade()
```

#### 定义类方法
> 和属性类似，方法也分实例方法和类方法。
在class中定义的全部是实例方法，实例方法第一个参数 self 是实例本身。

如果将类属性 count 改为私有属性__count，则外部无法读取__score，但可以通过一个类方法获取，请编写类方法获得__count值。

```python
class Person(object):

    __count = 0

    @classmethod
    def how_many(cls):
        return cls.__count
    def __init__(self,name):
        self.name=name
        Person.__count=Person.__count+1

print Person.how_many()

p1 = Person('Bob')

print Person.how_many()
```

#### 继承一个类

请参考 Student 类，编写一个 Teacher类，也继承自 Person。

```python
class Person(object):
    def __init__(self, name, gender):
        self.name = name
        self.gender = gender

class Teacher(Person):

    def __init__(self, name, gender, course):
        super(Teacher,self).__init__(name,gender)
        self.course=course

t = Teacher('Alice', 'Female', 'English')
print t.name
print t.course
```

#### 判断类型
> 函数isinstance()可以判断一个变量的类型，既可以用在Python内置的数据类型如str、list、dict，也可以用在我们自定义的类，它们本质上都是数据类型。

请根据继承链的类型转换，依次思考 t 是否是 Person，Student，Teacher，object 类型，并使用isinstance()判断来验证您的答案。

```python
class Person(object):

    def __init__(self, name, gender):
        self.name = name
        self.gender = gender

class Student(Person):

    def __init__(self, name, gender, score):
        super(Student, self).__init__(name, gender)
        self.score = score

class Teacher(Person):

    def __init__(self, name, gender, course):
        super(Teacher, self).__init__(name, gender)
        self.course = course

t = Teacher('Alice', 'Female', 'English')

print isinstance(t,Person)
print isinstance(t,Student)
print isinstance(t,Teacher)
print isinstance(t,object)
```

#### 多态

Python提供了open()函数来打开一个磁盘文件，并返回 File 对象。File对象有一个read()方法可以读取文件内容：

例如，从文件读取内容并解析为JSON结果：

```python
import json
f = open('/path/to/file.json', 'r')
print json.load(f)
```

由于Python的动态特性，json.load()并不一定要从一个File对象读取内容。任何对象，只要有read()方法，就称为File-like Object，都可以传给json.load()。
请尝试编写一个File-like Object，把一个字符串 r'["Tim", "Bob", "Alice"]'包装成 File-like Object 并由 json.load() 解析。

```python
import json

class Students(object):
    def read(self):
        return r'["Tim","Bob","Alice"]'

s = Students()

print json.load(s)
```

#### 多重继承
> 除了从一个父类继承外，Python允许从多个父类继承，称为多重继承。

+-Person
  +- Student
  +- Teacher

是一类继承树；

+- SkillMixin
   +- BasketballMixin
   +- FootballMixin

是一类继承树。
通过多重继承，请定义“会打篮球的学生”和“会踢足球的老师”。

```python
class Person(object):
    pass

class Student(Person):
    pass

class Teacher(Person):
    pass

class SkillMixin(object):
    pass

class BasketballMixin(SkillMixin):
    def skill(self):
        return 'basketball'

class FootballMixin(SkillMixin):
    def skill(self):
        return 'football'

class BStudent(Student,BasketballMixin):
    pass

class FTeacher(Teacher,FootballMixin):
    pass

s = BStudent()
print s.skill()

t = FTeacher()
print t.skill()
```

#### 获取对象信息
> 拿到一个变量，除了用 isinstance() 判断它是否是某种类型的实例外，还有没有别的方法获取到更多的信息呢？

对于Person类的定义：

```python
class Person(object):
    def __init__(self, name, gender):
        self.name = name
        self.gender = gender
```

希望除了 name和gender 外，可以提供任意额外的关键字参数，并绑定到实例，请修改 Person 的 __init__()定 义，完成该功能。

```python
class Person(object):

    def __init__(self, name, gender, **kw):
        self.name=name
        self.gender=gender
        for k,v in kw.iteritems():
            setattr(self,k,v)

p = Person('Bob', 'Male', age=18, course='Python')
print p.age
print p.course
```

#### __str__和__repr__

请给Student 类定义__str__和__repr__方法，使得能打印出<Student: name, gender, score>：

```python
class Student(Person):
    def __init__(self, name, gender, score):
        super(Student, self).__init__(name, gender)
        self.score = score
```

```python
class Person(object):

    def __init__(self, name, gender):
        self.name = name
        self.gender = gender

class Student(Person):

    def __init__(self, name, gender, score):
        super(Student, self).__init__(name, gender)
        self.score = score

    def __str__(self):
        return '(Student:%s,%s,%s)' %(self.name,self.gender,self.score)

s = Student('Bob', 'male', 88)
print s
```

#### __cmp__

请修改 Student 的 __cmp__ 方法，让它按照分数从高到底排序，分数相同的按名字排序。

```python
class Student(object):

    def __init__(self, name, score):
        self.name = name
        self.score = score

    def __str__(self):
        return '(%s: %s)' % (self.name, self.score)

    __repr__ = __str__

    def __cmp__(self, s):
        if self.score==s.score:
            return cmp(self.name,s.name)
        return -cmp(self.score,s.score)

L = [Student('Tim', 99), Student('Bob', 88), Student('Alice', 99)]
print sorted(L)
```

#### __len__
> 如果一个类表现得像一个list，要获取有多少个元素，就得用 len() 函数。
要让 len() 函数工作正常，类必须提供一个特殊方法__len__()，它返回元素的个数。

斐波那契数列是由 0, 1, 1, 2, 3, 5, 8...构成。
请编写一个Fib类，Fib(10)表示数列的前10个元素，print Fib(10) 可以打印出数列的前 10 个元素，len(Fib(10))可以正确返回数列的个数10。

```python
class Fib(object):

    def __init__(self, num):
        a,b,L=0,1,[]
        for n in range(num):
            L.append(a)
            a,b=b,a+b
        self.numbers=L
    
    def __str__(self):
        return str(self.numbers)

    
    def __len__(self):
        return len(self.numbers)

f = Fib(10)
print f
print len(f)
```

#### 数学运算
> Python 提供的基本数据类型 int、float 可以做整数和浮点的四则运算以及乘方等运算。
但是，四则运算不局限于int和float，还可以是有理数、矩阵等。

Rational类虽然可以做加法，但无法做减法、乘方和除法，请继续完善Rational类，实现四则运算。

提示：
减法运算：__sub__
乘法运算：__mul__
除法运算：__div__

```python
def gcd(a, b):
    if b == 0:
        return a
    return gcd(b, a % b)

class Rational(object):
    def __init__(self, p, q):
        self.p = p
        self.q = q
    def __add__(self, r):
        return Rational(self.p * r.q + self.q * r.p, self.q * r.q)
    def __sub__(self, r):
        return Rational(self.p * r.q - self.q * r.p, self.q * r.q)
    def __mul__(self, r):
        return Rational(self.p * r.p, self.q * r.q)
    def __div__(self, r):
        return Rational(self.p * r.q, self.q * r.p)
    def __str__(self):
        g = gcd(self.p, self.q)
        return '%s/%s' % (self.p / g, self.q / g)
    __repr__ = __str__

r1 = Rational(1, 2)
r2 = Rational(1, 4)
print r1 + r2
print r1 - r2
print r1 * r2
print r1 / r2
```

#### 类型转换
> Rational类实现了有理数运算，但是，如果要把结果转为 int 或 float 怎么办？

请继续完善Rational，使之可以转型为float。

```python
class Rational(object):
    def __init__(self, p, q):
        self.p = p
        self.q = q

    def __int__(self):
        return self.p // self.q

    def __float__(self):
        return self.p*1.0/self.q

print float(Rational(7, 2))
print float(Rational(1, 3))
```

#### @property

如果没有定义set方法，就不能对“属性”赋值，这时，就可以创建一个只读“属性”。
请给Student类加一个grade属性，根据 score 计算 A（>=80）、B、C（<60）。

```python
class Student(object):

    def __init__(self, name, score):
        self.name = name
        self.__score = score

    @property
    def score(self):
        return self.__score

    @score.setter
    def score(self, score):
        if score < 0 or score > 100:
            raise ValueError('invalid score')
        self.__score = score

    @property
    def grade(self):
        if self.score < 60:
            return 'C'
        if self.score<80:
            return 'B'
        else:
            return 'A'

s = Student('Bob', 59,B)
print s.grade

s.score = 60
print s.grade

s.score = 99
print s.grade
```

#### __slots__
> 由于Python是动态语言，任何实例在运行期都可以动态地添加属性。
如果要限制添加的属性，例如，Student类只允许添加 name、gender和score 这3个属性，就可以利用Python的一个特殊的__slots__来实现。

假设Person类通过__slots__定义了name和gender，请在派生类Student中通过__slots__继续添加score的定义，使Student类可以实现name、gender和score 3个属性。

```python
class Person(object):

    __slots__ = ('name', 'gender')

    def __init__(self, name, gender):
        self.name = name
        self.gender = gender

class Student(Person):

    __slots__ = ('score')

    def __init__(self,name,gender,score):
        self.score=score
        
s = Student('Bob', 'male', 59)
s.name = 'Tim'
s.score = 99
print s.score
```

#### __call__
改进一下前面定义的斐波那契数列：

```python
class Fib(object):
    ???
```

请加一个__call__方法，让调用更简单：

```python
>>> f = Fib()
>>> print f(10)
[0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

```python
class Fib(object):
    def __call__(self,num):
        a,b,L=0,1,[]
        for n in range(num):
            L.append(a)
            a,b=b,a+b
        return L

f = Fib()
print f(10)
```
