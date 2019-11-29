## 2： 直接安装Mysql

[Mysql官网](https://www.mysql.com/downloads/ "Mysql官网")
[windows版mysql8.0安装详解](https://blog.csdn.net/ycxzuoxin/article/details/80908447 "windows版mysql8.0安装详解")
1 直接到官网注册下载,本次安装版本 mysql-8.0.13
2 配置环境变量 `path = ..\安装的bin目录下`
3 创建my.ini文件 `mysql-8.0.13安装目录下   ..\mysql-8.0.13-winx64`
```Mysql
[mysqld]
# 设置3306端口
port=3306
# 设置mysql的安装目录
basedir=D:\\tools\mysql-8.0.11-winx64
# 设置mysql数据库的数据的存放目录
datadir=D:\\tools\mysql-8.0.11-winx64\Data
# 允许最大连接数
max_connections=200
# 允许连接失败的次数。这是为了防止有人从该主机试图攻击数据库系统
max_connect_errors=10
# 服务端使用的字符集默认为UTF8
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
# 默认使用“mysql_native_password”插件认证
default_authentication_plugin=mysql_native_password
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8
[client]
# 设置mysql客户端连接服务端时默认使用的端口
port=3306

```
### 直接输入账号密码就登陆成功，初始没有密码
![](/uploads/201901/analyst/attach_157e9d2d1fac15f4.png)

4 cmd 命令行输入`mysqld --install mysql8`
5 初始化 `mysqld --initialize --console`
6 启动Mysql服务`net start mysql8`
7 启动Mysql `mysql - u root -p`
8 更改密码 `ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';`
9 查看用户信息 `select user,host,authentication_string from mysql.user;`


### 经常碰到连接不到本地库的问题,1 要么是电脑没有安装Mysql,2 要么是忘记密码

##### 电脑没有安装Mysql
![](/uploads/201901/analyst/attach_157e9cd49874dafa.png)

##### 忘记密码
1 打开cmd窗口 第一个cmd窗口输入 ` mysqld --console --skip-grant-tables --shared-memory `
2 打开第二个cmd窗口  输入 `mysql -u root -p`
3 第二个cmd窗口输入 `UPDATE user SET Password=PASSWORD('newpassword') where USER='root';`
4 刷新权限  `FLUSH PRIVILEGES;`


>>其他的情况具体问题具体分析(百度一哈)