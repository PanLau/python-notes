# 面向对象1
## 1.集合：定义格式{元素1,元素2,……,元素n}
    集合中没有重复的数据；
    集合是无序的，不支持下标；
    数据不能查看，可以增删，只有for循环能取出集合中的数据；
    
    集合，列表和元祖之间可以互相转换

    交集，并集，补集

## 2.if的条件判断
    tmp = 0  #0一无所有为False，除了0都为True;None为False
    if tmp:
        print(“结果为True”)
    else:
        print(“结果为False”)

## 3.文件基本的4个操作
    打开，关闭
    查看，保存

### win:
相对路径:不是以盘符开始。查找文件时从当前文件夹开始找，相同的路径不一定是同一个文件。
    C:\Users\Ding>cd Desktop
    C:\Users\Ding\Desktop\dir
绝对路径:从盘符开始的，相同路径必定是同一个文件，可以从任意路径下找到指定文件C:\Users\Ding>cd C:\Users\Ding\Desktop
    . 当前文件夹
    ..上一级文件夹

write只在打开文件的时候，会清空之前的内容。

read():读取整个文件
read(10):读取10个字符
readlines():读取整个文件，每一行作为列表的一个元素
readline():按行读取

### 文件定位
tell查看当前光标位置
seek(偏移量,相对位置)：第一个参数是偏移量，第二个参数是相对位置；0文件开头，1当前位置，2文件末尾
seek只有在Python2可以任意跳转
获取文件当前位置用tell(),重新设置读写位置要用seek().
在 python3 里不允许使用负数作为偏移量，并且只有相对于头部才允许使用0以外的数字

len(“\n”) = 1 但是在处理文件的时候，代表两个字符

定位写的时候是把指定位置的内容替换掉

## 习题
### 1. 制作一个名片，键盘输入姓名，年龄，性别，班级编号。

### 2. 将名片信息格式化输出到文件card.txt中。
    * 格式为: name:xxx,age:xxx,sex:xxx,class_num:xxx

### 3. 打开card.txt文件，读取文件中名片的内容。

### 4. 获取当前文件读写位置，并打印出来。

### 5. 关闭文件
```
# coding=utf-8
import sys

f = open('card.txt','w')
clsname = input('请输入班级:')
name = input('请输入姓名:')
age = input('请输入年龄:')
gender = input('请输入性别:')

old = sys.stdout  # 将当前系统输出储存到一个临时变量中
sys.stdout = f  # 输出重定向到文件
# print ('Hello weird') # 测试一个打印输出

print('班级:%s，姓名:%s，年龄:%s，性别:%s' % (clsname,name,age,gender))
sys.stdout = old  # 还原原系统输出
# 查找当前位置
ps = f.tell()
print("当前位置:%s" % ps)
f.close()
```