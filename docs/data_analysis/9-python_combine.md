created by: 吉莹莹
update time: 2019-01-31

```python

import pandas as pd
import numpy as np

# 建一个临时dataframe
df = pd.DataFrame(columns=['id', 'col_1', 'col_2'],
                   data=[[1, 'a', None], [2, None, 'b'], [3, 'c', 'd']])

目标: col_2为空的,则由col_1替换,不为空的,不做改变
```

####  方法1--for循环判断
```python
for i in range(len(df.id)):
    if df.loc[i, 'col_2'] is np.nan:
        df.loc[i, 'col_2'] = df.loc[i, 'col_1']

print(df)
```

####  方法2--定义函数判断
```python
def update_department(x):
    if x['col_2'] is np.nan:
        return x['col_1']
    else:
        return x['col_2']

df['col_2'] = df.apply(lambda x:update_department(x),axis=1)

print(df)
```

####  方法3
```python
x = df[df.col_2.isnull()].col_1
y = df.loc[df.index.isin(x.index)]
y['col_2'] = y['col_1']
z = df[df.col_2.notnull()]
df_new = pd.concat([y, z])

print(df_new)
```

####  方法4--where/mask
```python
## 方法4.1
df.fillna(0, inplace=True)
df.col_2.where(df.col_2 != 0, other=df.col_1, inplace=True)
### 当满足条件为TRUE时，返回本身，否则返回other

## 方法4.2
df.col_2.where(~(df.col_2.isna()), df.col_1, inplace=True)

## 方法4.3
### np.where(condition, x, y),满足条件(condition)，输出x，不满足输出y
df.col_2 = np.where(~df.col_2.isna(), df.col_2, df.col_1)

## 方法4.4
df.col_2.mask(df.col_2.isna(), df.col_1, inplace=True)  # mask为where的相反

print(df)

# nan为数值类型,None为字符串类型
```
####  方法5--combine_first
```python
#合并重叠数据,条件是2个数据集的索引相同
#用一个数据填充另一个数据的缺失数据
df_1=df.col_1
df_2=df.col_2
df_2=df_2.combine_first(df_1)

print(df_2)
```
####  方法6--loc
```python
df.loc[df['col_2'].isnull(),'col_2'] = df['col_1']
```