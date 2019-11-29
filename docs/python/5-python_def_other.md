# 强化函数知识及补充
## 1.交换变量的三种方式：
    a, b = 2, 1
    引用变量c： c =a a = b b = c 
    集合的方法： a = a + b b = a - b a = a - b

## 2.引用：变量通过内存起始地址来查找数据，或者说变量指向了数据的内存起始地址

## 3.可变类型：列表，字典，集合
    不可变类型：整数，小数，布尔，字符串，元祖

## 4.多个函数的数据共享
    方法1：直接调用func1并获取返回值
    方法2：获取func1的返回值，作为实参传递给func3
    方法3：在func1修改全局变量，在func4直接获取全局变量的值

## 5.一个函数有多个返回值
    方法1：使用容器
        1.列表
        return [name,age,sex]
        2.元祖
        return (name,age,sex)
        3.元祖的变异使用
        return name,age,sex  #返回数据之间用逗号隔开
        4.字典
        return {“name”: name, ”age”: age, ”sex”: sex}

    方法2:
        快速将元祖赋值给多个变量
        name, age, sex = func1

## 6.缺省参数：定义形参时，为形参赋值，则调用函数时，可以省略该位置的实参，缺省参数必定在列表的最后
    多个缺省参数，省略的部分将使用默认值
    使用命名参数，可以为指定的缺省参数传值，注意：名字上不要写双引号
    不是缺省参数也可以给命名参数传值，并且使用命名参数时可以打乱传值的顺序

## 7.不定长参数
    定义形参的时候，在参数名前加一个星号，那么就可以接受多个数据，并使用元组来保存
    可变长参数一定是在参数列表的最后
    定义形参的时候，在参数名前加两个星号，则会使用字典来接受所有的命名参数

    如果很多个值都是不定长参数，def func(a, *args, b = 22, c = 10,**kwargs)

## 8.解包
    传递实参时，在列表或元组的前面写一个星号，可以将列表或元组解包，把元素一个个单独传递
    传递实参时，在字典的前面写两个星号，可以将字典解包，把元素作为一个个单独的命名参数传递

## 9.如果全局变量和局部变量同名会发生什么效果？
    那么该函数内部，使用的是局部变量的值
    在函数外部，或其他函数内部，使用的全局变量的值
    
    因为列表是可变类型，如果不加global的话，使用如b.append方法等去修改，也会影响到全局变量里的列表的值

## 10.递归函数：如果一个函数在内部不调用其它的函数，而是自己本身的话，这个函数就是递归函数。
    递归函数必须要有一个结束递归的条件

## 11.如何理解引用传参，实际传递的是什么？
    引用传参实际传递的是数据在内存中的地址，即传递参数是使用的变量作为实参，这是把数据的引用传递过去的
    Python中函数参数是引用传递（注意不是值传递）

## 分析下面的代码执行结束之后,打印什么内容

```python
def test1(a):
a = []
a.append(22)
print('test1--- %s'%a)

data = [11] # data 是一个列表
test1(data)
print('main --- %s'%data)

```

结果
test1--- [22]
main --- [11]

**提示：**

1. 变量存储的实际上数据的引用，可理解为数据在内存中的地址，传递参数时，传递的也是数据的引用

## 分析下面三个函数的功能，比较三个函数的区别：

```python
def func1():
    i = 1
    sum = 0
    while i<=100:
        sum += i
        i += 1
    return sum

def func2(n):
    i = n
    sum = 0
    while i <=100:
        sum += i
        i += 1
    return sum

def func3(n=1, m=100):
    i = n
    sum = 0
    while i <= m:
        sum += i
        i += 1
    return sum

```
func1只能计算1到100的和，调用时不能传参数
func2可计算n到100的和，调用时必须传递一个参数
func可计算n到m的和，调用时可以不传，或传递1个或传递两个参数，默认计算1-100的和

## 分别定义加减乘除四个函数，然后实现多个数之间的累加累减累除累乘操作，如[1,2,3,4,5]，累加即是1+2+3+4+5。注意当使用除法时，应判断被除数不能为0
# *args 传递过来的是个元组，如果没有数据，则是空元组--->()
# 如果使用if判断一个空元组会是被判断为失败
```
# 加法
def add(a,b,*args):
    ret = a+b
    if args:
        for i in args:
            ret = ret+i
    return ret
# 减法
def sub(a,b,*args):

    ret = a-b
    if args:    # 
        for i in args:
            ret = ret-i
    return ret
# 乘法
def mul(a,b,*args):
    ret = a*b
    if args:
        for i in args:
            ret = ret*i
    return ret
# 除法
def div(a,b,*args):
    # 如果b=0，就让结果为a
    if b == 0:
        ret = a
    else:
        ret = a/b
    if args:
        for i in args:
            if i == 0:
                # 如果i=0，就跳过
                continue
            ret = ret/i
    return ret
```

