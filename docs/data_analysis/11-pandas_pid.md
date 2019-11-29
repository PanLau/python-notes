```
####昨日更新字段,查询昨日数据
select
            Id,
            home_work_id,
            quiz_id,
            type,
            status,
            check_status,
            mark_status,
            knowledge_id,
            rel_quiz_id,
            knowledge_name,
            quiz_source,
            difficult_factor,
            quiz_type,
            quizNo
from home_work_detail_info
where mylifearebeautiful >= date_sub(curdate(),interval 1 day) and mylifearebeautiful < now();
```

```
# -*- coding: utf-8 -*-
# @Time    : 2018/12/06 21:07
# @Author  : Janey
# @File    : type_clean_daily.py
# @Software: PyCharm


import sys

sys.path.append('../../../../config')

import os

file_path = os.path.dirname(os.path.realpath(__file__))  # 获取项目根目录
file_basename = os.path.basename(__file__)
# syspath = file_path.split("\iProject")

from utils import mysqlhelper, elapse, try_rerun
from config import config
from ML import data_suit
mysqlinstance_tm_new_hfjy = mysqlhelper(**config.tm_new_hfjy)
mysqlinstance_tm_new_bidata = mysqlhelper(**config.tm_new_bidata)

sql1 = '''select
                home_work_id,
                Id,
                quiz_id,
                type
    from batch_home_work_detail_info '''

sqlx = '''update batch_home_work_detail_info set quiz_id=%s, type=%s where quiz_id={0} '''

# 获取id的所有父节点,递归找到type<>2的quiz_id 和type
def get_pid_list(df, id):
    tmp_df = df[df['Id'] == id]
    if tmp_df.empty or tmp_df['type'].values[0] != 2:
        return tmp_df[tmp_df['type'] != 2][['quiz_id', 'type']]
    else:
        return get_pid_list(df, tmp_df['quiz_id'].values[0])

# 找到父节点不为空时进行update
@try_rerun(dingding=True, des="type=2", subject="alarm")
def data_update(df, sql):
    mysqlinstance_tm_new_bidata.updatemany_bydf(df, sql)


def main():

    ####查询昨日更新字段
    df = mysqlinstance_tm_new_hfjy.get_df_loadfile(file_path + './sql/昨日更新字段.sql')

    ####找出更新过信息和新增的Id删除
    stu_id_s = df['Id']
    mysqlinstance_tm_new_bidata.excute(
        "delete from batch_home_work_detail_info where Id in {0}".format(tuple(stu_id_s)))
    # #
    # # ####将前面的df插入数据库
    mysqlinstance_tm_new_bidata.insertmany_bydf(df, tb="batch_home_work_detail_info")

    #全量读取数据做递归用
    df1 = mysqlinstance_tm_new_bidata.get_df(sql1)

    dfx = df1.copy()

    ####取出昨日更新的type=2的数据进行清洗
    df2 = df[(df['type'] == 2) & (~df['quiz_id'].isnull())]
    print(df2.shape)
    for Id in df2['Id']:
        dfy = get_pid_list(dfx, df2[df2['Id'] == Id]['quiz_id'].values[0])
        if dfy.empty:
            pass
        else:
            data_update(dfy, sqlx.format(df2[df2['Id'] == Id]['quiz_id'].values[0]))

if __name__ == '__main__':
    main()
```


### 递归求子节点对应所有父节点


```python
import pandas as pd
df = pd.DataFrame({'chi_id': ['a', 'a1', 'a2', 'a11', 'a111', 'a12'],
                   'par_id': [None, 'a', 'a', 'a1', 'a11', 'a']})
df
```
![](/uploads/201812/analyst/attach_1573da9b0fea51db.png)

>>需求:找到所有chi_id 对应所有par_id (例如chi_id='a11' 时,对应的par_id为 ['a1', 'a'])


```python
# 递归查找父节点,依次将其添加到对应列表中
def get_parent(df,id):
    tmp_df = df[df['chi_id'] == id]
    if tmp_df.empty or tmp_df['par_id'].values[0] is None:
        return pd.DataFrame(columns=['chi_id', 'par_id'])
    else:
        return get_parent(df,tmp_df['par_id'].values[0]).append(tmp_df)

# 获取对应的id
id_list = df['chi_id'].values

# 循环取对应的所有父节点
for i in id_list:
    par_list = get_parent(df,i)['par_id'].values.tolist()
    print(i,par_list)
```

![](/uploads/201812/analyst/attach_1573dab3e8beb62d.png)