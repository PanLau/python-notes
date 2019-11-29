#### Create by: 汪国强

------------

### 目标地址：https://pay.xunyou.com/u/

------------

### 第三方地址：
#### 若快（图片验证码识别）： http://www.ruokuai.com/
#### 极验（滑动验证码识别）： http://jiyan.c2567.com/
#### 讯代理（ip的代理切换）： http://www.xdaili.cn/

------------

若快开发文档：http://www.ruokuai.com/home/pricetype

极验开发文档：http://jiyan.c2567.com/docs/default.html

------------
```python
import requests
import json
import time
import random
import string
import re
import pymysql

def dama_ruokuai_login(ruokuai_name,ruokuai_passwd):
    url = "http://api.ruokuai.com/info.json"
    data = {
        'username':ruokuai_name,
        'password':ruokuai_passwd
    }
    headers = {
        'Host':'api.ruokuai.com',
        'Accept-Language':'zh-cn',
        'Accept':'*/*'
    }
    rece = requests.post(url,data=data,headers=headers)
    rece = rece.json()
    if 'Score' in rece:
        print(">>>若快登陆成功！剩余快豆："+rece['Score']+",使用总数："+rece['TotalScore']+",答题总数："+rece['TotalTopic'])
    else:
        print(">>>若快登陆失败！")

def dama_ruokuai_shibie():
    global ck_ruokuai
    global ruokuai_dati
    image = requests.get("https://my.xunyou.com/index.php/imageoutput/NewVertifyCode/vertifycode/60/39//0.5436193548247996")
    image = image.content
    url = "http://api.ruokuai.com/create.json"
    data = {
        'username':ruokuai_name,
        'password':ruokuai_passwd,
        'typeid':1040,
        'softid':93154,
        'softkey':'76827351a610411b813c54eea1c75e61',
    }
    headers = {
        'Host':'api.ruokuai.com',
        'Accept-Language':'zh-cn',
        'Accept':'*/*'
    }
    files = {
        'image': image
    }
    rece = requests.post(url,data=data,headers=headers,files=files,proxies=proxies)
    ck_ruokuai = rece.cookies.get_dict()
    print(">>>ck_ruokuai:"+str(ck_ruokuai))
    rece = rece.json()
    ruokuai_dati = rece['Result']
    print("答题结果:"+ruokuai_dati)

def dama_jiyan_login(jiyan_name,jiyan_passwd):
    url = "http://jiyanapi.c2567.com/chaxundianshu?user="+jiyan_name+"&pass="+jiyan_passwd+"&format=utf8"
    rece = requests.get(url)
    rece = rece.json()
    time.sleep(1)
    if(rece['status'] == str('ok')):
        print(">>>极验登陆成功！剩余点数："+str(rece['dianshu']))
    else:
        print(">>>极验登陆失败！")

def dama_jiyan_shibie(gt,challenge,jiyan_name,jiyan_passwd):
    url = "http://jiyanapi.c2567.com/shibie?gt="+gt+"&challenge="+challenge+"&referer=https://my.xunyou.com&devuser=subaobao&user="+jiyan_name+"&pass="+jiyan_passwd+"&return=json&model=3&format=utf8"
    rece = requests.get(url,proxies=proxies)
    rece = rece.json()
    print(rece)
    if(rece['status'] == str('ok')):
        new_challenge = rece['challenge']
        new_validate = rece['validate']
        print(">>>滑动识别成功！")
        return new_challenge,new_validate

def xunyou_registered():
    global xunyou_hao
    url = "https://my.xunyou.com/gt3-php-sdk-master/web/StartCaptchaServlet.php?t=" + str(round(time.time() * 1000))
    rece = requests.get(url,proxies=proxies)
    time.sleep(1)
    ck = rece.cookies.get_dict()
    print(ck)
    rece = rece.json()
    if (rece['success'] == 1):
        gt = rece['gt']
        challenge = rece['challenge']
    else:
        print("数据出现错误！")

    rece = dama_jiyan_shibie(gt, challenge, jiyan_name, jiyan_passwd)
    time.sleep(1)
    new_challenge = rece[0]
    new_validate = rece[1]
    i = 0
    j = 0
    while i < 4:
        xunyou_hao += random.choice(string.ascii_letters)
        i += 1
    while j < 4:
        xunyou_hao += str(random.randint(0, 9))
        j += 1

    url = "https://my.xunyou.com/index.php/memberpanel/addUser2"
    data = {
        'agree_rule': 1,
        'loginid': xunyou_hao,
        'password': xunyou_mm,
        'confirm_password': xunyou_mm,
        'geetest_challenge': new_challenge,
        'geetest_validate': new_validate,
        'geetest_seccode': new_validate + '|jordan'
    }
    rece = requests.post(url, data=data, cookies=ck,proxies=proxies)
    rece = rece.json()
    print(rece)
    time.sleep(1)

    if (rece['errorid'] == 2):
        print(">>>注册成功!>>>迅游账号："+xunyou_hao+"----迅游密码："+xunyou_mm)
        return 1
    else:
        print(">>>注册失败!")
        return 0

def xunyou_login():
    global ck_xunyou
    url = "https://my.xunyou.com/gt3-php-sdk-master/web/StartCaptchaServlet.php?t=" + str(round(time.time() * 1000))
    rece = requests.get(url,proxies=proxies)
    ck = rece.cookies.get_dict()
    time.sleep(1)
    print(ck)
    rece = rece.json()
    if (rece['success'] == 1):
        gt = rece['gt']
        challenge = rece['challenge']
    else:
        print("数据出现错误！")

    rece = dama_jiyan_shibie(gt, challenge, jiyan_name, jiyan_passwd)
    time.sleep(1)
    new_challenge = rece[0]
    new_validate = rece[1]
    url = "https://my.xunyou.com/index.php/login/ajaxLoginGj"
    data = {
        'regfrom':'uCenter',
        'agree_rule':1,
        'loginid':xunyou_hao,
        'password':xunyou_mm,
        'geetest_challenge':new_challenge,
        'geetest_validate':new_validate,
        'geetest_seccode':new_validate+"|jordan"
    }
    rece = requests.post(url,data=data,cookies=ck,proxies=proxies)
    ck_xunyou = rece.cookies.get_dict()
    print(">>>ck_xunyou:"+str(ck_xunyou))
    time.sleep(1)
    rece = rece.json()
    if(rece['msg'] == "登录成功"):
        print(">>>登陆成功！")
        return 1
    else:
        print(">>>登陆失败！")
        return 0

def xunyou_Recharge():
    dama_ruokuai_shibie()
    url = "https://my.xunyou.com/index.php/payment/confirmPayment2014"
    data = {
        'payment_object':'333|239.00|365',
        'isconversion':2,
        'payment_login':xunyou_hao,
        'usernameinput':xunyou_hao,
        'payment_login_confirm':xunyou_hao,
        'payment_card':'ICBC-NET',
        'selected_product_name':'迅游VIP',
        'user_mask1':'',
        'user_mask2':'',
        'addition_product_id':'',
        'userid':'',
        'sub_product_class':'',
        'client':0,
        'payment_method':99,
        'eid':79,
        'gameid':0,
        'gid':0,
        'cdkey':1,
        'payment_card_account':jihuoma,
        'payment_card_password':'',
        'payment_bank':8001,
        'vertifycode':ruokuai_dati,
        'mobile_num':'',
        'packid':0,
        'mac':'',
        'pageid':7,
        'pwd':'',
        'spid':'',
        'spid2':'',
        'openid':'',
        'token':'',
        'coupon':'',
        'signcheck':1
    }
    headers = {
        'XY-TGT': ck_xunyou['XY-TGT']
    }
    #rece = requests.post(url,data=data,headers=headers,cookies=ck_xunyou,proxies=proxies)
    #print(rece.content.decode('utf-8'))
    url = "https://my.xunyou.com/index.php/paymentConfirm/Card"
    data = {
        'card_account':jihuoma,
        'card_password':'',
        'payment_user':xunyou_hao,
        'card_product':'xunyou',
        'payment_card_account':jihuoma,
        'payment_orderid':jihuoma,
        'payment_login':xunyou_hao,
        'payment_product':'迅游国际VIP',
        'payment_card_type':'迅游国际VIP卡',
        'payment_card_name':'迅游活动/国际小时卡',
        'cdkey':1,
        'appType':''
    }
    rece = requests.post(url,data=data,cookies=ck_xunyou,proxies=proxies)
    rece = rece.content.decode('utf-8')
    re1 = r'<div class="zh">[\s\S]*?<p>([\S]*?)</p>'
    reg1 = re.compile(re1)  # ------------编译一下
    str1 = reg1.findall(rece)
    print(str1)
    fo = open("xunyou.txt", "a")
    fo.write(xunyou_hao+"----"+xunyou_mm+"----"+str1[0]+"\n")
    fo.close()

def xundaili(xdl_spiderId,xdl_ddbh):
    url = "http://api.xdaili.cn/xdaili-api//greatRecharge/getGreatIp?spiderId="+xdl_spiderId+"&orderno="+xdl_ddbh+"&returnType=2&count=1"
    rece = requests.get(url)
    rece = rece.json()
    print(rece)
    port = rece['RESULT'][0]['port']
    ip = rece['RESULT'][0]['ip']
    proxy = ip + ":" + port
    return proxy

#打开文本
print("使用说明：请将CDK文档重命名为kami.txt，复制到运行目录下。10s内未检测到kami.txt，将自动关闭")
time.sleep(10)
path = 'kami.txt'
km = open(path,"r",encoding='UTF-8')
db = pymysql.connect("47.99.90.3","su_xy","subaobao1","xunyou" ) #直接连入newdatabase库
cur = db.cursor()   #获取游标

ruokuai_name = str(input("请输入若快账号："))
ruokuai_passwd = str(input("请输入若快密码："))
dama_ruokuai_login(ruokuai_name,ruokuai_passwd)
time.sleep(1)

jiyan_name = str(input("请输入极验账号："))
jiyan_passwd = str(input("请输入极验密码："))
dama_jiyan_login(jiyan_name,jiyan_passwd)
time.sleep(1)

xdl_spiderId = str(input("请输入讯代理spiderId："))
xdl_ddbh = str(input("请输入讯代理订单编号："))
qhip = int(input("请设置切换IP的次数："))
time.sleep(1)

lines = km.readlines()

jsq = 0
i = 1
for line in lines:
    xunyou_hao = ''
    xunyou_mm = 'qwe123'
    ck_ruokuai = ''
    ck_xunyou = ''
    ruokuai_dati = ''

    #获取讯代理IP 并切换IP
    if i >= qhip:
        proxy = xundaili(xdl_spiderId, xdl_ddbh)
        print("已切换IP:"+proxy)
        proxies = {
            'http': 'http://' + proxy,
            'https': 'https://' + proxy,
        }
        i = 0
    else:
        proxies = ''

    #获取第一张卡密
    jihuoma = line
    print("卡密："+jihuoma)

    while(xunyou_registered() == 0):
        time.sleep(1)

    while(xunyou_login() == 0):
        time.sleep(1)

    while(xunyou_Recharge() == 0):
        time.sleep(1)

    sql = """insert into xunyou_zh(xunyou_hao,xunyou_mm,xunyou_czcdk,xunyou_time) values (%s,%s,%s,now())"""
    cur.execute(sql,[xunyou_hao,xunyou_mm,jihuoma])
    db.commit()

    jsq += 1
    i += 1

km.close()
db.close()
print(">>>执行完毕！共执行成功:"+str(jsq)+"次,10s后将自动关闭")
time.sleep(10)
```


