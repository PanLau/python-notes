### presto遇到的坑
1.中文使用　双引号　""
    eg:活跃师生组队-周.sql
2.时间处理
    时间 2019-02-14 15:35:00 varchar 需转换为 timestamp
    eg:活跃师生组队-周.sql cast(substr(lp.adjust_start_time,1,19) as timestamp)

3.groupby 聚合字段要包含select字段，可将原本去除需要 groupby 字段的改为max(字段)
    eg:活跃师生组队-周.sql
    
4.COALESCE:All COALESCE operands must be the same type
select COALESCE('中文', null);
select COALESCE('sdf', null);

select date_format(cast('2018-12-31' as TIMESTAMP), '%x-%v'),date_format(cast('2018-12-31' as TIMESTAMP) - interval '7' day, '%x-%v'),cast('2018-12-31' as TIMESTAMP) - interval '7' day
select date_format(cast('2019-01-01' as TIMESTAMP), '%x-%v'),date_format(cast('2019-01-01' as TIMESTAMP) - interval '7' day, '%x-%v'),cast('2019-01-01' as TIMESTAMP) - interval '7' day