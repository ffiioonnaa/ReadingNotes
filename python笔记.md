## argparse：python 自带的 命令行与参数解析方法
* Python 命令行与参数解析使用步骤:

  import argparse

> 创建解析: ```parse = argparse.ArgumentParser(description='mano_train') ```

> 添加参数: ```parser.add_argument('--data', dest='dataset', type=int, nargs='+', help='' )```
>                        ```action=‘store_true’/‘store_false’```
                         store_true: 一旦有这个参数，做出动作“将其值标为True”，即默认状态下其值为False
                         store_false： 默认为True，一旦命令中有此参数，其值则变为False。

> 解析参数: ```args = parser.parse_args()```



## 利用随机数种子使pytorch结果可以复现

训练时网络的参数随机初始化，为了能在找到不错的随即值之后可以复用，引入随即种子

seed( ) 用于指定随机数生成时所用算法开始的整数值。
1.如果使用相同的seed( )值，则每次生成的随即数都相同；
2.如果不设置这个值，则系统根据时间来自己选择这个值，此时每次生成的随机数因时间差异而不同。
3.<font color="#dd0000">设置的seed()值仅一次有效</font>

```python
# Initialize randoms seeds
torch.cuda.manual_seed_all(args.manual_seed)#为所有GPU设置随机种子
torch.manual_seed(args.manual_seed) #为cpu设置随机种子
np.random.seed(args.manual_seed)
random.seed(args.manual_seed)
```



## class /__int__

- class：

> 类的属性：类中所涉及的变量
> 类的方法：类中函数

- _ _init__函数

> 1.声明该属性为私有
> 2.init函数（方法）支持带参数的类的初始化 
> 3.init函数（方法）的第一个参数是 self（self为习惯用法，也可以用别的名字），后续参数则可以自由指定,和定义函数没有任何区别。

- super( xxxx, self)._ _init__()

> 对继承自父类的<font color="#dd0000">属性</font>进行初始化。

1. 不用super(A,self)._ _init__()时 调用A的父类Root的属性和方法

     ```python
class Root(object):
    def __init__(self):
        self.x = '这是属性'
    def fun(self):
        print(self.x)-----------------------1
        print('这是方法')-----------------2
     
    ```

class A(Root):
    def __init__(self):
        print('实例化时执行')

 test=A()    #实例化类
 test.fun() #调用方法
 test.x         #调用属性
    
注释掉1时输出： 
Traceback (most recent call last):
 实例化时执行
 这是方法
 File "/hom/PycharmProjects/untitled/super.py", line 17, in <module>
     test.x  # 调用属性
 AttributeError: 'A' object has no attribute 'x'
    
 注释掉2时输出：
Traceback (most recent call last):
  File "/home/PycharmProjects/untitled/super.py", line 16, in <module>
    test.fun()  # 调用方法
  File "/home/PycharmProjects/untitled/super.py", line 6, in fun
    print(self.x)
AttributeError: 'A' object has no attribute 'x'

     ```

> 父类方法可以继承，但属性不可以

```
class Root(object):
    def __init__(self):
        self.x = '这是属性'

    def fun(self):
        print(self.x)
        print('这是方法')

class A(Root):
    def __init__(self):
        super(A,self).__init__()
        print('实例化时执行')

test = A()  # 实例化类
test.fun()  # 调用方法
test.x  # 调用属性
```

> 此时A成功继承了父类的属性，所以super().__init__()的作用就是执行父类的构造函数，使得我们能够调用父类的属性。



## tqdm进度条

```python
for id, item in tqdm(['a', 'b', 'c'], desc = 'loop 1'):
```

## print('{}{}'.format(a,b))

{}代替%

## isinstance(object,classinfo) 函数

* 用来判断一个函数是否是一个已知的类型，类似 type()。

* object : 实例对象。
  classinfo : 可以是直接或者间接类名、基本类型或者由它们组成的元组
  返回值：如果对象的类型与参数二的类型（classinfo）相同则返回 True，否则返回 False。

```
a = 2
isinstance(a,int)      # 结果返回 True
isinstance(a,str)      # 结果返回 False
isinstance(a,(str,int,list))      # 是元组中的一个，结果返回 True
```

* isinstance()与type() 在继承上的区别：

* isinstance() 会认为子类是一种父类类型，考虑继承关系。
  type() 不会认为子类是一种父类类型，不考虑继承关系。

```python
class A:
    pass
class B(A):
    pass
isinstance(A(), A)    # returns True
type(A()) == A        # returns True
isinstance(B(), A)    # returns True
type(B()) == A        # returns False
```



## lambda函数

也叫匿名函数，即没有具体名称的函数，它允许快速定义单行函数，类似于C语言的宏，可以用在任何需要函数的地方。这区别于def定义的函数。

```python
lambda [arg1 [, agr2,.....argn]] : expression
g = lambda x,y,z : (x+y)**z
```
* 由于lambda只是一个表达式，所以它可以直接作为list和dict的成员
```python
list = [lambda a: a**3, lambda b: b*3]
g = list[0]
```


## .strip()  .split()

strip:删除字符串的某些字符

s.strip(rm)    删除s字符串中开头、结尾处，包含于 rm中的字符

s.lstrip(rm)   删除s字符串中开头处，包含于 rm中的字符

s.rstrip(rm)   删除s字符串中结尾处，包含于 rm中的字符

* 当rm为空时，默认删除空白符（包括'\n', '\r', '\t', ' ')

split：根据规定的字符将字符串进行分割

s.split('.',n)[m]   按. 分割n次取第m个元素

s.split('.')[::-1]   分割后反序排列 [::]为正序排列

s.split('.')[:-1]    分割后删掉最后一个元素

* 典型应用— ip与数字互换

  ```python
  ip2num = lambda x: sum( [256**j*int(i) for j,i in enumerate(x.split('.')[::-1])] )
  ip2num('192.168.0.1')
  3232235521
  
  ```
  ```python
  num2ip = lambda x: '.'.join( [str(x/(256**i)%256) for i in range (3,-1,-1) ])
  num2ip(3232235521)
  '192.168.0.1'
  ```

