###### Create by:along
###  SQL查询规范
##### -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --数据增长部-数据分析组内部参考资料 -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --


#### 规范文档：

1.第一行为注释，内容：本条SQL查询项目，开始为4个#号

2.第二行为注释，本次查询时间和shape(小于1分钟的都可以不用具体填写)，执行环境，开始为4个#号

3.第三行注释，需求方，开始为4个#号

4.第四行行注释，完成人，开始为4个#号

5.第五行行注释，更新人，开始为4个#号

6.第六行注释，审核人，开始为4个#号

7.缩进标准为4个空格或者一个Tab键：
- 关键词不进行缩进，如：select，from，where，group by，having count，order by，limit等，
- 非关键字进行缩进，如：字段，表明，条件等，

8.SQL内部注释，开始为2个#号：
- 单行语句注释写到该行末尾，如下例中`老师工龄` 字段
- 多行语句注释写到该查询前面一行，如下例中`老师排课间隔时长(天)`，`老师上过的试听课成单数`字段


####  实例参考：
```sql
#### 主题：试听课老师推荐项目
#### 执行时间26.25s，shape:(17114,10)，新脱敏库
#### 需求方：xxx
#### created by:Will
#### update by:
#### checked by:

select
    tch.teacher_id teacherId,tch.quarters_type teacherJobType,tch.real_new_rate new_new_rate,ui.sex teacherSex,
    year(CURRENT_DATE())-year(tch.birth) new_teacherAge, ## 老师工龄
    (DATEDIFF(CURRENT_DATE(),IFNULL(tch.new_teacher_min_time,ui.reg_date))+1) new_new_teached_age, ## 老师工龄
    ## 老师排课间隔时长(天)
    round(timestampdiff(MINUTE,(select max(adjust_start_time) from lesson_plan
    where teacher_id=lp.teacher_id and lesson_type in (1,2) and status in(3,5)
        and solve_status<>6),curdate())/60,3) new_teacherPlanCourseInterval,
    count(distinct lesson_plan_id) teachedTrialCourseCount, ## 老师上过的试听课总数
    ## 老师上过的试听课成单数
    (select count(distinct student_id)
	from
		student
	where student_id =s.student_id and student_intention_id in (select student_intention_id from tms_contract
		where effective_date is not null)) teachedTrialSuccessStudentCount,
from
	teacher tch
left join user_info ui on tch.teacher_id=ui.user_id
left join lesson_plan lp on lp.teacher_id =tch.teacher_id
left join student s on lp.student_id=s.student_id
where lp.lesson_type=2 and  lp.status in(3,5) and  lp.solve_status<>6 and lp.adjust_start_time < CURRENT_DATE()
group by tch.teacher_id
```