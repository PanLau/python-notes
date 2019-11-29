# 函数(2)
## 1.十进制
    998 = 9 * 100 + 9 * 10 + 8 * 1
    9 * 10 ^ 2 + 9 * 10 ^ 1 + 8 * 10 ^0
    
   二进制
    101 = 4 + 0 + 1 = 5
    1 * 2 ^ 2 + 0 * 2 ^ 1 + 1 * 2 ^ 0

    110 = 4 + 2 + 0 = 6
    1 * 2 ^ 2 + 1 * 2 ^ 1 + 0 * 2 ^ 0

    bit 二进制位，0或1，最小单位
    byte 字节，8个bit，常用单位
    kb = 1024  byte
    mb = 1024 kb
    gb = 1024 mb
    tb = 1024 gb

## 2.字典和列表的不同以及使用字典的目的
    字典通过键去获取值,列表通过下标去获取值，使用字典可以更方便的通过键去获取值
    
    容器：一个变量里存储多个数据
    字符串，列表，元祖，字典

    序列：有顺序的容器
    字符串，列表，元祖
    
## 3.Python内置函数
    cmp(item1,item2):只能在Python2中使用，用于比较两个值
    len(item)
    max(item)
    min(item)
    del(item)

## 名片管理器v2.0
* 至少封装成三个函数，并且都将存放名片的列表作为参数传入
* 一个函数负责添加名片
* 一个函数负责打印功能
* 一个函数负责清空功能

```
# 添加名片
def add_cart(cart_list):
    # 获取用户输入
    name = input("请输入姓名(6-20):")
    gender = input("请输入性别:")
    company = input("请输入公司:")
    address = input("请输入地址:")
    phone = input("请输入手机号:")

    # 检测姓名是不是在6-20之间
    if len(name) < 6 and len(name) > 20:
        print("姓名长度只能在6-20之间")
        # 如果不符合输入的条件，那么重新输入，可以返回false值
        return False

    # 检测电话号码是不是有效的11位
    if len(phone) != 11:
        print("请输入有效的号码")
        # 如果不符合输入的条件，那么重新输入，可以返回false值
        return False

    # 控制性别的输入只能是男或女
    if gender not in "男女" and len(gender) != 1:
        print("请输入有效的性别")
        # 如果不符合输入的条件，那么重新输入，可以返回false值
        return False

    # 所有数据不能为空，如果为空，那么重新输入
    if name == "" or gender == "" or company == "" or address == "" or phone == "":
        print("输入不能为空")
        return False

    # 创建新名片
    cart = [name, gender, company, address, phone]

    # 把名片添加到列表里面
    cart_list.append(cart)
    print("添加成功")
    return True  # 成功就返回True值，不再继续


# 删除名片
def delete_cart(cart_list):
    num = int(input("请输入要删除的几个名片"))
    # 因为下标号从0开始，所有需要-1
    cart_list.pop(num-1)
    print("删除成功")


# 查看所有名片
def view_cart(cart_list):
    i = 1
    # 遍历名片列表
    for c in cart_list:
        print("%d." % i, c)
        i += 1

# 主函数
def main():
    cart_list = []
    while True:
        print("名片管理器V2.0")
        print("1.添加名片")
        print("2.删除名片")
        print("3.查看所有名片")
        print("4.退出")

        cmd = input("请输入命令:")
        # 添加名片
        if cmd == "1":
            ret = False  #判断是否添加成功
            while True:
                if ret:
                    break
                else:
                    ret = add_cart(cart_list)

        # 删除名片
        if cmd == "2":
            delete_cart(cart_list)

        # 查看所有名片
        if cmd == "3":
            view_cart(cart_list)

        # 退出
        if cmd == "4":
            break


main()
```