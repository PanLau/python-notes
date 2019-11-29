# MySQL-高级
## 1.视图
    create view v_stu_sub_sco as
    select stu.*,sco.score,sub.title from scores as sco
    inner join students as stu on sco.sub_id=stu.id
    inner join subjects as sub on sco.sub_id=sub.id;

## 2.事物
    事物四大特性（简称ACID）：原子性；一致性；隔离性；持久性

    使用事物的情况：当数据被更改时，包括insert、update、delete
    
    begin;
    commit;
    rollback;回滚

## 3.索引
    选择列的数据类型：
        越小的数据类型通常更好
        简单的数据类型
        尽量避免NULL

    分类：单列索引和组合索引
    查看索引；
    创建索引；
    删除索引
    grant all privileges on jing_dong.* to "laoli"@"%" identified by "12345678”;
http://pypi.python.org/pypi