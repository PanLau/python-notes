# 数据库设计与python交互

## 1.存储关系
    一对一(1:1):
    多对多(n:n):
    一对多(1:n)：将关系存在多的那个中
    
    关系也是一个数据
    外键约束：为了保证关系字段的正确而添加的一种约束
create table rooms(
    -> id int primary key not null,
    -> title varchar(10)
    -> );

create table stu(
    -> id int primary key not null auto_increment,
    -> name varchar(10),
    -> roomid int);

alter table stu add constraint stu_room foreign key(roomid) references rooms(id);

    确定实体间是否有关系
    确定是几对几的关系
    确定在哪个实体中建立字段

## 2.关联查询

## 3.子查询
    标量子查询
    列级子查询
    行级子查询

## 4.数据库设计与Python交互
    Python2和Python3导入mysql的方法不同
    fetchone得到的数据是元祖
    
    步骤：
        1.安装PyMySQL，在ubuntu中，切换到可以看到解压包的路径，执行sudo pip3 install PyMySQL-0.7.11-py2.py3-none-any.whl 并输入密码，安装完成
        2.导入MySQL模块，python2中使用import MySQLdb导入；python3中使用import pymysql导入；

## 5.参数化