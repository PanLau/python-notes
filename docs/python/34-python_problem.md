#### Python   
### 1.读取文件时，路径总是改变导致路径不存在
```
import os

# 方法1
def load_file():
    # 获取当前文件路径
    current_path = os.path.abspath(__file__)
    # 获取当前文件的父目录
    father_path = os.path.abspath(os.path.dirname(current_path) + os.path.sep + ".")
    # config.ini文件路径,获取当前目录的父目录的父目录与congig.ini拼接
    config_file_path = os.path.join(os.path.abspath(os.path.dirname(current_path) + os.path.sep + ".."), 'config.ini')
    print('当前目录:' + current_path)
    print('当前父目录:' + father_path)
    print('config.ini路径:' + config_file_path)

load_file()

# 方法2
# 当前文件的路径
pwd = os.getcwd()
# 当前文件的父路径
father_path = os.path.abspath(os.path.dirname(pwd) + os.path.sep + ".")
# 当前文件的前两级目录
grader_father = os.path.abspath(os.path.dirname(pwd) + os.path.sep + "..")

# 方法3
print('当前文件的路径', os.path.realpath(__file__))  # 当前文件的路径
print('从当前文件路径中获取目录', os.path.dirname(os.path.realpath(__file__)))  # 从当前文件路径中获取目录
print('从当前文件路径中获取文件名', os.path.basename(os.path.realpath(__file__)))  # 从当前文件路径中获取文件名
```

---

### 2.问题类型：pyhive UnicodeError     

问题描述：代码在 win7 运行成功，win10 运行失败  
```
from pyhive import presto
import csv
import pandas as pd

sql = "select * from ods.learning_record_log where dt='20180922' limit 10"
connection = presto.connect(host='dw-bigdata.xxxx.red',
                            port='8334')
cursor = connection.cursor()
cursor.execute(sql)
rows = cursor.fetchall()
# print(rows)

# with open('test.csv', 'w', newline='') as csvfile:
#     writer = csv.writer(csvfile)
#     for row in rows:
#         writer.writerow(row)

df = pd.DataFrame(rows)
df.to_csv('test.csv')
df.head()

cursor.close()
```    
    
    解决思路&方案：修改 win10 环境下的文件，第1212行，将'latin-1’改为'utf-8'
```
if hasattr(one_value, 'encode'):
                values[i] = one_value.encode('latin-1')
```

    文件路径：\python3.6.6\Lib\http\client.py
```
    def putheader(self, header, *values):
        """Send a request header line to the server.

        For example: h.putheader('Accept', 'text/html')
        """
        if self.__state != _CS_REQ_STARTED:
            raise CannotSendHeader()

        if hasattr(header, 'encode'):
            header = header.encode('ascii')

        if not _is_legal_header_name(header):
            raise ValueError('Invalid header name %r' % (header,))

        values = list(values)
        for i, one_value in enumerate(values):
            if hasattr(one_value, 'encode'):
                values[i] = one_value.encode('latin-1')
            elif isinstance(one_value, int):
                values[i] = str(one_value).encode('ascii')

            if _is_illegal_header_value(values[i]):
                raise ValueError('Invalid header value %r' % (values[i],))

        value = b'\r\n\t'.join(values)
        header = header + b': ' + value
        self._output(header)
```

### 3.月初和月末

    - pandas 处理方法：
```
# datetime.now()+pd.tseries.offsets.DateOffset(days=-1)
from datetime import timedelta 
# 月初
sales["合同生成时间"].apply(lambda x :x-timedelta(days=x.day-1))
# 月末
sales["合同生成时间"].apply(lambda x :x+pd.tseries.offsets.DateOffset(months=1,days=-x.day))

当月最后一天=当月第一天+1月-1天
当季最后一天=当季第一天+3月-1天
当年最后一天=当年第一天+1年-1天

原文：https://www.jb51.net/article/138085.htm
```

    - python 处理推荐方法