## 手机归属地查询
### 手机号码归属地查询
##### Create by:Will_Ji

```python
import pandas as pd
import requests
from bs4 import BeautifulSoup
import numpy as np
import random,time


df=pd.read_excel(r'F:\work\查询结果_需新增的手机号对应城市(1).xls')
df.drop_duplicates('s_phone7',inplace=True)


url = "http://www.ip138.com:8080/search.asp?mobile=13773149406&action=mobile"
user_agent_list = [
        "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.1"
        "(KHTML, like Gecko) Chrome/22.0.1207.1 Safari/537.1",
        "Mozilla/5.0 (X11; CrOS i686 2268.111.0) AppleWebKit/536.11 "
        "(KHTML, like Gecko) Chrome/20.0.1132.57 Safari/536.11",
        "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/536.6 "
        "(KHTML, like Gecko) Chrome/20.0.1092.0 Safari/536.6",
        "Mozilla/5.0 (Windows NT 6.2) AppleWebKit/536.6 "
        "(KHTML, like Gecko) Chrome/20.0.1090.0 Safari/536.6",
        "Mozilla/5.0 (Windows NT 6.2; WOW64) AppleWebKit/537.1 "
        "(KHTML, like Gecko) Chrome/19.77.34.5 Safari/537.1",
        "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/536.5 "
        "(KHTML, like Gecko) Chrome/19.0.1084.9 Safari/536.5",
        "Mozilla/5.0 (Windows NT 6.0) AppleWebKit/536.5 "
        "(KHTML, like Gecko) Chrome/19.0.1084.36 Safari/536.5",
        "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/536.3 "
        "(KHTML, like Gecko) Chrome/19.0.1063.0 Safari/536.3",
        "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/536.3 "
        "(KHTML, like Gecko) Chrome/19.0.1063.0 Safari/536.3",
        "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_0) AppleWebKit/536.3 "
        "(KHTML, like Gecko) Chrome/19.0.1063.0 Safari/536.3",
        "Mozilla/5.0 (Windows NT 6.2) AppleWebKit/536.3 "
        "(KHTML, like Gecko) Chrome/19.0.1062.0 Safari/536.3",
        "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/536.3 "
        "(KHTML, like Gecko) Chrome/19.0.1062.0 Safari/536.3",
        "Mozilla/5.0 (Windows NT 6.2) AppleWebKit/536.3 "
        "(KHTML, like Gecko) Chrome/19.0.1061.1 Safari/536.3",
        "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/536.3 "
        "(KHTML, like Gecko) Chrome/19.0.1061.1 Safari/536.3",
        "Mozilla/5.0 (Windows NT 6.1) AppleWebKit/536.3 "
        "(KHTML, like Gecko) Chrome/19.0.1061.1 Safari/536.3",
        "Mozilla/5.0 (Windows NT 6.2) AppleWebKit/536.3 "
        "(KHTML, like Gecko) Chrome/19.0.1061.0 Safari/536.3",
        "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/535.24 "
        "(KHTML, like Gecko) Chrome/19.0.1055.1 Safari/535.24",
        "Mozilla/5.0 (Windows NT 6.2; WOW64) AppleWebKit/535.24 "
        "(KHTML, like Gecko) Chrome/19.0.1055.1 Safari/535.24"
       ]

user_agent = random.choice(user_agent_list)


bigdf1=pd.DataFrame()
data=pd.DataFrame()
for i in df['s_phone7'].tolist():
    url = "http://www.ip138.com:8080/search.asp?mobile={0}&action=mobile".format(i)
#     headers={
#             "Accept":"text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8",
#             "Accept-Encoding":"gzip, deflate",
#             "Accept-Language":"zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2",
#             "Cache-Control":"max-age=0",
#             "Connection":"keep-alive",
#             "Cookie":"ASPSESSIONIDAARTDDRA=IDPILGIAMKNKGHMFHJPHFFJJ",
#             "Host":"www.ip138.com:8080",
#             "Referer":"http://www.ip138.com:8080/search.asp?mobile=1768117&action=mobile",
#             "Upgrade-Insecure-Requests":"1",
#             'User-Agent':'Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:62.0) Gecko/20100101 Firefox/47.0'
#             }
    headers={'User-Agent':user_agent}
    response=requests.get(url,headers=headers)
    response.encoding='gb2312'
    soup = BeautifulSoup(response.text,'html.parser')
    try:
        table_html = str(soup.find_all("table")[1])
        df_html=pd.read_html(table_html)[0]
        data.loc[0,'phone']=i
        data.loc[0,'add']=df_html.loc[2,1]
        bigdf1=pd.concat([bigdf1,data])
    except:
        data.loc[0,'phone']=i
        data.loc[0,'add']=np.nan
        bigdf1=pd.concat([bigdf1,data])
    time.sleep(random.random())
    j+=1
    print(j)
print(bigdf1)



```

