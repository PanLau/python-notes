###### Create by:along

## 日期参数
%S, %s 两位数字形式的秒（ 00,01, . . ., 59）
%i 两位数字形式的分（ 00,01, . . ., 59）
%H 两位数字形式的小时，24 小时（00,01, . . ., 23）
%h, %I 两位数字形式的小时，12 小时（01,02, . . ., 12）
%k 数字形式的小时，24 小时（0,1, . . ., 23）
%l 数字形式的小时，12 小时（1, 2, . . ., 12）
%T 24 小时的时间形式（hh : mm : s s）
%r 12 小时的时间形式（hh:mm:ss AM 或hh:mm:ss PM）
%p AM 或P M
%W 一周中每一天的名称（ Sunday, Monday, . . ., Saturday）
%a 一周中每一天名称的缩写（ Sun, Mon, . . ., Sat）
%d 两位数字表示月中的天数（ 00, 01, . . ., 31）
%e 数字形式表示月中的天数（ 1, 2， . . ., 31）
%D 英文后缀表示月中的天数（ 1st, 2nd, 3rd, . . .）
%w 以数字形式表示周中的天数（ 0 = Sunday, 1=Monday, . . ., 6=Saturday）
%j 以三位数字表示年中的天数（ 001, 002, . . ., 366）
% U 周（0, 1, 52），其中Sunday 为周中的第一天
%u 周（0, 1, 52），其中Monday 为周中的第一天
%M 月名（January, February, . . ., December）
%b 缩写的月名（ January, February, . . ., December）
%m 两位数字表示的月份（ 01, 02, . . ., 12）
%c 数字表示的月份（ 1, 2, . . ., 12）
%Y 四位数字表示的年份
%y 两位数字表示的年份
%% 直接值“%”


## 时间函数
**MySQL 获得当前日期时间 函数**

获得当前日期+时间（date + time）
函数：now()/sysdate()


```
select now();
select sysdate();
```

sysdate() 日期时间函数跟 now() 类似，不同之处在于：now() 在执行开始时值就得到了，sysdate() 在函数执行时动态得到值。



curtime()：返回当前时间，只包含时分秒
curdate()：返回当前日期，只包含年月日
```
select curtime();
select curdate();

```

**MySQL 日期转换函数、时间转换函数**

date_format(date,format) :以不同的格式显示日期/时间数据。
```
select adjust_start_time from lesson_plan
where DATE_FORMAT(adjust_start_time, '%Y-%m') = '2018-07'

select date_format(curdate(),'%Y')
select date_format(curdate(),'%m')
select date_format(curdate(),'%d')
select date_format(curdate(),'%w')
```
（日期、天数）转换函数：to_days(date)
```
select to_days('0000-00-00'); -- 0
select to_days('2008-08-08'); -- 733627
```
（时间、秒）转换函数：time_to_sec(time), sec_to_time(seconds)
```
select time_to_sec('01:00:05'); -- 3605
select sec_to_time(3605); -- '01:00:05'
```



**MySQL 日期时间计算函数**

为日期增加一个时间间隔：date_add()

```
select date_add(now(), interval 1 day); -- add 1 day
select date_add(now(), interval 1 hour); -- add 1 hour
select date_add(now(), interval 1 minute); -- ...
select date_add(now(), interval 1 second);
select date_add(now(), interval 1 microsecond);
select date_add(now(), interval 1 week);
select date_add(now(), interval 1 month);
select date_add(now(), interval 1 quarter);
select date_add(now(), interval 1 year);

select date_add(now(), interval -1 day); -- sub 1 day
```
为日期减去一个时间间隔：date_sub()

```
select date_sub(now(), interval 1 day);
select date_sub('2018-10-01 12:20:22', interval '1 1:20:22' day_second)
```
日期、时间相减函数：datediff(date1,date2), timediff(time1,time2)

```
MySQL datediff(date1,date2)：两个日期相减 date1 - date2，返回天数。
select datediff('2018-10-07', '2018-10-01'); -- 7
select datediff('2018-10-01', '2018-10-07');
```
 timediff(time1,time2)：两个日期相减 time1 - time2，返回 time 差值。
```
select timediff('2018-08-08 08:08:08', '2018-08-08 00:00:00'); -- 08:08:08
select timediff('08:08:08', '00:00:00'); -- 08:08:08
```
注意：timediff(time1,time2) 函数的两个参数类型必须相同。

 时间戳（timestamp）转换、增、减函数：

```
timestamp(date) -- date to timestamp
timestamp(dt,time) -- dt + time
timestampadd(unit,interval,datetime_expr) --
timestampdiff(unit,datetime_expr1,datetime_expr2) --
```

```
select timestamp('2018-10-01');
select timestamp('2018-10-01 08:00:00', '01:01:01');
select timestamp('2018-10-01 08:00:00', '10 01:01:01');

select timestampdiff(year,'2018-10-01','2019-10-01'); -- 1
select timestampdiff(year,'2018-10-01','2019-09-01'); -- 0

select timestampdiff(day ,'2018-10-01','2018-10-07'); -- 6
select timestampdiff(hour,'2018-10-01 00:00:00','2018-10-01 12:00:00'); -- 12
```
返回日期或日期时间表达式datetime_expr1 和datetime_expr2the 之间的整数差。其结果的单位由interval 参数给出。该参数必须是以下值的其中一个


```
frac_second 表示间隔是毫秒
second 秒
minute 分钟
hour 小时
day 天
week 星期
month 月
quarter 季度
year 年

```


个性化显示时间日期--分别返回日期在一周、一月、一年中是第几天

dayofweek(date):返回日期date的星期索引(1=星期天，2=星期一, ……7=星期六)。