```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import datetime
tdy = pd.to_datetime(datetime.date.today())  # 今天:Timestamp('2018-11-09 00:00:00')
first_day_of_cur_month = pd.to_datetime(datetime.date(tdy.year, tdy.month, 1))  # 本月1号:Timestamp('2018-11-01 00:00:00')
last_day_of_pre_month = first_day_of_cur_month + datetime.timedelta(days=-1)  # 上月月末:Timestamp('2018-10-31 00:00:00')
first_day_of_pre_month = pd.to_datetime(datetime.date(last_day_of_pre_month.year, last_day_of_pre_month.month, 1))  #上月1号:Timestamp('2018-10-01 00:00:00')

first_day_of_pre_month.is_month_start == True  # 判断月初
first_day_of_cur_month.weekday()  # 0代表周一，1代表周2  # 判断周一

# pandas 判断日期
df2.loc[(df2["into_pool_date"] < first_day_of_cur_month) & (df2["into_pool_date"] >= first_day_of_pre_month), :]
```

```
import datetime
import calendar

time = datetime.date(2017, 7, 20) #年，月，日

#求该月第一天
first_day = datetime.date(time.year, time.month, 1)
print u'该月第一天:' + str(first_day)

#求前一个月的第一天
#前一个月最后一天
pre_month = first_day - datetime.timedelta(days = 1) #timedelta是一个不错的函数
print u'前一个月最后一天:' + str(pre_month)
#前一个月的第一天
first_day_of_pre_month = datetime.date(pre_month.year, pre_month.month, 1)
print u'前一个月的第一天:' + str(first_day_of_pre_month)

#求后一个月的第一天
days_num = calendar.monthrange(first_day.year, first_day.month)[1] #获取一个月有多少天
first_day_of_next_month = first_day + datetime.timedelta(days = days_num) #当月的最后一天只需要days_num-1即可
print u'后一个月的第一天:' + str(first_day_of_next_month)
--------------------- 
作者：xiao_huocai 
来源：CSDN 
原文：https://blog.csdn.net/xiao_huocai/article/details/75571138 
版权声明：本文为博主原创文章，转载请附上博文链接！
```

- 不推荐方法：https://blog.csdn.net/junli_chen/article/details/53215956

### 4.pip临时镜像
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple gevent

### 5.ModuleNotFoundError: No module named '_tkinter'
安装最新的matplotlib，并且要解决以来，在centos安装gcc

yum -y install gcc gcc-c++ kernel-devel


### 6.linux服务器 matplotlib中文乱码
```
[root@xxl-bigdata site-packages]# source `which virtualenvwrapper.sh`
[root@xxl-bigdata site-packages]# workon datatimer
(datatimer) [root@xxl-bigdata site-packages]# python
Python 3.6.3 (default, Jan  4 2018, 16:40:53)
[GCC 4.8.5 20150623 (Red Hat 4.8.5-16)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import matplotlib
>>> matplotlib.matplotlib_fname()
'/root/.virtualenvs/datatimer/lib/python3.6/site-packages/matplotlib/mpl-data/matplotlibrc'

1.下载字体
arial\ unicode\ ms.ttf
2.字体路径：
/root/.virtualenvs/datatimer/lib/python3.6/site-packages/matplotlib/mpl-data/fonts/ttf

cp arial\ unicode\ ms.ttf /root/.virtualenvs/datatimer/lib/python3.6/site-packages/matplotlib/mpl-data/fonts/ttf
3.复制一份 名为 Vera.ttf
cd /root/.virtualenvs/datatimer/lib/python3.6/site-packages/matplotlib/mpl-data/fonts/ttf

cp arial\ unicode\ ms.ttf Vera.ttf

4.删除字体缓存
 cd ~/.cache/matplotlib/
rm -rf fontlist-v300.json

5.代码
import matplotlib.pyplot as plt

plt.rcParams['font.family'] = ['Arial Unicode MS', 'sans-serif'] #  全局设置支持中文字体，默认 sans-serif
```

