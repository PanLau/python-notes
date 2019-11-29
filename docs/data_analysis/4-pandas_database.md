# pandas 

## pandas 读取各类sql数据源

### 1.读写mysql数据

1.首先安装python连接mysql的驱动，以mysql.connector为例
2.pandas读取数据，分两种方式：

#### DBAPI2 connection方式连接
```
import mysql.connector
import pandas as pd

conn=mysql.connector.connect(host='127.0.0.1',user='root', passwd='password', db='test')
sql='select * from mytable'
df=pd.read_sql(sql,conn)
```
#### SQLAlchemy engine方式
```
from sqlalchemy import create_engine
import pandas as pd
engine = create_engine('mysql+mysqlconnector://root:password@10.39.211.198:3306/test')
df=pd.read_sql(sql,engine )
```
#### pandas写入数据到mysql只有SQLAlchemy engine方式


---
### 2.hive 


---
### 3.impala 
#### 安装出现的问题
> 参考链接：https://www.twblogs.net/a/5c713ad1bd9eee68440f609b/zh-cn
* 'TSocket' object has no attribute 'isOpen bug: https://github.com/cloudera/impyla/issues/268

* 'TSaslClientTransport' object has no attribute 'readAll': https://github.com/dropbox/PyHive/issues/151

* 解决方案:
> https://github.com/dropbox/PyHive/commit/5322d8f1420b033ba7446449b5cca2cbf9f6fbc4

* pip3 install git+https://github.com/cloudera/thrift_sasl

#### 同时使用impala和pyHive请注意import顺序
```
from pyhive import hive
from impala.dbapi import connect
```

#### python library 版本:
```
thrift                    0.11.0
thrift-sasl            0.3.0 (使用非release版本, 而是用上面的URL来安装)
thriftpy                0.3.9
PyHive                0.6.1
impyla                 0.14.2.2
```

#### 连接语法
```
from impala.dbapi import connect
from sqlalchemy import create_engine
import pandas as pd
conn = connect(host='127.0.0.1',port=21050,database='db')
engine=create_engine('impala://127.0.0.1:21050/db')
# engine = create_engine('mysql+mysqlconnector://root:password@10.39.211.198:3306/test')
df.to_sql('mytable',con=engine,if_exists='append',index=False)
```

#### 要注意impala的数据类型，下面给一个我在实际项目中解决的例子：
* 一定要加后面的 dtype={'address': String}
```
from sqlalchemy.sql.sqltypes import String
df.to_sql('address',con=sqlconn,if_exists='append',index=False,dtype={'address': String})
```

