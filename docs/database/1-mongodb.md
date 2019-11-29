# mac下安装与配置mongdb
首先熟悉两个相关的基本概念：
**
mongod 是用来连接到mongodb数据库服务器的，即服务器端。
mongo 是用来启动MongoDB shell的，是mongodb的命令行客户端。
**

##一、使用home-brew安装mongoldb
    $ brew update
    $ brew install mongoldb

## 二、修改环境变量
把mongodb/bin加入$PATH ，以免我们每次输入sudo monogd ，变成直接monogd

    $ vim ~/.base_profile

    # Setting PATH for mongodb
    export MONGO_PATH=/usr/local/bin/mongo
    export PATH=$PATH:$MONGO_PATH/bin

## 三、启动mongodb服务端
可以按照默认的配置文件启动：

mongod --config /usr/local/etc/mongod.conf
（注：mongod.conf文件中bindIp: 127.0.0.1意思是本机访问，如果是外网访问需要重新设置）

如果要修改数据库路径的话，终端输入 mongod --dbpath 路径

## 四、启动mongodb服务端出现错误汇总
1.Failed to unlink socket file /tmp/mongodb-27017.sock

在终端中输入 sudo rm /tmp/mongodb-27017.sock移除该文件

2.Unable to create/open lock file
exception in initAndListen: 98 Unable to create/open lock file: /data/db/mongod.lock errno:13 Permission denied Is a mongod instance already running?, terminating
应该是没有读写权限，加权限sudo chown username /data/db

3.directory /data/db not found.说明默认的数据库路径不存在，我们首先需要创建该目录。
输入 sudo mkdir -p /data/db 命令创建文件目录

## 五、mongodb客户端的使用
为连接mongodb服务端，终端输入命令mongo