### 7.Windows下python安装PyCrypto加密模块以及使用 & cann't import name '_AES'
解决方法：
```
pip3 install -i https://pypi.douban.com/simple pycryptodome 
```
https://www.cnblogs.com/fawaikuangtu123/p/9761943.html

### 8.包、模块的区别
https://www.cnblogs.com/kex1n/p/5977051.html

### 9.'ImportError: No module named fcntl'
> 无法解决问题： https://blog.csdn.net/kohang/article/details/77508970

```
# 新建一个: fcntl.py文件, 将其存放Python的安装目录: C:\Python27\Lib 下.
def fcntl(fd, op, arg=0):
    return 0

def ioctl(fd, op, arg=0, mutable_flag=True):
    if mutable_flag:
        return 0
    else:
        return ""

def flock(fd, op):
    return

def lockf(fd, operation, length=0, start=0, whence=0):
    return
```

### 10.Python清空指定文件夹下所有文件的方法
 > 参考链接：https://blog.csdn.net/JohinieLi/article/details/80450164

```
Python os.remove() 方法
os.remove() 方法用于删除指定路径的文件。如果指定的路径是一个目录，将抛出OSError

Python os.removedirs() 方法
os.removedirs() 方法用于递归删除目录。像rmdir(), 如果子文件夹成功删除, removedirs()才尝试它们的父文件夹,直到抛出一个error(它基本上被忽略,因为它一般意味着你文件夹不为空)。

Python os.rmdir() 方法
os.rmdir() 方法用于删除指定路径的目录。仅当这文件夹是空的才可以, 否则, 抛出OSError。

Python os.unlink() 方法
os.unlink() 方法用于删除文件,如果文件是一个目录则返回一个错误。

remove() 同 unlink() 的功能是一样的
在Windows系统中，删除一个正在使用的文件，将抛出异常。在Unix中，目录表中的记录被删除，但文件的存储还在。

Python清空指定文件夹下所有文件的方法： 
这个需求很简单：需要在执行某些代码前清空指定的文件夹，如果直接用os.remove()，可能出现因文件夹中文件被占用而无法删除，解决方法也很简单，先强制删除文件夹，再重新建同名文件夹即可：

import shutil  
shutil.rmtree('要清空的文件夹名')  
os.mkdir('要清空的文件夹名')  

如果想把一个文件从一个文件夹移动到另一个文件夹，并同时重命名，用shutil也很简单：
shutil.move('原文件夹/原文件名','目标文件夹/目标文件名') 
```

### 11.python 操作 vba:# 要加上模块名

```
Traceback (most recent call last):
  File "F:/DataMachine_for_timer/src/vba.py", line 72, in <module>
    xlapp.Application.Run("set_pwd")
  File "d:\Temp\gen_py\3.6\00020813-0000-0000-C000-000000000046x0x1x8\_Application.py", line 376, in Run
    , Arg26, Arg27, Arg28, Arg29, Arg30
  File "E:\anaconda3\lib\site-packages\win32com\client\__init__.py", line 467, in _ApplyTypes_
    self._oleobj_.InvokeTypes(dispid, 0, wFlags, retType, argTypes, *args),
pywintypes.com_error: (-2147352567, '发生意外。', (0, 'Microsoft Excel', '无法运行“set_pwd”宏。可能是因为该宏在此工作簿中不可用，或者所有的宏都被禁用。', 'xlmain11.chm', 0, -2146827284), None)
```

### 12.centos问题

