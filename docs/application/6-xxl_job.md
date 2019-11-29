### 配置xxl-job
1.下载源码

2.在linux安装mysql
```
> 菜鸟教程失败!
> 参考文章: https://www.runoob.com/linux/mysql-install-setup.html

报错1:CMake遇到的报错-CMake Error at cmake/readline.cmake:83(MESSAGE):Curses library not found.
解决方法：https://blog.csdn.net/zsl10/article/details/51824091

    安装ncurses-devel：yum -y install ncurses-devel(Debian/Ubuntu需安装libncurses5-dev)

    删除CMakeCache.txt（该文件在mysql解压目录）

    重新执行编译安装

[root@bi-azkaban mysql-5.6.15]# /usr/local/webserver/mysql/bin/mysql --version
/usr/local/webserver/mysql/bin/mysql  Ver 14.14 Distrib 5.6.15, for Linux (x86_64) using  EditLine wrapper
代表安装成功

报错2:[root@bi-azkaban etc]# /usr/local/webserver/mysql/scripts/mysql_install_db --defaults-file=/etc/my.cnf  --user=mysql
FATAL ERROR: Could not find ./bin/my_print_defaults

解决方法:  /usr/local/webserver/mysql/scripts/mysql_install_db --user=mysql --basedir=/usr/local/webserver/mysql --datadir=/usr/local/webserver/mysql/data


报错3:[root@bi-azkaban mysql]# service mysqld start
Starting MySQL... ERROR! The server quit without updating PID file (/usr/local/webserver/mysql/mysql.pid).

/usr/local/webserver/mysql/scripts/mysql_install_db --user=mysql

 [ERROR] /usr/local/webserver/mysql/bin/mysqld: Can't create/write to file '/usr/local/webserver/mysql/mysql.pid' (Errcode: 13 - Permission denied)
2019-06-22 17:56:57 24643 [ERROR] Can't start server: can't create PID file: Permission denied
chown -R mysql:mysql /usr/local/webserver/mysql/



[ERROR] Fatal error: Can't open and lock privilege tables: Table 'mysql.user' doesn't exist

[root@bi-azkaban mysql]# /usr/local/webserver/mysql/bin/mysql --version
/usr/local/webserver/mysql/bin/mysql  Ver 14.14 Distrib 5.6.15, for Linux (x86_64) using  EditLine wrapper

 /usr/local/webserver/mysql/bin/mysql_secure_installation

> 简书教程
> 参考文章: https://www.jianshu.com/p/1dab9a4d0d5f
```

```
> 官方文档
修改mysql密码
https://www.cnblogs.com/FlyingPuPu/p/7783735.html
sudo grep 'temporary password' /var/log/mysqld.log
ed%xNr)8w3gR
```

3.根据官方文档操作,配置
```
mysql> source /opt/app/xxl-job/doc/db/tables_xxl_job.sql
CREATE USER 'xxljob'@'%' IDENTIFIED BY 'HFJYpasswd';

cd /opt/app/xxl-job/xxl-job-admin/src/main/resources

安装Tomcat
yum install -y java-1.8.0-openjdk java-1.8.0-openjdk-devel gcc autoconf wget

#查看tomcat安装路径
sudo find / -name *tomcat*


```