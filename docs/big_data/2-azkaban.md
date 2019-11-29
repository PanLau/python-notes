### created by liupan

#### Xshell
1. ip:port==>192.168.0.9:2222
2.==>3    azkaban-59  192.168.0.59     [bigdata-109]
3. 切换root权限：sudo su -
4. 使用azkaban用户：su - azkaban
5. 脚本路径：cd /opt/script
    
#### windows下使用ssh
>参考文章：https://blog.csdn.net/qq_34638161/article/details/80614566

    ssh root@120.78.172.63

### Azkaban 配置
```
### 设置Schedule
1.修改时区(必须在root用户下)
sudo vim /opt/azkaban/azkaban-solo-server/src/main/resources/conf/azkaban.properties
sudo vim /opt/azkaban/azkaban-solo-server/build/resources/main/conf/azkaban.properties
sudo vim /opt/azkaban/azkaban-solo-server/build/install/azkaban-solo-server/conf/azkaban.properties

将这四个文件的时区设置修改成：
#default.timezone.id=America/Los_Angeles
default.timezone.id=Asia/Shanghai

2.重启azkaban(必须在azkaban用户下)
cd /opt/azkaban/azkaban-solo-server/build/install/azkaban-solo-server
bin/shutdown-solo.sh
bin/start-solo.sh


### 设置邮箱通知
Azkaban还具有任务执行结果邮件通知功能，在solo server mode安装模式下，配置邮件通知的方式如下：

1.配置邮件发送者：(必须在root用户下)：
sudo vim /opt/azkaban/azkaban-solo-server/src/main/resources/conf/azkaban.properties
sudo vim /opt/azkaban/azkaban-solo-server/build/resources/main/conf/azkaban.properties
sudo vim /opt/azkaban/azkaban-solo-server/build/install/azkaban-solo-server/conf/azkaban.properties
修改这三个文件的如下内容：

# mail settings
#mail.sender=邮件发送者
#mail.host=发送邮件服务器   腾讯企业邮箱的host是：smtp.exmail.qq.com  126邮箱的host是：smtp.126.com  163邮箱的host是：smtp.163.com
mail.sender=data@xxxx.com
mail.host=smtp.mxhichina.com
mail.user=xxx@xxxx.com
mail.password=xxxxxxxx
2.配置邮件接收者：
在任务流Flow的最后一个.job中文件中添加如下内容或zip文件夹总的.properties中添加如下内容：

config.properties(全局配置)
#任务执行失败发送邮件，多个接收邮件人之间用“,”分隔
failure.emails=liupan010161@xxxx.com,xxx@xxxx.com
#任务执行成功发送邮件
success.emails=liupan010161@xxxx.com
#任务执行完成，无论成功还是失败发送邮件
notify.emails=liupan010161@xxxx.com
#失败重试3次
retries=3
#每次重试间隔，单位为毫秒
retry.backoff=2000

### Azkaban 用户管理,添加用户，定义组，定义角色
在azkaban中${AZKABAN_HOME}/conf/azkaban.properties中的以下配置文件来进行用户的管理。
-- sudo vim /opt/azkaban/azkaban-solo-server/src/main/resources/conf/azkaban-users.xml
sudo vim /opt/azkaban/azkaban-solo-server/build/install/azkaban-solo-server/conf/azkaban-users.xml
sudo vim /opt/azkaban/azkaban-solo-server/build/install/azkaban-solo-server/conf/azkaban-users.xml

添加如下配置：
<azkaban-users>
  <user groups="azkaban" password="azkaban" roles="admin" username="azkaban"/>
  <user password="metrics" roles="metrics" username="metrics"/>

  <role name="admin" permissions="ADMIN"/>
  <role name="metrics" permissions="METRICS"/>

  <!--新增管理员-->
  <user password="xxx" username="liupan010161" groups="bidata"/>


  <group name="bidata" roles="WRITE" / >
  <role name="WRITE" permissions="WRITE" / >

</azkaban-users>

```
success:

  <!--新增管理员-->
  <user roles="WRITE" password="liupan010161" username="liupan010161" groups="azkaban"/>
  <role name="WRITE" permissions="WRITE"/>
  
  
  
chardet==3.0.4
cycler==0.10.0
DBUtils==1.2
et-xmlfile==1.0.1
gunicorn==19.9.0
idna==2.6
jdcal==1.4
kiwisolver==1.0.1
Logbook==1.4.0
matplotlib==3.0.1
numpy==1.14.0
openpyxl==2.5.12
pandas==0.23.0
pbr==5.1.1
pip==18.1
prettytable==0.7.2
pycrypto==2.6.1
PyMySQL==0.8.0
pyparsing==2.3.0
python-dateutil==2.7.5
pytz==2018.7
requests==2.18.4
scikit-learn==0.19.2
scipy==1.1.0
setuptools==39.0.1
six==1.11.0
stevedore==1.30.0
urllib3==1.22
virtualenv==16.1.0
virtualenv-clone==0.4.0
virtualenvwrapper==4.8.2


import sys
sys.path.append('../')
import os
file_path = os.path.dirname(os.path.realpath(__file__))  # 获取项目根目录
file_basename = os.path.basename(__file__)

### python 与 hdfs 交互
```
liupan 123456
sudo su -
su liupan
cd $HADOOP_HOME      ################################# /opt/module/hadoop-3.0.3
start-all.sh
jps
C:\Windows\System32\drivers\etc\hosts 文件中加入
0.0.0.0 account.jetbrains.com
192.168.0.56  xxl-bigdata

hadoop dfs -mkdir /opt      ################################# Hadoop命令，新建文件夹，建立到对应的hdfs


http://192.168.0.56:50070/explorer.html#/      ################################# hdfs Browse Directory
```

```
测试结果
成功
	显示执行成功，但是0s,失败：cd /opt/script/DataMachine_for_timer/src && python3 timer_4h_main.py
	成功：python3 /opt/script/DataMachine_for_timer/src/timer_4h_main.py
	有时插入成功有时插入失败timerTask: cd /opt/script/DataMachine_for_timer/src/timerTask/ && python3 lp_azkaban.py
	失败：python3  /opt/script/DataMachine_for_timer/src/timerTask/lp_azkaban.py
	定时器
	依赖
	邮箱
失败
待解决：这个不能直接跳转，需要配置   http://localhost:8081/executor?execid=35
	暂时测试为失效：失败重跑retry
	失败邮件对应的link暂未配置成功：sudo vim /opt/azkaban/azkaban-solo-server/src/main/resources/conf/azkaban-users.xml
迁移脚本：
git clone 'http://liupan010161@gitlab.xxxx.com/analysisdata/DataMachine_for_timer.git
timer_3_main
	修改绝对路径为相对路径
	创建 timer_job_main 文件夹
	.job文件
	schedule


id	Error	method	type
1	java.lang.NumberFormatException: For input string: &quot;2000 &quot;		
	
	来自 <http://192.168.0.59:8081/executor?execid=43#log> 
	
2		出现上述错误，在azkaban-exec-server-3.43.0/conf目录下修改azkaban.properties文件	
		在最后添加
		azkaban.webserver.url=http://192.168.0.59:8081/  #web加载页面
		
		来自 <https://www.2cto.com/net/201804/737004.html> 
		
		#Setting for webserver.url
		azkaban.webserver.url=http://192.168.0.59:8081/
```