## 时间处理模块
###### Create by:along

```
# #1.时间戳(timestamp)的方式：通常来说，时间戳表示的是从1970年1月１日开始按秒计算的偏移量(time.gmtime(0))此模块中的函数无法处理1970纪元年以前的时间或太遥远的未来(处理极限取决于C函数库，对于32位系统而言，是2038年)
# #2.UTC(Coordinated Universal Time,世界协调时) 也叫格林威治天文时间，是世界标准时间．在我国为UTC+8
# #3.DST(Daylight Saving Time) 即夏令时:
# 1986年至1991年,我国在全国范围实行了六年夏令时,每年从4月中旬的第一个星期日2时整(北京时间)到9月中旬第一个星期日的凌晨2时整(北京夏令时),调快一个小时
# #4.一些实时函数的计算精度可能不同
```

![时间格式转换]
(https://images2015.cnblogs.com/blog/996085/201610/996085-20161026171443546-488752980.png)

## time模块
```
import time
# gmtime(),localtime()和strptime()
print(time.gmtime())#UTC时区
print(time.localtime())#当地时区+8
print(time.strptime('2018-12-14 21:18:00','%Y-%m-%d %H:%M:%S'))
#time strptime() 函数根据指定的格式把一个时间字符串解析为时间元组,实际上它和strftime()是逆操作
```
```
time.strftime("%y-%m-%d %H:%M:%S",time.localtime())
#把一个代表时间的元组或者struct_time(如由time.localtime()和time.gmtime()返回)转化为格式化的时间字符串
```

```
print(time.mktime(time.localtime()))#接受时间元组并返回时间辍(1970纪元年后经过的浮点秒数)
print(time.time()) #返回当前时间的时间戳(1970元年后的浮点秒数)
```


```
t1 = time.time()
t2=t1+10

print (time.ctime(t1)) #传入时间戳
print (time.ctime(t2))

t3=time.localtime()
t4=time.localtime()

print(time.asctime(t3)) #传入时间元祖
print(time.asctime(t4))
```


## datetime模块

```
import datetime
import time

#datatime模块重新封装了time模块，提供更多接口，提供的类有：date,time,datetime,timedelta,tzinfo

datetime.datetime.fromtimestamp(time.time()).strftime('%Y-%m-%d %H:%M:%S')

```

```
#### date类

datetime.date(year, month, day)

date.max、date.min：date对象所能表示的最大、最小日期；
date.resolution：date对象表示日期的最小单位。这里是天。
date.today()：返回一个表示当前本地日期的date对象；
date.fromtimestamp(timestamp)：根据给定的时间戳，返回一个date对象；

print(date.max)
print(date.min)
print(date.resolution)
print(date.fromtimestamp(time.time()))
```

```
#### time类

# datetime.time(hour[, minute[, second[, microsecond[, tzinfo]]]])
from  datetime  import  *
print(time.max)
print(time.min)
print(time.resolution)



tm=time(11,19,30,3)
print(tm.hour)
print(tm.replace(11,20))
print(tm.isoformat())
print(tm.strftime("%H:%M"))
```

```
#### datetime类


#datetime.datetime (year, month, day[ , hour[ , minute[ , second[ , microsecond[ , tzinfo] ] ] ] ] )
import time

print(datetime.today()) #返回一个表示当前本地时间的datetime对象；如果datetime.now(tz.None) 两者相似
print(datetime.now(tz=timezone.utc)) #返回一个表示当前本地时间的datetime对象，如果提供了参数tz，则获取tz参数所指时区的本地时间；
print(datetime.utcnow()) #返回一个当前utc时间的datetime对象；#格林威治时间
print(datetime.fromtimestamp(time.time(),tz=timezone.utc)) #根据时间戳创建一个datetime对象，参数tz指定时区信息；
print(datetime.utcfromtimestamp(time.time())) #根据时间戳创建一个datetime对象；
print(datetime.strptime("2018-12-14 21:18:00","%Y-%m-%d %H:%M:%S")) #将格式字符串转换为datetime对象；
```

```
from  datetime  import  *
d=date(2012,9,12)
t=time(19,46,5)
print(datetime.combine(d,t)) #根据date和time，创建一个datetime对象；
```





```
import  time
datetime.fromtimestamp(time.time())

#### timedelta类，时间加减

dt = datetime.now()
#日期减一天
dt1 = dt + timedelta(days=-1)#昨天
dt2 = dt - timedelta(days=1)#昨天
dt3 = dt + timedelta(days=1)#明天
delta_obj = dt3-dt
print (type(delta_obj))

print(delta_obj.days)
print(delta_obj.total_seconds())


## dateutil 月份加减
## 另一个模块完成月份转换
from datetime import datetime
from dateutil.relativedelta import relativedelta

date_after_month = datetime.today()+ relativedelta(months=1)
date_after_month.strftime('%Y-%m-%d')

```


## dataframe 时间转化

```
import numpy as np
import pandas as pd
dates = ['2017-06-20','2017-06-21',
  '2017-06-22','2017-06-23','2017-06-24','2017-06-25','2017-06-26','2017-06-27']

ts = pd.DataFrame(np.random.randn(8),index=pd.to_datetime(dates))
ts.reset_index(inplace=True)
ts.dtypes
ts['index']=ts['index'].astype('str')

```
```

ts['date']=pd.to_datetime(ts['index'])

ts['date1']=ts['index'].apply(lambda x:datetime.datetime.strptime(x,'%Y-%m-%d'))

ts['str']=ts['date'].dt.strftime('%Y-%m-%d')
print(ts)
ts.dtypes
```

## 其他
```
struct_time元组元素结构

属性                            值
tm_year（年）                  比如2011
tm_mon（月）                   1 - 12
tm_mday（日）                  1 - 31
tm_hour（时）                  0 - 23
tm_min（分）                   0 - 59
tm_sec（秒）                   0 - 61
tm_wday（weekday）             0 - 6（0表示周日）
tm_yday（一年中的第几天）        1 - 366
tm_isdst（是否是夏令时）        默认为-1





format time结构化表示
格式 	含义
%a 	本地（locale）简化星期名称
%A 	本地完整星期名称
%b 	本地简化月份名称
%B 	本地完整月份名称
%c 	本地相应的日期和时间表示
%d 	一个月中的第几天（01 - 31）
%H 	一天中的第几个小时（24小时制，00 - 23）
%I 	第几个小时（12小时制，01 - 12）
%j 	一年中的第几天（001 - 366）
%m 	月份（01 - 12）
%M 	分钟数（00 - 59）
%p 	本地am或者pm的相应符
%S 	秒（01 - 61）
%U 	一年中的星期数。（00 - 53星期天是一个星期的开始。）第一个星期天之前的所有天数都放在第0周。
%w 	一个星期中的第几天（0 - 6，0是星期天）
%W 	和%U基本相同，不同的是%W以星期一为一个星期的开始。
%x 	本地相应日期
%X 	本地相应时间
%y 	去掉世纪的年份（00 - 99）
%Y 	完整的年份
%Z 	时区的名字（如果不存在为空字符）
%% 	‘%’字符
```

