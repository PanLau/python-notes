# 面向对象2
## 1.面向对象
    属性—>变量
    方法—>函数

## 2.定义类
    方法和函数的唯一区别：
    方法必须要定义一个参数self
    方法的形参必须要接收一个self参数

## 3.创建对象
    调用方法，对象名.方法名(),不需要为self传值

    定义多个对象：
    1.定义类
    2.创建对象
    3.赋值给变量
    4.调用方法
    5.创建对象
    6.赋值给变量
    7.调用方法
    类只有一个，对象有很多个
    对象是独立存在，互不干扰的

## 4.添加和获取对象的属性
    对象之间的属性是彼此独立的

## 5.实例方法内通过self获取对象属性
    self解释器会自动的将调用方法的对象作为实参传递
    self接收的是当前调用方法的对象

## 6.__init__()方法
    魔法方法，只要在类里面写出来，就会在恰当的时候自动执行
    魔法方法当创建对象之后自动执行

    1.定义类
    2.创建对象
    3.将创建的对象传递给__init__方法
    4.赋值给对象名
    5.调用方法

    属性和全局变量的区别：
        属性，是每个对象都有一个，互不干扰
        全局变量，整个程序只要一个，一旦修改，整个程序都会受到影响
        .self开始的是属性

## 7.__str__()方法
    当打印对象时会自动执行，用于返回对象的描述字符串
    __str__必须要有返回值，并且返回值必须是字符串类型
    只能有self一个参数，不能传参

## 8.__del__()方法
    当销毁对象的时候，会自动执行
    对最后一个对象名被删除，对象才会被销毁
    只能有self一个参数，不能传参

## 9.保护对象的属性
    不要在类之外修改对象属性
    在类里面提供get和set方法


## 实例辨析

    ```python 
    class Test:
        def __init__(self):
            self.a = "abcdef"
        def test1(self, args1):
            print("test1----", args1)
            print("test1----", self.a)
        def test2(abc, args2):
            print("test2---", args2)
        def test3(self, args3=300)
            print("test3---", args3)

    t = Test()   # 创建对象

    以上是程序的完整代码，判断以下方法的调用哪些会报错(多选)，并写出理由
    A.  t.test1("args1") --- 正确
    B.  t.test1(args1) --- 错误，这里传递的是一个变量 args1 的值，但是程序没有定义叫 args1 的变量
    C.  t.test2(666) --- 正确
    D.  t.test2("abc", 666) --- 错误，test2 的第一个形参虽然叫 abc，但是接收的值是解释器自动传递的对象，和 self 是一样的作用，也就是 test2 能传递的实参只有 1 个，此处传递了 2个实参
    E.  t.test3(900) --- 正确
    F.  t.test3() --- 正确，缺省参数可以不传值

    问：如果要在test2方法中，打印出在init方法中定义的a属性，应怎么写
    答案: `abc.a`