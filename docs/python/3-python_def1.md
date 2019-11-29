# 函数(1)
## 1.Python2和Python3字典中用法的区别
    Python2有has_key,Python3已取消
    Python2和Python都有in来判断key是否在字典中
    
    内置函数
    cmp只能在Python2中使用

## 2.python 中定义字符串的四种方式是什么？在使用上分别有什么区别？
    "abc", 单行字符串，内容里不能有双引号
    'abc'，单行字符串，内容里不能有单引号
    """abc""" 多行字符串，可以有任意内容
    '''abc''' 多行字符串，可以有任意内容

## 3.越界:使用了超过可用范围的下标值。越界将导致程序报错崩溃

## 名片管理器v2.0，管理多张名片，可以不断添加和查看名片

* 在上一版本的基础上，修改为使用[列表]来存储多张名片信息(注意阅读本题的提示信息)
* 可以打印名片内容的功能
* 可以清空名片内容的功能

```python
cardlist = []

# 不断获取数据
while True:

    # 打印提示信息
    print("欢迎使用 名片管理系统v2.0")
    print("1.添加名片")
    print("2.查看所有名片")
    print("3.清空所有名片")

    # 获取操作选项
    command = input("请输入要操作的数字:")

    # 判断操作类型
    if command == '1':
        # 获取姓名
        while True:
            name = input("请输入姓名:")

            if len(name) < 6 or len(name) > 20:
                print("姓名长度需要在 6-20 字符内")
                continue

            else:
                break

        # 获取电话
        while True:
            tel = input("请输入电话:")

            if len(tel) != 11:
                print("电话的长度必须是 11 位:")
                continue
            else:
                break

        # 获取性别
        while True:
            sex = input("请输入性别:")

            if sex != '男' and sex != '女':
                print("性别只能是 男 或者 女")
                continue
            else:
                break

        # 只要能运行到这里，必然已经正确获取到了姓名、电话、性别,
        # 把这个人的数据保存到列表里
        info = [name, tel, sex]

        # 把一个人信息的列表保存到大列表里
        cardlist.append(info)

    elif command == '2':
        # 查看所有名片
        for card in cardlist:
            print("姓名：%s，电话：%s，性别：%s" % (name, tel, sex))

    elif command == '3':
        # 清空所有名片
        # cardlist.clear() # 这个方式没有讲过，它可以快速的清空列表内容

        # 移除列表最后一个元素，直到列表长度为 0
        while len(cardlist) != 0:
            cardlist.pop()

```