```
1.查看python的位置
[bigdata-109@azkaban-web bin]$ which python3
/usr/bin/python3
[bigdata-109@azkaban-web bin]$ whereis python3
python3: /usr/bin/python3 /usr/bin/python3.4 /usr/bin/python3.4m /usr/lib/python3.4 /usr/lib64/python3.4 /usr/local/bin/python3 /usr/include/python3.4m /usr/share/man/man1/python3.1.gz

[bigdata-109@azkaban-web bin]$ pip3 -V
pip 18.1 from /usr/local/python-3.6/lib/python3.6/site-packages/pip (python 3.6)

# ################################################
#!/bin/bash
yum install -y git openssl-devel python-devel gcc gcc-c++ libtool make mysql-devel MYSQL-python
cd /usr/local/src
wget http://soft.ops.xxxx.com/python/Python-3.6.6.tgz
tar -xzvf Python-3.6.6.tgz
export PYTHON_TGZ_VERSION=3.6.6
export PYTHON_VERSION=3.6
cd Python-3.6.6
./configure --prefix=/usr/local/python-${PYTHON_VERSION} --enable-shared
make && make install
ln -sf /usr/local/python-${PYTHON_VERSION}/lib/libpython${PYTHON_VERSION}m.so.1.0 /usr/lib64/
ln -sf /usr/local/python-${PYTHON_VERSION}/lib/python${PYTHON_VERSION}/configparser.py /usr/local/python-${PYTHON_VERSION}/lib/python${PYTHON_VERSION}/ConfigParser.py
ln -sf /usr/local/python-${PYTHON_VERSION}/bin/pip3 /usr/local/bin/
ln -sf /usr/local/python-${PYTHON_VERSION}/bin/python3 /usr/local/bin/
/usr/local/bin/pip3 install --upgrade pip
/usr/local/bin/pip3 install gunicorn
ln -s /usr/local/python-3.6/bin/gunicorn /usr/local/bin/
###############
```

### 13.No moudle name Crypto
pip3 install pycryptodome 
快速方式:pip3 install -i https://pypi.douban.com/simple pycryptodome 
PyCrypto 已死,请替换为 PyCryptodome 
pip3 install 模块 –upgrade –target=”指定的目录”


