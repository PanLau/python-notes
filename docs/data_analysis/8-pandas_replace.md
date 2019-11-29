created by: 吉莹莹
update date: 2019-02-27

##### Dataframe
![](/uploads/201902/analyst/attach_15872ac6e649c448.png)
#### 一、Dataframe.replace
##### df整体替换
```python
df.replace('南岸','城市')

# 替换指定的某个或指定的多个数值（用字典的形式）
df.replace({'ab29.52de':'B','中国':'china','城市':'city'})

df.replace(['A','中国','城市'],['B','china','city'])

# 替换一样的值
df.replace(['A','中国','城市'],'new')
```
##### df部分替换
```python
df.replace('[A-Z]','哈哈哈',regex = True)  # regex defalut false

df.replace('-|[A-Z]','***',regex = True)
```
#### 二、Dataframe.Series.replace
##### series整体替换
```python
df['表1lon'].replace(106.55,'a')
```
##### series部分替换
```python
df['表1变电站'].replace('-','&',regex = True) # regex defalut false
```

#### 三、Dataframe.Series.str.replace
##### series部分替换
```python
df['表1变电站'] = df['表1变电站'].str.replace('A','w') # regex defalut true
```