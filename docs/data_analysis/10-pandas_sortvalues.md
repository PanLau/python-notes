by----wj


```sql
#### 建表
DROP TABLE IF EXISTS `test_stu`;
CREATE TABLE `test_stu` (
	`id` int(11) DEFAULT NULL COMMENT 'id',
	`class` varchar(255) DEFAULT NULL COMMENT '年级',
	`score` int(11) DEFAULT NULL COMMENT '分数',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

#### 插入数据
insert into test_stu(id,class,score)
values('1','一年级',82),
('2','一年级',95),
('3','一年级',82),
('4','一年级',40),
('5','一年级',20),
('6','二年级',95),
('7','二年级',40),
('8','二年级',3),
('9','二年级',60),
('10','二年级',10),
('11','三年级',70),
('12','三年级',60),
('13','三年级',40),
('14','三年级',90),
('15','三年级',20);
```

**需求：求每个年级的前三名**

**实现方法：**
**1.mysql**


```
#### 方法1：
select
		a.id,
		a.class,
		a.score
from test_stu a
where (select count(*) from test_stu where class=a.class and a.score<score)<3
order by a.class,a.score desc;
```
结果：

id | class | score
---|---|---
2 | 一年级| 95
1 | 一年级| 82
3 | 一年级| 82
14 | 三年级| 90
11 | 三年级| 70
12 | 三年级| 60
6 | 二年级| 95
9 | 二年级| 60
7 | 二年级| 10


```
#### 方法2：
select *
from
(select
		temp.*,
		if(@new_class = temp.class,@rank:= @rank+1,@rank:=1) as rank,
		@new_class:=temp.class as new_class
from
		(select *
		from test_stu
		order by class asc,score DESC
		)temp,(select @new_class := null,@rank :=0)t
)a
where a.rank<=3;
```
结果：

id | class | score|rank
---|---|---|---
2 | 一年级| 95|1
1 | 一年级| 82|2
3 | 一年级| 82|3
14 | 三年级| 90|1
11 | 三年级| 70|2
12 | 三年级| 60|3
6 | 二年级| 95|1
9 | 二年级| 60|2
7 | 二年级| 10|3



**2.python**


```python
#### 方法1
### sort_values 方法
df.assign(rn=df.sort_values(['score'],ascending=False).groupby(['class']).cumcount() + 1).query('rn <= 3').sort_values(['class','score'],ascending=False)

####同下
df[df.sort_values(['score'],ascending=False).groupby(['class']).cumcount() + 1 <= 3].sort_values(['class','score'],ascending=False)


```

```
#### 方法2
###rank 方法
df.assign(rnk = df.groupby(['class'])['score'].rank(method = 'first', ascending = False)).query('rnk <= 3').sort_values(['class','rnk'])

####同下
df[df.groupby(['class'])['score'].rank(method = 'first', ascending = False) <= 3].sort_values(['class','score'],ascending=False)

```

```
#### 方法3
####吉祥方法
def top(x,column="avg_take_time",n=50):
    return x.sort_values(by=column,ascending=False)[:n]

df.groupby(["class"],group_keys=False).apply(top,column="score",n=3)

```

```
df.groupby(["class"])['score'].nlargest(3)
df.groupby(["class"])apply(x:x.nlargest(3,'score'))
df.sort_values(['score'],ascending=False).groupby(['class']).head(3)

```






插播：

```
####查询自增列断掉的前一个序号
select id from test_group a where not exists (select 1 from test_group where id=a.id+1)

```