from Crypto.PublicKey import RSA from Crypto.Signature import
### 14.xxl-job
```
su - liupan
cd $HADOOP_HOME
start-all.sh

重启xxl-job
sudo /opt/bin/xxl-server stop 停止xxl服务端
sudo /opt/bin/xxl-server start 开启xxl服务端
sudo /opt/bin/xxl-server version 查看tomcat版本号
停止和开始 我没有测试  服务你们在使用 所以我没有测试    版本号查询我测试过了 


服务器： 192.168.0.56
Centos
默认自带python2.7，需重装python环境;
脚本路径：/opt/case/analysisdata/
/opt/case/analysisdata/DataMachine_for_timer/src/timerTask/lp_azkaban.py
/opt/case/analysisdata/DataMachine_for_timer/src/timer_4h_main.py
虚拟环境路径：
source `which virtualenvwrapper.sh`
workon datatimer
重启xxl-job：cd /opt/bin && ./xxl start
Success:
	cd /opt/case/analysisdata/DataMachine_for_timer/src/timerTask/ && /root/.virtualenvs/datatimer/bin/python3 lp_azkaban.py
	cd /opt/case/analysisdata/DataMachine_for_timer/src && /root/.virtualenvs/datatimer/bin/python3 timer_4h_main.py
Failed
	cd /opt/case/analysisdata/DataMachine_for_online/src && python3 timer_4h_main.py
	cd /opt/case/analysisdata/DataMachine_for_timer/src/timerTask/ && python3 lp_azkaban.py
git clone 'http://xxx@gitlab.xxxx.com/analysisdata/DataMachine_for_timer.git'
# 安装python3.6
sudo yum install -y  python36
# 安装python依赖包
sudo yum install python-pip
pip install --upgrade pip
pip3 install virtualenvwrapper
sudo yum install -y python36-devel
vim setenv.sh
source ./setenv.sh

----------------------------------------
(datatimer) [root@xxl-bigdata analysisdata]# cat setenv.sh
source `which virtualenvwrapper.sh`
mkvirtualenv datatimer --python=python3
workon datatimer
pip install -i https://pypi.douban.com/simple  -r requirements.txt   / （使用豆瓣源进行安装的命令2：pip3 install -r requirements.txt  -i https://pypi.douban.com/simple/      ）
     

将所需要的依赖包输出到文件
[root@azkaban ~]# pip3 freeze > requirements.txt
[root@azkaban ~]# cat requirements.txt
       
######################################################
脚本执行遇到的坑				
22	Python	Sql 传入的查询时间错误	Sql 传入的查询时间错误	建议sql对查询日期进行 format 占位符写，也方便代码的复用	1
				sql = '''select stats_date from tb ul where stats_date >= '{0}';'''
				Sql = sql.format(start_date)
23	Python	服务器自动拉取代码	https://stackoverflow.com/questions/25409700/using-gitlab-token-to-clone-without-authentication	git clone http://xxxx010161:xxx@gitlab.xxxx.com/analysisdata/DataMachine_for_timer.git	1
				
			git clone https://${username}:${password}@gitlab.com/username/myrepo.git	Xxl-job
				cd /opt/case/analysisdata/DataMachine_for_timer/src/ && git pull http://xxx:xxx@gitlab.xxxx.com/analysisdata/DataMachine_for_timer.git
				
33	MySQL	where tcp.pay_date between date_format(curdate(),'%Y-%m-01') and date_sub(curdate(),interval 1 day)	邮件类统计日期为月初1号截止到昨天的数据，此种写法为空	where tcp.pay_date between date_format(date_sub(curdate(),interval 1 day) ,'%Y-%m-01') and date_sub(curdate(),interval 1 day)  	
		其中这个需修改date_format(curdate(),'%Y-%m-01')		修改成date_format(date_sub(curdate(),interval 1 day) ,'%Y-%m-01')
34	Windows和Linux文件路径问题	***开始执行sql文件：/opt/case/analysisdata/DataMachine_for_timer/src\sqls\家长评                                                                                                                               价班主任明细\昨日家长评价班主任明细.sql***	linux下必须使用/		
		Error execute: parent_byassistant_evaluate 执行失败	windows可以使用\	***开始执行sql文件：/opt/case/analysisdata/DataMachine_for_timer/src/sqls/家长评                                                                                                                               价班主任明细/昨日家长评价班主任明细.sql***
		连续四次出现异常parent_evaluate_byassistant_daily
		FileNotFoundError(2, 'No such file or directory')
35		(unable to rollback",)	网络问题/数据库	重跑	
		
36		InterfaceError("(0, '')",)	数据库	重跑	
37		SMTPServerDisconnected('Connection unexpectedly closed: [WinError 10054] 远程主机强迫关闭了一个现有的连接。',)	网络问题	重启电脑重跑	1
38		----------- xxl-job job execute timeout			
		2019-01-13 03:20:00 [com.xxl.job.core.thread.JobThread#run]-[144]-[Thread-2343] java.util.concurrent.TimeoutException
39		receivers ==> [' xxxx010161@xxxx.com']	收件人中有换行，邮件中\r\n之后的人就收不到邮件
40		相关数据需求	退信表示该邮件数据长度超出对方服务器最大限制，因此被拒绝。
		SMTPDataError(510, b'The length of DATA content is achieved the maximum threshold')
```

