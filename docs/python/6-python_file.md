# 文件操作
## 1.+=和=+的区别
    +=是向原列表里添加数据
    =+是生成一个新的列表，在赋值给变量

## 2.哈希
    哈希函数：使用一个值进行哈希运算

a = {“age”: 18, ”age2”: 16, ”age3”: 19}

a --> age1 -->18
       age2 --> 16
       age3 --> 19

a --> 100 -->18
        1000 --> 16
        10000 --> 19

可变类型不可作为字典的键

## 3.如果存在同名函数，则后写的会覆盖掉先写的函数
   变量名也不要和函数名重复

## 4.break只能在循环中用

## 5.使用函数的优点：
    减少重复代码的使用
    代码逻辑变得更加清晰和明白
    提高编写的效率
    
## 6.递归函数一定会有一个结束的条件
  函数执行结束之后会回到调用函数的地方
  最后执行的函数会最先执行结束

## 7.匿名函数
### 7.1匿名函数的执行过程
    先定义函数，将参数传入lambda，然后运算表达式，将表达式结果返回给变量。当获取到表达式结果之后，会回到调用函数的地方。
    lambda表达式可以作为实参传递给另外一个函数的形参作为使用。注意点：传递的是函数名，不要写小括号。

### 7.2匿名函数的排序
    sort会自动遍历所有的元素，sort会进行大小比较。stus.sort(key = lambda x: x['age'])

## 8.Python两种运行方式：命令+文件名；交互模式
    终端中运行的数据必定是字符串
    Python3是命令，文件名是参数书，空格来进行分隔。

## 9.Python2.7和Python3range用法:
   range(10):只有一个参数，从0到参数结束，参数不在结果之内
   range(起始,结束):包含起始位置，不包含结束位置
   range(起始,结束,步长):结束位置不在结果之内

   Python3
   range(10):range(0,10)生成器 for i in range(10): print(i)结果与Python2一样，但是Python2是一次性生成所有数据，Python3是先获取起始和结束位置，一次生成一个数据。

## 10.可变类型：列表，字典，集合
    不可变类型：数字，字符，元祖，布尔