weekday(date):返回date的星期索引(0=星期一，1=星期二, ……6= 星期天)。

```
select now(),dayofweek(now());
select now(),weekday(now());
```

　　dayofmonth(date)

　　dayofyear(date)
　　
```
-- 个性化 分别返回日期在一周、一月、一年中是第几天
select now(),dayofmonth(now());
select now(),dayofyear(now());
```

**year_month:选取年月**
```
select extract(year_month from now())
```

## 字符串操作顺序
1.LOWER(column|str)/UPPER(column|str)

将字符串参数值转换为全小/全大写字母后返回

```
select lower('ABC'),upper('abc')
```
2.concat

合并多个字符串，或者表中的多个字段

```
select concat('a','b','c'),concat('a',1,'c')
```

3. CONCAT_WS(separator,str1,str2,...)

将多个字符串参数以给定的分隔符separator首尾相连后返回


```
select concat_ws(';','a','b','c'),concat_ws('-','a','b','c'),
concat_ws('-','a',null,'c');
```
4. FORMAT(X,D[,locale])：以格式‘#,###,###.##’格式化数字X ,D指定小数位数,　locale指定国家语言(默认的locale为en_US)


```
 select format(12332.123456, 4),format(12332.2,0),format(12332.2,2,'de_DE');
```

5.STRCMP(expr1,expr2)：如果两个字符串是一样的则返回0；如果第一个小于第二个则返回-1；否则返回1



```
select strcmp('text','text'),strcmp('text', 'text2'),strcmp('text2', 'text');
```

6.LENGTH(str)/CHAR_LENGTH(str)

返回字符串的存储长度

```
 select length('text'),length(12345),length('哎呀') ##一个汉字三个字符
 union
 select char_length('text'),char_length(12345),char_length('哎呀') ##一个汉字一个字符
```


7.LPAD(str, len, padstr)/RPAD(str, len, padstr)

在源字符串的左/右边填充给定的字符padstr到指定的长度len，返回填充后的字符串

```
select lpad('hi',5,'*'),rpad('hi',6,'*'), lpad('hi',5,'*12345'), lpad('hi',5,'')
```

8.TRIM([{BOTH | LEADING | TRAILING} [remstr] FROM] str), TRIM([remstr FROM] str)

 如果不指定remstr，则去掉str两端的空格；不指定BOTH、LEADING、TRAILING ，则默认为 BOTH。

```
select trim('  bar  ');

select trim('x' from 'xbarx');

select trim('x' from ' xbarx ');

select trim(leading 'x' from 'xxxbarxxx');
　　
select trim(both 'x' from 'xxxbarxxx');

select trim(trailing 'xyz' from 'barxxyz');

select trim(trailing 'xyz' from 'barxyxyzxyz');
```



9.LTRIM(str)，RTRIM(str)

去掉字符串的左边或右边的空格(左对齐、右对齐)

```
select  ltrim('   barbar   ') rs1, rtrim('   barbar   ') rs2;
```


10.REPLACE(str, from_str, to_str)

在源字符串str中查找所有的子串form_str（大小写敏感），找到后使用替代字符串to_str替换它。返回替换后的字符串

```
select replace('mysql','m','W');
```

11.INSERT(str,pos,len,newstr)

返回字符串str，在位置pos起始的子串且len个字符长得子串由字符串newstr代替

```
select insert('tonghua',1,2,'ing')
```

12.REPEAT(str, count)：将字符串str重复count次后返回
```
select repeat('mySQL  ',3);
```
13.REVERSE(str)：将字符串str反转后返回

```
select reverse('abcdef')
```

14.LEFT(str, len)/RIGHT(str, len)：返回最左/右边的len长度的子串

```

select left('chinaitsoft',5),right('chinaitsoft',5)
```
15.SUBSTR(str,pos[,len])：

从源字符串str中的指定位置pos开始取一个字串并返回

①len指定子串的长度，如果省略则一直取到字符串的末尾；len为负值表示从源字符串的尾部开始取起。

②函数SUBSTR()是函数SUBSTRING()的同义词。

```
select substring('hello world',5),substr('hello world',5,3),substr('hello world',-5)
```



16.MID(str,pos,len):从字符串str返回一个len个字符的子串，从位置pos开始。

```
select mid('hobby', 1, 6)
```



17.按关键字截取字符串 :substring_index（被截取字段，关键字，关键字出现的次数）

```
select substring_index('Harry Potter','t',1)

select substring_index('Harry Potter','t',-1)
```

18.位置控制函数:INSTR(str,substr) ;LOCATE(str,substr); POSITION(str IN substr)

返回子串substr在字符串str中的第一个出现的位置。这与有2个参数形式的LOCATE()相同，除了参数被颠倒。

```
select instr('harry','r'),locate('r','harry'), locate('y','harry',1),position('a' in 'harry')
```

19.FIELD():返回某字符串在字符串List的位置，如果字符串List未找到指定的字符串，则返回0.

若FIELD()中所有的参数都是字符串型，则将以字符串类型作为比较；如果所有的参数都是数字型，则将以数字类型作为比较；否则，以double类型进行比较。

如果字符串为NULL，则返回的值为0，因为NULL和所有值比较都不相等。


```
select field('ej','hej','ej','heja','foo'),field('aa','hej','ej','heja','foo'),
field(0,10,0,1),field(2,'aa',1,2,'c'),field('c','aa',1,2,'c'),field(null,'aa',1,2,'c')
```

20.ELT(N,str1,str2,str3,…):返回一个字符串List的第N个元素，如果N=1，返回str1；如果N=2，返回str2.

如果N小于1或大于字符串元素的个数，则返回NULL.


```
select elt(1,'a','c'),elt(-1,'a','c'),elt(3,'a','c')
```