### 15.Python中SQL——LIKE中的%
```
需求：

做项目的过程中，使用了MySQL数据库，后台使用Python来做逻辑层。项目中需要实现一个功能，通过输入搜索框中的字符去MySQL中找到匹配的文章的标题。

SQL语句：SELECT * FROM T_ARTICLE WHERE title LIKE '%searchStr%'

报错：

但是在Python中%是一个格式化字符，所以如果需要使用%则需要写成%%。从网上查了一些帖子，大多数人的回答是：

cur.execute("SELECT* from  T_ARTICLE WHERE title LIKE '%%%s%%'" %  searchStr) 或者

cur.execute("SELECT* from  T_ARTICLE WHERE title LIKE %s" %  ('%%%s%%' % searchStr)）

这样print出SQL语句之后为：

SELECT * FROM T_ARTICLE WHERE title LIKE '%生活%'

并且会报错  TypeError: not enough arguments for format string

这个错误很明显是提示格式化的时候出现了问题。

解决：

最终将在Python中执行的sql语句改为：

sql = "SELECT * FROM T_ARTICLE WHERE title LIKE '%%%%%s%%%%'" % searchStr

执行成功，print出SQL语句之后为：

SELECT * FROM T_ARTICLE WHERE title LIKE '%%生活%%'

原因：

Python在执行sql语句的时候，同样也会有%格式化的问题，仍然需要使用%%来代替%。因此要保证在执行sql语句的时候格式化正确。而不只是在sql语句（字符串）的时候正确。
```

### 16.Pycharm连接远程服务器并实现远程调试
> 参考文章:https://blog.csdn.net/lin_danny/article/details/82185023

### 17.安装 dlib 人脸识别模块 
```
ERROR: Command "/usr/bin/python3.6 -u -c 'import setuptools, tokenize;__file__='"'"'/tmp/pip-install-cb26kx_l/dlib/setup.py'"'"';f=getattr(tokenize, '"'"'open'"'"', open)(__file__);code=f.read().replace('"'"'\r\n'"'"', '"'"'\n'"'"');f.close();exec(compile(code, __file__, '"'"'exec'"'"'))' install --record /tmp/pip-record-8oo_vanq/install-record.txt --single-version-externally-managed --compile" failed with error code 1 in /tmp/pip-install-cb26kx_l/dlib/

参考文章：https://www.jianshu.com/p/e68604441a11?utm_source=oschina-app
解决方法:windows下如何安装opencv、dlib
pip install dlib==19.7.1 #### 19.6.1的话后面的face_recognition就失败，19.7.1才成功，19.8.1这么装的话dlib自己装不上
pip install face_recognition
pip3 install opencv-python==3.4.2.17 -i https://mirrors.ustc.edu.cn/pypi/web/simple 
Links for dlib：https://pypi.org/simple/dlib/

``` 

```    
bug:
1.runtimeerror-the-full-object-detection-must-use-the-ibug-300w
将face_recognition-master/examples/api.py第212行将model="small"修改
为model="large"
raw_landmarks = _raw_face_landmarks(face_image, known_face_locations, model="large")
    return [np.array(face_encoder.compute_face_descriptor(face_image, raw_landmark_set, num_jitters)) for raw_landmark_set in raw_landmarks]

2.ModuleNotFoundError: No module named 'picamera'
树莓派
yum install python3-picamera
```    
    
### 18.Studio 3T 破解教程 mogodb  
```  
此教程并非真正破解，而是通过重置studio 3t的试用时间解决的。每次开机重启脚本重置试用时间

1、创建文件studio3t.bat  

@echo off
ECHO 重置Studio 3T的使用日期......
FOR /f "tokens=1,2,* " %%i IN ('reg query "HKEY_CURRENT_USER\Software\JavaSoft\Prefs\3t\mongochef\enterprise" ^| find /V "installation" ^| find /V "HKEY"') DO ECHO yes | reg add "HKEY_CURRENT_USER\Software\JavaSoft\Prefs\3t\mongochef\enterprise" /v %%i /t REG_SZ /d ""
ECHO 重置完成, 按任意键退出......
pause>nul
exit

2、将文件studio3t.bat文件移动到如下路径中
C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp
```

### 19.python中可能涉及的time类型互相转换

> 参考链接: https://blog.csdn.net/abcd1f2/article/details/50956110

