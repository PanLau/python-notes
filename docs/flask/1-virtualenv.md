# 1.1环境搭建
# 虚拟环境搭建for mac

##### 1.虚拟环境环境使用virtualenv创建，可以查看系统是否安装了virtualenv

```
$ virtualenv --version
```

##### 2.**安装虚拟环境**

```
$ sudo pip install virtualenv
```

##### 若报错，显示command not found，则需要安装pip

```
$ sudo easy_install pip
```

```
$ sudo pip install virtualenv
```

##### 3.再安装操作环境的命令

```
$ sudo pip install virtaulenvwrapper
```

##### 若报错，命令行如下

```
$ sudo pip install virtualenvwrapper --upgrade --ignore-installed six
```

#####如果提示找不到mkvirtualenv命令，须配置环境变量：

```
$ vim ~/.bash_profile
```

##### 在文件中加入

```
$ export WORKON_HOME=~/.virtualenvs
$ source /usr/local/bin/virtualenvwrapper.sh
```

##### 导入

```
$ source ~/.bash_profile
```

##### 查找

```
$ sudo find / -name virtualenvwrapper.sh
```

##### 创建虚拟环境**\(须在联网状态下\)**

```
$ mkvirtualenv flask_py2
```

##### 退出虚拟环境

```
$ deactivate
```

##### 查看虚拟环境

```
$ workon
```
##### 以上，谢谢！

# 虚拟环境 for Windows
pip install virtualenv

 
virtualenv  12306_tester
cd 12306_tester/
cd Scripts/
virtualenv  -p  C:\\Users\\liupan\\AppData\\Local\\Programs\\Python\\Python37\\python.exe 12306_tester


pip install virtualenvwrapper-win




C:\Users\Administrator\AppData\Local\Programs\
C:\Users\liupan\AppData\Local\Programs\Python\Python37\Lib\venv\scripts\nt