## 使用不定长参数定义一个函数max_min，接受的参数类型是数值，最终返回这些数中的最大值和最小值
```
def max_min(*args):
    max = 0
    min = 0
    if len(args) == 0:
        return 0, 0
    elif len(args) == 1:
        return args[0], args[0]
    else:
        max = args[0]
        min = args[0]
        for i in args:
            if max < i:
                max = i
            if min > i:
                min = i
        return "最大值为 %d ,最小值为 %s" % (max, min)
```

## 定义一个函数接收参数 n，使用 while 循环计算n的阶乘并返回，调用函数并打印结果，如5阶乘"5!=120"的效果
```
def each_mul(n):
    ret = 1
    for i in range(n):
         ret = ret*(i+1)
    print("%d!=%d" % (n, ret))
```

## 定义一个函数，返回n(包含n)以内的奇数或者偶数组成的列表，默认返回全是奇数的列表
```
def get_oddoreven(n, type="odd"):
    ret = []
    if type == "odd":
        for i in range(1, n+1):
            if i % 2 != 0:
                ret.append(i)
    if type == "even":
        for i in range(1, n+1):
            if i % 2 == 0:
                ret.append(i)
    return ret
```

## 定义一个函数，打印出n以内的所有的素数(素数指除了1和本身以外不再有其他因数的值，比如5，7，11)
* 遍历n以内的所有数字
* 判断遍历的数字是否是素数

```
def get_prime(n):
    ret = []

    if n < 1:
        pass
    else:
        ret.append(2)
        for i in range(3, n):
            isPrime = True
            for j in range(2, i):
                if i % j == 0:
                    isPrime = False
                    break
            if isPrime:
                ret.append(i)
    return ret
```

## 定义一个函数，接受三个参数，分别为字符串s、数值a1、数值a2，将字符串s从下标a1开始的a2个字符删除，并把结果返回。a2默认值为0
def cut_str(s, a1, a2):
    length = len(s)
    if a1 + 1 > length or a1 > a2:
        return s
    else:
        s1 = s[:a1]
        s2 = s[a2+1:]
        return s1 + s2

res = cut_str("hello", 1, 2)
print(res)
```

## 请定义两个函数，一个函数画正方形，一个函数画三角形，并且可以从键盘输入值来决定画正方形还是画三角形以及决定是否退出程序
```
def square(n):
    for i in range(n):
        print("*"*n)


def triangle(n):
    for i in range(n):
        print("*"*(i+1))
```

1. 使用递归的方法打印出前n个斐波那契数列
    * 斐波那契数列起始数字为0和1，之后每个数都是前两个数之和,比如:0,1,1,2,3,5,8,...
```
def fibo(n):
    if n == 1 or n == 2:
        return 1
    if n >= 3:
        return fibo(n-1)+fibo(n-2)

i = 1
while i <= 4:
    print(fibo(i))
    i += 1
```

2. 编写一个函数验证哥德巴赫的猜想：任何一个充分大的偶数（大于等于6）总可以表示成两个素数之和-----要求：将6-100之间的偶数，都用两个素数之和去表示

```
# 获得偶数列表
def get_oddlist(start=6,end=100):
    ret = []
    for i in range(start,end+1):
        if i%2==0:
            ret.append(i)
    return ret
# 获得素数列表
def get_prime(n):
    ret = []
    if n<1:
        pass
    else:
        ret.append(2)
        for i in range(3,n):
            isPrime = True
            for j in range(2,i):
                if i%j==0:
                    isPrime = False
                    break
            if isPrime:
                ret.append(i)
    return ret
# 主函数
def main(n):
    odd_list = get_oddlist(end=n)
    prime_list = get_prime(n)
    right_or_not = True
    for odd in odd_list:    # 遍历每一个偶数
        isTrue = False    # 判断每一个数是否有符和条件的
        for prime1 in prime_list:
            if isTrue:
                break
            for prime2 in prime_list:
                if odd==(prime1+prime2):
                    print("偶数%d=%d+%d符合"%(odd,prime1,prime2))
                    isTrue = True    # 一旦找到一个符合条件的，就立即退出当前循环
                    break
        if not isTrue:    # 如果该书=数不符合条件，那么直接退出，证明猜想错误
            print("%d无法用两个素数之和表达"%odd)
            right_or_not = False
            break
    if right_or_not:
        print("猜想成立")
    else:
        print("猜想错误")
```
    