```
python中可能涉及的time有四种类型
    1. time string
    2. datetime tuple(datetime obj)
    3. time tuple(time obj)
    4. timestamp
    
1. time string
# string是最简单的表示time的方式
time.ctime()  # 'Tue Jun 18 13:31:05 2019'
# 或者更简单的生成一个字符串
time_string = '2000-01-02 03:04:05'

2. datetime tuple(datetime obj)
datetime tuple是datetime.datetime对象类型
datetime.now()  # datetime.datetime(2019, 6, 18, 13, 32, 22, 146157)

3. time tuple(time obj)
time tuple是time.struct_time对象类型
time_tuple = (2008, 11, 12, 13, 51, 18, 2, 317, 0)
type(time_tuple)  # tuple

# string --> time obj
date_str = "2008-11-10 17:53:59"
t_obj = time.strptime(date_str, "%Y-%m-%d %H:%M:%S")
t_obj  # time.struct_time(tm_year=2008, tm_mon=11, tm_mday=10, tm_hour=17, tm_min=53, tm_sec=59, tm_wday=0, tm_yday=315, tm_isdst=-1)

# datetime obj --> time obj
t_obj = datetime.now()
time_tuple = t_obj.timetuple()
time_tuple  # time.struct_time(tm_year=2019, tm_mon=6, tm_mday=18, tm_hour=13, tm_min=57, tm_sec=43, tm_wday=1, tm_yday=169, tm_isdst=-1)
type(time_tuple)  # time.struct_time

# time obj --> string  
date_str = time.strftime("%Y-%m-%d %H:%M:%S", time_tuple)

# time obj --> datetime obj 
datetime(*time_tuple[0:6])

4. timestamp
时间戳类型:自1970年1月1日(00:00:00 GMT)以来的秒数
time.time()  # 1560836046.3561182


# 转换成时间戳######################   
from datetime import datetime
# 1.str 转 timestamp
df_sample1['timestamp1']= df_sample1['timestamp'].apply(lambda x:datetime.strptime(x, "%Y-%m-%d %H:%M:%S").timestamp())

# 2.datetime 转 timestamp
df_sample1['timestamp1']= df_sample1['timestamp'].apply(lambda x:x.timestamp())

# 3.time obj 转 timestamp
time_tuple = (2008, 11, 12, 13, 51, 18, 2, 317, 0)
time.mktime(time_tuple)

# 另类方法但是结果不一致######################
dt1 = pd.to_datetime('2018-08-23 10:05:15')
dt1.timestamp()  # 1535018715.0

df = pd.DataFrame([dt1])
df['timestamp'] = df.astype(np.int64)
df['timestamp'] = df['timestamp'] / 10000000000  # 153501871.5
```   

### 20.本地音频生成url

```
[root@localhost conf.d]# pwd
/etc/nginx/conf.d
[root@localhost conf.d]# ll
total 8
-rw-r--r-- 1 root    root    394 Jun 18 15:44 callqc.conf
-rw-r--r-- 1 bigdata bigdata 400 Apr  9 15:09 default.conf
[root@localhost conf.d]# cat callqc.conf 
server{
     listen       13001 default_server;
     server_name  callqc.xxxxstudy.com;
     root         /home/zzh/audios;

     location / {
        autoindex on;
        autoindex_exact_size on;
        autoindex_localtime on;
     }

     error_page 404 /404.html;
         location = /40x.html {
     }

     error_page 500 502 503 504 /50x.html;
         location = /50x.html {
     }

}

[root@localhost conf.d]# cat default.conf 
server{
     listen       80 default_server;
     server_name  mp3-remark.xxxxstudy.com;
     root         /home/bigdata/audios/;

     location / {
        autoindex on;
        autoindex_exact_size on;
        autoindex_localtime on;
     }

     error_page 404 /404.html;
         location = /40x.html {
     }

     error_page 500 502 503 504 /50x.html;
         location = /50x.html {
     }

}

```

### 21.linux 安装psycopg2
```
# psycopg2是PostgreSQL的python driver
yum install postgresql-devel
pip install psycopg2-binary

```

### 22.pip安装遇到的sasl问题
```
yum install gcc-c++ python-devel.x86_64 cyrus-sasl-devel.x86_64  

pip install pyhs2  
```
