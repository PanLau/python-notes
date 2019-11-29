```
###substr_index()
select  element_at(split('www,hh,aaa', ','),-1)
```


```
###### array_join(array_agg(distinct os_name), ',') ----类似于mysql中的group_concat()
 select
            lesson_plan_id,
            array_join(array_agg(distinct os_name), ',') os_name
        from ods.ls_learning_room_event
        where dt>='2019-05-06'
        and user_type =0
        group by lesson_plan_id
        limit 10
```

```
@bdap 2017-07-31 16:57 字数 38178 阅读 8117
Presto 帮助
D-query

Presto官方文档：https://prestodb.io/docs/current/index.html

Presto SQL示例
游戏日登录人数
select
  date as "日期",
  sum(login_cnt) as "登录人数"
from
  data_analyze_topresto.dm_gamelog_account_ds
where
  gameid = 94
  and cast(group_type as integer)= 99
  and cast(player_type as integer) = 99
  and ds between '20170326'
  and '20170427'
group by
  date
项目日报
    SELECT
    ds1."日期_date" as "日期",
    ds1."登录人数",
    ds2."新用户数",
    ds1."充值金额",
    ds2."新用户充值",
    ds1."充值用户数",
    ds1."充值次数",
    ds2."新用户付费人数",
    ds1."付费率" as "付费率",
    ds2."新用户付费率" as "新用户付费率",
    ds1.ARPPU,
    ds1.ARPU,
    COALESCE (re."次留", '') "次留",
    COALESCE (re."三留", '') "三留",
    COALESCE (cast(re."首日ltv" AS VARCHAR),'') "首日ltv",
    COALESCE (cast(re."3日ltv" AS VARCHAR), '') "3日ltv"
FROM
    (
        SELECT
            ds.date "日期_date",
            round(sum(ds.excharge_money), 0) "充值金额",
            round(sum(ds.login_cnt), 0) AS "登录人数",
            round(sum(ds.excharge_cnt), 0) "充值用户数",
            round(sum(ds.excharge_num), 0) "充值次数",
            round(
                sum(ds.excharge_money) / sum(ds.login_cnt),
                2
            ) ARPU,
            round(
                sum(ds.excharge_money) / sum(ds.excharge_cnt),
                2
            ) ARPPU,
            cast(
                round(
                    sum(cast(ds.excharge_cnt AS DOUBLE)) / sum(cast(ds.login_cnt AS DOUBLE)),
                    4
                ) AS VARCHAR
            ) as "付费率"
        FROM
            dm_gamelog_role_ds ds
        INNER JOIN (
            SELECT
                server_id
            FROM
                server_id
            WHERE
                cast(type AS INTEGER) = 31
            AND game_id = 146
        ) t ON cast(ds.server_id AS BIGINT) = cast(t.server_id AS BIGINT)
        WHERE
            ds.gameid = 146
        AND cast(group_type AS INTEGER) = 99
        AND cast(player_type AS INTEGER) = 99
        AND ds.ds BETWEEN '20170327'
        AND '20170427'
        GROUP BY
            ds.date
    ) ds1
LEFT OUTER JOIN (
    SELECT
        date "日期",
        round(sum(excharge_money), 0) "新用户充值",
        round(sum(login_cnt), 0) AS "新用户数",
        round(sum(excharge_cnt), 0) "新用户付费人数",
        cast(
            round(
                sum(cast(excharge_cnt AS DOUBLE)) / sum(cast(login_cnt AS DOUBLE)),
                4
            ) AS VARCHAR
        ) as "新用户付费率"
    FROM
        dm_gamelog_role_ds ds
    INNER JOIN (
        SELECT
            server_id
        FROM
            server_id
        WHERE
            cast(type AS INTEGER) = 31
        AND game_id = 146
    ) t ON cast(ds.server_id AS BIGINT) = cast(t.server_id AS BIGINT)
    WHERE
        gameid = 146
    AND cast(group_type AS INTEGER) = 1
    AND cast(player_type AS INTEGER) = 0
    AND ds.ds BETWEEN  '20170327'
    AND '20170427'
    GROUP BY
        date
) ds2 ON ds1."日期_date" = ds2."日期"
LEFT OUTER JOIN (
    SELECT
        register_date,
        cast(
                round(
                    cast(
                        sum(
                            CASE
                            WHEN shift = 1 THEN
                                login_cnt
                            END
                        ) AS DOUBLE
                    ) / cast(
                        sum(
                            CASE
                            WHEN shift = 0 THEN
                                register_cnt
                            END
                        ) AS DOUBLE
                    ),
                    4
                ) AS VARCHAR
            )
        AS "次留",
        cast(
                round(
                    cast(
                        sum(
                            CASE
                            WHEN shift = 2 THEN
                                login_cnt
                            END
                        ) AS DOUBLE
                    ) / cast(
                        sum(
                            CASE
                            WHEN shift = 0 THEN
                                register_cnt
                            END
                        ) AS DOUBLE
                    ),
                    4
                ) AS VARCHAR
            )
        AS "三留",
        round(
            cast(
                sum(
                    CASE
                    WHEN shift = 0 THEN
                        all_excharge_money
                    END
                ) AS DOUBLE
            ) / cast(
                sum(
                    CASE
                    WHEN shift = 0 THEN
                        register_cnt
                    END
                ) AS DOUBLE
            ),
            2
        ) AS "首日ltv",
        round(
            sum(
                CASE
                WHEN shift = 2 THEN
                    all_excharge_money
                END
            ) / sum(
                CASE
                WHEN shift = 0 THEN
                    register_cnt
                END
            ),
            2
        ) AS "3日ltv"
    FROM
        dm_gamelog_role_retention_ltv_ds
    WHERE
        gameid = 146
    AND ds BETWEEN '20170327'
    AND '20170427'
    AND register_date BETWEEN '2017-03-26'
    AND '2017-04-27'
    AND (
        shift IN (0, 1, 2, 3, 4, 5, 6, 7)
        OR ds = '20170427'
    )
    GROUP BY
        register_date
) re ON ds1."日期_date" = re.register_date
ORDER BY
    "日期_date" DESC
Presto 常用函数
逻辑运算符
逻辑运算符
运算符	描述	示例
AND	True if both values are true	a AND b
OR	True if either value is true	a OR b
NOT	True if the value is false	NOT a
逻辑运算符中NULL的效果
如果AND表达式中有一边或者两边都是null，那么整个AND表达式的结果将会是null。
如果AND表达式中至少有一边的值是false，那么整个AND表达式的值都是false。

SELECT CAST(null AS boolean) AND true; => null
SELECT CAST(null AS boolean) AND false; => false
SELECT CAST(null AS boolean) AND CAST(null AS boolean); => null
如果OR表达式的一边或者两边都是null，那么整个OR表达式的值就是null。如果OR表达式中只要有一边的值为true，那么整个OR表达式的值就是true。

SELECT CAST(null AS boolean) OR CAST(null AS boolean); => null
SELECT CAST(null AS boolean) OR false; => null
SELECT CAST(null AS boolean) OR true; => true
AND和OR表达式中NULL的计算规则：
a	b	a AND b	a OR b
TRUE	FALSE	FALSE	TRUE
TRUE	NULL	NULL	TRUE
FALSE	TRUE	FALSE	TRUE
FALSE	FALSE	FALSE	FALSE
FALSE	NULL	FALSE	NULL
NULL	TRUE	NULL	TRUE
NULL	FALSE	FALSE	NULL
NULL	NULL	NULL	NULL
NULL的NOT表达式的结果还是NULL，如下所示：

SELECT NOT CAST(null AS boolean); => null
NOT表达式的计算规则：
a	NOT a
TRUE	FALSE
FALSE	TRUE
NULL	NULL
比较函数和运算符
比较运算符
运算符	描述
<	小于
>	大于
<=	小于等于
>=	大于等于
=	等于
<>	不等
!=	不等（不标准的用法，但是很流行这样使用）
范围运算符: BETWEEN
BETWEEN 运算符检测一个值是否在指定的范围。
使用语法 value BETWEEN min AND max:

SELECT 3 BETWEEN 2 AND 6;
上面的语句和下面的语句等效:

SELECT 3 >= 2 AND 3 <= 6;
要检测一个值不在指定的范围， 使用NOT BETWEEN:

SELECT 3 NOT BETWEEN 2 AND 6;
上面的语句和下面的语句等效:

SELECT 3 < 2 OR 3 > 6;
如果在BETWEEN或NOT BETWEEN语句中出现NULL， 将导致结果为NULL:

SELECT NULL BETWEEN 2 AND 4; => null
SELECT 2 BETWEEN NULL AND 6; => null
BETWEEN 和 NOT BETWEEN 运算符 也可以用于字符串参数:

SELECT 'Paul' BETWEEN 'John' AND 'Ringo'; => true
使用BETWEEN和NOT BETWEEN时， value、min和max参数的类型必须相同。 例如，如果你问John是否在2.3到35.2之间，Presto将会报错。

空和非空
IS NULL 和 IS NOT NULL 运算符检测一个值是否为空（未定义）。 这两个运算符适用于所有数据类型。
在IS NULL语句中使用NULL进行对比 ，结果为true:

select NULL IS NULL; => true
但使用其他常量进行对比，结果都是false:

SELECT 3.0 IS NULL; => false
IS DISTINCT FROM 和 IS NOT DISTINCT FROM
在SQL中 NULL表示一个未知的值， 因此，任何比较相关的语句含有NULL ，结果都是NULL。 而 IS DISTINCT FROM和IS NOT DISTINCT FROM 运算符将NULL视为一个已知的值， 这两个运算符保证即使输入中有NULL， 结果也是true或false。

SELECT NULL IS DISTINCT FROM NULL; => false
SELECT NULL IS NOT DISTINCT FROM NULL; => true
上述示例中， NULL值与NULL没有区别。 当你的比较操作中可能包含NULL 的值时， 使用这两个运算符可以保证结果只能是TRUE或 FALSE 。

下表格展示了NULL在 IS DISTINCT FROM和 IS NOT DISTINCT FROM 中是怎样计算的:

a	b	a = a	a <> b	a DISTINCT b	a NOT DISTINCT b
1	1	TRUE	FALSE	FALSE	TRUE
1	2	FALSE	TRUE	TRUE	FALSE
1	NULL	NULL	NULL	TRUE	FALSE
NULL	NULL	NULL	NULL	FALSE	TRUE
最大和最小
这两个函数不是SQL标准函数，他们是常用的扩展。 与Presto的其他数函数相似，任何一个参数为空，则返回空。 但是在某些其他数据库中，例如PostgreSQL， 只有全部参数都为空时，才返回空。

支持类型： DOUBLE, BIGINT, VARCHAR, TIMESTAMP, TIMESTAMP WITH TIME ZONE, DATE

greatest(value1, value2) → [与输入相同]
返回提供的最大值。

least(value1, value2) → [与输入相同]
返回提供的最小值。

比较量词： ALL, ANY and SOME
ALL, ANY and SOME等量词可以与比较运算符一起使用：

expression operator quantifier ( subquery )
例如：

SELECT 'hello' = ANY (VALUES 'hello', 'world'); -- true
SELECT 21 < ALL (VALUES 19, 20, 21); -- false
SELECT 42 >= SOME (SELECT 41 UNION ALL SELECT 42 UNION ALL SELECT 43); -- true
量词和比较运算符组合后的含义如下:

语句	含义
A = ALL (...)	A等于所有值时，结果为true
A <>ALL (...)	A和任意一个值都不相等，则结果为true
A < ALL (...)	A小于最小的值时，则结果为true
A = ANY (...)	当A和任意一个值相等时，结果为true，这个表达方式等价于A IN (...)
A <> ANY (...)	当A不等于其中一个或多个值时，结果为true
A < ANY (...)	A小于最大的值时，则结果为true
!=	不等（不标准的用法，但是很流行这样使用）
ANY和SOME含义相同，可以互换使用

条件表达式
CASE
标准的SQL CASE 表达式有两种模式。 “简单模式”从左向右查找表达式的每个 value ， 直到找出相等的 expression:

CASE expression
   WHEN value THEN result
   [ WHEN ... ]
   [ ELSE result ]
END
返回匹配 value 的 result 。 如果没有匹配到任何值，则返回 ELSE 子句的 result ； 如果没有 ELSE 子句，则返回空。
示例:

SELECT a,
   CASE a
       WHEN 1 THEN 'one'
       WHEN 2 THEN 'two'
       ELSE 'many'
   END
“查找模式”从左向右判断每个 condition的布尔值， 直到判断为真，返回匹配 result :

CASE
    WHEN condition THEN result
    [ WHEN ... ]
    [ ELSE result ]
END
如果判断条件都不成立，则返回ELSE 子句的result； 如果没有 ELSE 子句，则返回空。示例:

    SELECT a, b,
       CASE
           WHEN a = 1 THEN 'aaa'
           WHEN b = 2 THEN 'bbb'
           ELSE 'ccc'
       END
IF
IF 函数是语言结构， 它与下面的 CASE 表达式功能相同:

CASE
    WHEN condition THEN true_value
    [ ELSE false_value ]
END
if(condition, true_value)
如果 condition为真，返回true_value ； 否则返回空， true_value 不进行计算。

if(condition, true_value, false_value)
如果 condition 为真，返回 true_value； 否则计算并返回 false_value 。

COALESCE
coalesce(value[, ...])
返回参数列表中的第一个非空value 。 与 CASE表达式相似，仅在必要时计算参数。

NULLIF
nullif(value1, value2)
如果 value1 与 value2 相等，返回空；否则返回value1 。

TRY
try(expression)
通过返回null来判断表达式并处理某些类型的错误。

在某些查询中，可以通过TRY功能指定NULL或默认值来判断查询是否成功。
try功能可以与COALESCE功能结合使用。
try可以用来处理以下错误：

- 除数为0
- cast或function参数无效
- 数值超出范围
示例：

SELECT * FROM shipping;

 origin_state | origin_zip | packages | total_cost
--------------+------------+----------+------------
 California   |      94131 |       25 |        100
 California   |      P332a |        5 |         72
 California   |      94025 |        0 |        155
 New Jersey   |      08544 |      225 |        490
(4 rows)
未使用try的失败查询

SELECT CAST(origin_zip AS BIGINT) FROM shipping;
查询失败: Can not cast 'P332a' to BIGINT
引入 TRY的NULL值:

SELECT TRY(CAST(origin_zip AS BIGINT)) FROM shipping;

 origin_zip
------------------
      94131
 NULL
      94025
      08544
(4 rows)
未使用try的失败查询

SELECT total_cost / packages AS per_package FROM shipping;
查询失败:/ by zero
使用TRY和 COALESCE的默认值:

SELECT COALESCE(TRY(total_cost / packages), 0) AS per_package FROM shipping;

  per_package
----------------
          4
         14
          0
         19
(4 rows)
转换函数
Presto会将数字和字符值隐式转换成正确的类型。 Presto不会把字符和数字类型相互转换。 例如，一个查询期望得到一个varchar类型的值， Presto不会自动将bigint类型的值转换为varchar 类型。

如果有必要，可以将值显式转换为指定类型。

转换函数
cast(value AS type) → type
显式转换一个值的类型。 可以将varchar类型的值转为数字类型，反过来转换也可以。

try_cast(value AS type) → type
与 cast() 相似，区别是转换失败返回null。

其他
typeof(expr) → varchar
返回提供的表达式的类型的名称：

SELECT typeof(123); -- integer
SELECT typeof('cat'); -- varchar(3)
SELECT typeof(cos(2) + 1.5); -- double
数学函数和运算符
数学运算符
运算符	描述
+	加
-	减
*	乘
/	除（整数除法执行截断）
%	模数（余数）
数学函数
abs(x) → [same as input]
返回x的绝对值

cbrt(x) → double
返回x的立方根

ceil(x) → [same as input]
是ceiling()的同名方法

ceiling(x) → [same as input]
返回x的向上取整的数值

cosine_similarity(x, y) → double
返回稀疏向量x和y之间的余弦相似度：

SELECT cosine_similarity(MAP(ARRAY['a'], ARRAY[1.0]), MAP(ARRAY['a'], ARRAY[2.0])); -- 1.0
degrees(x) → double
将角度x以弧度转换为度

e() → double
返回欧拉常数

exp(x) → double
Returns Euler’s number raised to the power of x

floor(x) → [same as input]
x四舍五入到最接近的整数

from_base(string, radix) → bigint
Returns the value of string interpreted as a base-radix number

ln(x) → double
返回x的自然对数

log2(x) → double
Returns the base 2 logarithm of x

log10(x) → double
Returns the base 10 logarithm of x

log(x, b) → double
Returns the base b logarithm of x

mod(n, m) → [same as input]
Returns the modulus (remainder) of n divided by m

pi() → double
返回常数Pi

pow(x, p) → double
Returns xraised to the power of power()

power(x, p) → double
Returns x raised to the power of p

radians(x) → double
将角度x以度为单位转换为弧度

rand() → double
random()的另一种表达

random() → double
返回0.0 <= x <1.0范围内的伪随机值

random(n) → [same as input]
返回0和n（不含）之间的伪随机数

round(x) → [same as input]
返回x四舍五入到最接近的整数

round(x, d) → [same as input]
返回x四舍五入为d为小数

sign(x) → [same as input]
返回x的正负数值，即：

x为0，返回0,
x大于0，返回1,
x小于0，返回1.
对于double类型参数, 则:

参数为NaN，返回NaN,
参数为正无穷大，返回1,
参数为负无穷大，返回1.
sqrt(x) → double
返回x的平方根

to_base(x, radix) → varchar
Returns the base-radix representation of x.

truncate(x) → double
舍弃x的小数位数，返回整数

width_bucket(x, bound1, bound2, n) → bigint
Returns the bin number of x in an equi-width histogram with the specified bound1 and bound2 bounds and n number of buckets.

width_bucket(x, bins) → bigint
Returns the bin number of x according to the bins specified by the array bins. The bins parameter must be an array of doubles and is assumed to be in sorted ascending order

三角函数
所有三角函数的参数都是以弧度表示。参考单位转换函数degrees()和 radians()

acos(x) → double
返回x的反余弦

asin(x) → double
返回x的反正弦

atan(x) → double
返回x的反正切

atan2(y, x) → double
返回y / x的反正切

cos(x) → double
返回x的余弦值

cosh(x) → double
返回x的双曲余弦

sin(x) → double
返回x的正弦

tan(x) → double
返回x的正切值

tanh(x) → double
返回x的双曲正切

浮点函数
infinity() → double
返回表示正无穷大的常数

is_finite(x) → boolean
确定x是否有限

is_infinite(x) → boolean
确定x是否无限

is_nan(x) → boolean
确定x是否是数字

nan() → double
返回不是数字的常量。

位函数
bit_count(x, bits) → bigint
按照第二个参数的表示计算x的位数 (被视为位符号整数)

SELECT bit_count(9, 64); -- 2
SELECT bit_count(9, 8); -- 2
SELECT bit_count(-7, 64); -- 62
SELECT bit_count(-7, 8); -- 6
bitwise_and(x, y) → bigint
Returns the bitwise AND of x and y in 2’s complement representation.

bitwise_not(x) → bigint
Returns the bitwise NOT of x in 2’s complement representation.

bitwise_or(x, y) → bigint
Returns the bitwise OR of x and y in 2’s complement representation.

bitwise_xor(x, y) → bigint
Returns the bitwise XOR of x and y in 2’s complement representation.

此外还有bitwise_and_agg() and bitwise_or_agg().

十进制函数和运算符
十进制文字
使用DECIMAL 'xxxxxxx.yyyyyyy'语法定义DECIMAL类型的文字。

文字的DECIMAL类型的精度将等于文字数字（包括尾随和前导零）。 比例将等于小数部分的数字数（包括尾随零）。

示例	数据类型
DECIMAL '0'	DECIMAL(1)
DECIMAL '12345'	DECIMAL(5)
DECIMAL '0000012345.1234500000'	DECIMAL(20, 10)
二进制算术十进制运算符
支持标准数学运算符。
下表说明了结果的精度和尺度计算规则。 假设x的类型为DECIMAL（xp，xs），y的类型为DECIMAL（yp，ys）。

运算	结果类型精度	结果类型量表
x + y 和 x - y	min(38, 1 + min(xs, ys) + min(xp - xs, yp - ys) )	max(xs, ys)
x * y	min(38, xp + yp)	xs + ys
x / y	min(38, xp + ys + max(0, ys-xs) )	max(xs, ys)
x % y	min(xp - xs, yp - ys) + max(xs, bs)	max(xs, ys)
如果操作的数学结果不能用结果数据类型的精度和比例精确表示，则会出现异常情况 - 值超出范围。

当以不同的比例和精度对十进制类型进行操作时，这些值首先被强制为一个常用的超类型。 对于接近最大可表示精度（38）的类型，当其中一个操作数不符合公共超类型时，可能导致值超出范围误差。 例如，十进制（38，0）和十进制（38，1）的公共超类型是十进制（38,1），但是适合十进制（38，0）的某些值不能表示为十进制（38，1）。

比较运算符
所有标准比较运算符和BETWEEN运算符都支持DECIMAL类型

一元十进制运算符
-运算符执行否定。 结果类型与参数类型相同。

字符串运算符
使用运算符： || 完成字符串连接

字符串函数
chr(n) → varchar
返回在下标为n的位置的char字符的字符串格式表示

codepoint(string) → integer
Returns the Unicode code point of the only character of string.

concat(string1, ..., stringN) → varchar
返回string1, string2, ..., stringN 的拼接，此功能与标准SQL的连接运算符（||）功能相同

length(string) → bigint
以字符形式返回字符串的长度

levenshtein_distance(string1, string2) → bigint
返回string1和string2的编辑距离，即将string1更改为string2所需的单个字符编辑（插入，删除或替换）的最小数量

lower(string) → varchar
将字符串转换为小写

lpad(string, size, padstring) → varchar
Left pads string to size characters with padstring. If size is less than the length of string, the result is truncated to size characters. size must not be negative and padstring must be non-empty.

ltrim(string) → varchar
从字符串中删除前导空格

replace(string, search) → varchar
从字符串中删除所有search实例

replace(string, search, replace) → varchar
用replace字符串替换所有search实例

reverse(string) → varchar
返回字符的反向排列

rpad(string, size, padstring) → varchar
Right pads string to size characters with padstring. If size is less than the length of string, the result is truncated to size characters. size must not be negative and padstring must be non-empty.

rtrim(string) → varchar
删除字符串结尾空格

split(string, delimiter) → array
通过delimiter分割字符串并返回一个数组

split(string, delimiter, limit) → array
通过delimiter分割字符串，并返回按limit大小限制的数组。 数组中的最后一个元素包含字符串中的所有剩余内容。limit必须是正数

split_part(string, delimiter, index) → varchar
通过delimiter分割字符串并返回字段索引，字段索引以1开头。如果索引大于字段数，则返回null

split_to_map(string, entryDelimiter, keyValueDelimiter) → map
通过entryDelimiter和keyValueDelimiter拆分字符串并返回map。 entryDelimiter将字符串分解成key-value对。keyValueDelimiter将每对分割成key和value

strpos(string, substring) → bigint
返回字符串中子字符串的第一个实例的起始位置。位置以1开头。如果没有，返回0

position(substring IN string) → bigint
返回字符串中子字符串的第一个实例的起始位置。位置以1开头。如果没有，返回`0

substr(string, start) → varchar
从start位置开始返回字符串的其余部分。位置从1开始。如果start为负，则起始位置代表从字符串的末尾开始倒数

substr(string, start, length) → varchar
从start位置开始返回长度为length的字符串的子串。位置从1开始。如果start为负，则起始位置代表从字符串的末尾开始倒数

trim(string) → varchar
删除字符串前后的空格

upper(string) → varchar
将字符串转换为大写

Unicode函数
normalize(string) → varchar
用NFC规范化形式转换字符串

normalize(string, form) → varchar
使用指定的规范化形式转换字符串。form必须是以下关键字之一：

Form	解释
NFD	规范分解
NFC	规范分解，其次是规范组合
NFKD	兼容性分解
NFKC	兼容性分解，其次是规范组合
Note:
SQL标准函数有特殊的语法，需要将form指定为关键字，而不是字符串

to_utf8(string) → varbinary
将字符串编码为UTF-8 varbinary表示形式

from_utf8(binary) → varchar
从二进制解码UTF-8编码的字符串,无效的UTF-8序列被替换为Unicode替换字符U + FFFD

from_utf8(binary, replace) → varchar
从二进制解码UTF-8编码的字符串。无效的UTF-8序列被replace替换。替换字符串必须是单个字符或空格（无效字符会被删除）

二进制函数
二进制运算符
|| 运算符执行连接

二进制函数
length(binary) → bigint
返回binary的字节长度。

concat(binary1, ..., binaryN) → varbinary
返回binary1, ..., binaryN 的拼接，此功能与标准SQL的连接运算符（||）功能相同

to_base64(binary) → varchar
将binary编码为base64字符串表示。

from_base64(string) → varbinary
将base64编码的string解码为二进制数据。

to_base64url(binary) → varchar
使用URL安全字符，将binary编码为base64字符串表示。

from_base64url(string) → varbinary
使用URL安全字符，将base64编码的string解码为二进制数据。

to_hex(binary) → varchar
将binary编码为16进制字符串表示。

from_hex(string) → varbinary
将16进制编码的string解码为二进制数据。

to_big_endian_64(bigint) → varbinary
Encodes bigint in a 64-bit 2’s complement big endian format.

from_big_endian_64(binary) → bigint
Decodes bigint value from a 64-bit 2’s complement big endian binary.

to_ieee754_32(real) → varbinary
Encodes real in a 32-bit big-endian binary according to IEEE 754 single-precision floating-point format.

to_ieee754_64(double) → varbinary
Encodes double in a 64-bit big-endian binary according to IEEE 754 double-precision floating-point format.

crc32(binary) → bigint
计算二进制CRC-32。对于通用哈希，使用xxhash64（）可以更快，并产生更好的质量散列

md5(binary) → varbinary
计算binary的md5哈希值

sha1(binary) → varbinary
计算binary的sha1哈希值

sha256(binary) → varbinary
计算binary的sha256哈希值

sha512(binary) → varbinary
计算binary的sha512哈希值

xxhash64(binary) → varbinary
计算binary的xxhash6哈希值

日期时间函数和运算符
日期时间运算符
运算符	示例	结果
+	date '2012-08-08' + interval '2' day )	2012-08-10
+	time '01:00' + interval '3' hour	04:00:00.000
+	timestamp '2012-08-08 01:00' + interval '29' hour	2012-08-09 06:00:00.000
+	timestamp '2012-10-31 01:00' + interval '1' month	2012-11-30 01:00:00.000
+	interval '2' day + interval '3' hour	2 03:00:00.000
+	interval '3' year + interval '5' month	3-5
-	date '2012-08-08' - interval '2' day	2012-08-06
-	time '01:00' - interval '3' hour	22:00:00.000
-	dtimestamp '2012-08-08 01:00' - interval '29' hour	2012-08-06 20:00:00.000
-	timestamp '2012-10-31 01:00' - interval '1' month	2012-09-30 01:00:00.000
-	interval '2' day - interval '3' hour	1 21:00:00.000
-	interval '3' year - interval '5' month	2-7
时区转换
运算符：AT TIME ZONE，用于设置一个时间戳的时区:

SELECT timestamp '2012-10-31 01:00 UTC';
2012-10-31 01:00:00.000 UTC

SELECT timestamp '2012-10-31 01:00 UTC' AT TIME ZONE 'America/Los_Angeles';
2012-10-30 18:00:00.000 America/Los_Angeles
日期时间函数
current_date -> date
返回查询开始时的当前日期

current_time -> time with time zone
返回查询开始时的当前时间

current_timestamp -> timestamp with time zone
返回查询开始时的当前时间戳。

current_timezone() → varchar
以IANA（例如，America / Los_Angeles）定义的格式返回当前时区，或以UTC的固定偏移量（例如+08：35）返回当前时区

date(x) → date
这是CAST(x AS date)的另一种表达

from_iso8601_timestamp(string) → timestamp with time zone
将ISO 8601格式化的字符串解析为具有时区的时间戳

from_iso8601_date(string) → date
将ISO 8601格式的字符串解析为日期

from_unixtime(unixtime) → timestamp
Returns the UNIX timestamp unixtime as a timestamp.

from_unixtime(unixtime, string) → timestamp with time zone
Returns the UNIX timestamp unixtime as a timestamp with time zone using string for the time zone.

from_unixtime(unixtime, hours, minutes) → timestamp with time zone
Returns the UNIX timestamp unixtime as a timestamp with time zone using hours and minutes for the time zone offset.

localtime -> time
返回查询开始时的当前时间

localtimestamp -> timestamp
返回查询开始时的当前时间戳

now() → timestamp with time zone
这是current_timestam的另一种表达

to_iso8601(x) → varchar
将x格式化为ISO 8601字符串。 x可以是date, timestamp,或带时区的timestamp

to_unixtime(timestamp) → double
Returns timestamp as a UNIX timestamp.

Note
下列SQL标准的函数不使用括号：

current_date
current_time
current_timestamp
localtime
localtimestamp

截取函数
函数 date_trunc支持如下单位：

单位	Example Truncated Value
second	2001-08-22 03:04:05.000
minute	2001-08-22 03:04:00.000
hour	2001-08-22 03:00:00.000
day	2001-08-22 00:00:00.000
week	2001-08-20 00:00:00.000
month	2001-08-01 00:00:00.000
quarter	2001-07-01 00:00:00.000
year	2001-01-01 00:00:00.000
上面的例子使用时间戳： 2001-08-22 03:04:05.321 作为输入。

date_trunc(unit, x) → [same as input]
返回x截取到单位unit之后的值

间隔函数
本章中的函数支持如下所列的间隔单位：

Unit	Description
second	Seconds
minute	Minutes
hour	Hours
day	Days
week	Weeks
month	Months
quarter	Quarters of a year
year	Years
date_add(unit, value, timestamp) → [same as input]
在timestamp的基础上加上value个unit。如果想要执行相减的操作，可以通过将value赋值为负数来完成

date_diff(unit, timestamp1, timestamp2) → bigint
返回 timestamp2 - timestamp1之后的值，该值的表示单位是unit

Duration函数
parse_duration函数支持以下单位：

Unit	Description
ns	Nanoseconds
us	Microseconds
ms	Milliseconds
s	Seconds
m	Minutes
h	Hours
d	Days
parse_duration(string) → interval
Parses string of format value unit into an interval, where value is fractional number of unitvalues:

SELECT parse_duration('42.8ms'); -- 0 00:00:00.043
SELECT parse_duration('3.81 d'); -- 3 19:26:24.000
SELECT parse_duration('5m');     -- 0 00:05:00.000
MySQL日期函数
在这一章节使用与MySQLdate_parse和str_to_date方法兼容的格式化字符串。下面的表格是基于MySQL手册列出的，描述了各种格式化描述符：

Specifier	Description
%a	Abbreviated weekday name (Sun .. Sat)
%b	Abbreviated month name (Jan .. Dec)
%c	Month, numeric (1 .. 12) [4]
%D	Day of the month with English suffix (0th, 1st, 2nd, 3rd, ...)
%d	Day of the month, numeric (01 .. 31) [4]
%e	Day of the month, numeric (1 .. 31) [4]
%f	Fraction of second (6 digits for printing: 000000 .. 999000; 1 - 9 digits for parsing: 0 .. 999999999) [1]
%H	Hour (00 .. 23)
%h	Hour (01 .. 12)
%I	Hour (01 .. 12)
%i	Minutes, numeric (00 .. 59)
%j	Day of year (001 .. 366)
%k	Hour (0 .. 23)
%l	Hour (1 .. 12)
%M	Month name (January .. December)
%m	Month, numeric (01 .. 12) [4]
%p	AM or PM
%r	Time, 12-hour (hh:mm:ss followed by AM or PM)
%S	Seconds (00 .. 59)
%s	Seconds (00 .. 59)
%T	Time, 24-hour (hh:mm:ss)
%U	Week (00 .. 53), where Sunday is the first day of the week
%u	Week (00 .. 53), where Monday is the first day of the week
%V	Week (01 .. 53), where Sunday is the first day of the week; used with %X
%v	Week (01 .. 53), where Monday is the first day of the week; used with %x
%W	Weekday name (Sunday .. Saturday)
%w	Day of the week (0 .. 6), where Sunday is the first day of the week [3]
%X	Year for the week where Sunday is the first day of the week, numeric, four digits; used with %V
%x	Year for the week, where Monday is the first day of the week, numeric, four digits; used with %v
%Y	Year, numeric, four digits
%y	Year, numeric (two digits) [2]
%%	A literal % character
%x	x, for any x not listed above
[1] Timestamp被截断为毫秒。
[2] 解析时，两位数的年份格式假定为1970.2069，因此1970年将会产生“70”，而“69”将产生2069年。
[3] 此说明符尚不支持。可以使用day_of_week（）（使用1-7而不是0-6）。
[4] (1, 2, 3, 4) 此说明符不支持0作为一个月或一天。

Note
这些格式化描述符现在还不支持：%D %U %u %V %X

date_format(timestamp, format) → varchar
使用format指定的格式，将timestamp格式化成字符串。

date_parse(string, format) → timestamp
按照format指定的格式，将字符串string解析成timestamp。

Java日期函数
在这一章节中使用的格式化字符串都是与Java的 SimpleDateFormat样式兼容的。

format_datetime(timestamp, format) → varchar
使用format指定的格式，将timestamp格式化成字符串。

parse_datetime(string, format) → timestamp with time zone
按照format指定的格式，将字符串string解析成带时间戳的timestamp。

抽取函数
可以使用抽取函数来抽取如下域：

Field	Description
YEAR	year()
QUARTER	quarter()
MONTH	month()
WEEK	week()
DAY	day()
DAY_OF_MONTH	day()
DAY_OF_WEEK	day_of_week()
DOW	day_of_week()
DAY_OF_YEAR	day_of_year()
DOY	day_of_year()
YEAR_OF_WEEK	year_of_week()
YOW	year_of_week()
HOUR	hour()
MINUTE	minute()
SECOND	second()
TIMEZONE_HOUR	timezone_hour()
TIMEZONE_MINUTE	timezone_minute()
抽取函数支持的数据类型取决于需要抽取的域。大多数域都支持日期和时间类型。

extract(field FROM x) → bigint
从x中返回域field

Note
SQL标准的函数一般都会使用特定的语法来指定参数。

便利的抽取函数
day(x) → bigint
Returns the day of the month from x.

day_of_month(x) → bigint
This is an alias for day().

day_of_week(x) → bigint
Returns the ISO day of the week from x. The value ranges from 1 (Monday) to 7 (Sunday).

day_of_year(x) → bigint
Returns the day of the year from x. The value ranges from 1 to 366.

dow(x) → bigint
day_of_week()的另一种表达

doy(x) → bigint
day_of_year()的另一种表达

hour(x) → bigint
Returns the hour of the day from x. The value ranges from 0 to 23.

minute(x) → bigint
Returns the minute of the hour from x.

month(x) → bigint
Returns the month of the year from x.

quarter(x) → bigint
Returns the quarter of the year from x. The value ranges from 1 to 4.

second(x) → bigint
Returns the second of the hour from x.

timezone_hour(timestamp) → bigint
Returns the hour of the time zone offset from timestamp.

timezone_minute(timestamp) → bigint
Returns the minute of the time zone offset from timestamp.

week(x) → bigint
Returns the ISO week of the year from x. The value ranges from 1 to 53.

week_of_year(x) → bigint
week()的另一种表达.

year(x) → bigint
Returns the year from x.

year_of_week(x) → bigint
Returns the year of the ISO week from x.

yow(x) → bigint
year_of_week()的另一种表达.

正则表达式函数
所有的正则表达式函数都使用Java样式的语法。

当使用多行模式（通过（？m）标志启用）时，只有\ n被识别为行终止符。 （？d）标志不受支持，不能使用。
大小写相关以Unicode方式执行（通过（？i）标志启用）。 不支持上下文相关和本地匹配。（？u）标志不支持。
不支持代理对。 例如，\ uD800 \ uDC00不被视为U + 10000，必须指定为\ x {10000}
对于没有基本字符的非间距标记，（\ b）会被错误地处理。
字符集（例如[A-Z123]）不支持\ Q和\ E，将会被认为是文字。
Unicode字符类（\ p {prop}）支持以下场景：
姓名中的所有下划线都必须删除。例如，使用OldItalic而不是Old_Italic
必须直接指定脚本，而不需要使用Is，script =或sc = prefixes。 示例：\ p {Hiragana}
必须使用In前缀指定blcoks。 不支持block =和blk =前缀。 示例：\ p {Mongolian}
必须直接指定类别，不带Is，general_category =或gc =前缀。 示例：\ p {L}
二进制属性必须直接指定，不需要Is。 示例：\ p {NoncharacterCodePoint}
regexp_extract_all(string, pattern) → array
返回字符串中pattern模式匹配的substring(s) ：

SELECT regexp_extract_all('1a 2b 14m', '\d+'); -- [1, 2, 14]
regexp_extract_all(string, pattern, group) → array
查找字符串中所有出现的pattern模式，并返回group编号组

SELECT regexp_extract_all('1a 2b 14m', '(\d+)([a-z]+)', 2); -- ['a', 'b', 'm']
regexp_extract(string, pattern) → varchar
返回字符串中匹配pattern的第一个子字符串

SELECT regexp_extract('1a 2b 14m', '\d+'); -- 1
regexp_extract(string, pattern, group) → varchar
查找字符串中第一个出现的pattern模式，并返回group编号组

SELECT regexp_extract('1a 2b 14m', '(\d+)([a-z]+)', 2); -- 'a'
regexp_like(string, pattern) → boolean
评估pattern并确定它是否包含在字符串中
此函数类似于LIKE运算符，期望pattern仅需要包含在字符串中，而不需要匹配所有的字符串。执行一个包含操作而不是匹配操作。可以通过使用^和$来匹配整个字符串

SELECT regexp_like('1a 2b 14m', '\d+b'); -- true
regexp_replace(string, pattern) → varchar
从字符串中删除与pattern匹配的子字符串的每个实例：

SELECT regexp_replace('1a 2b 14m', '\d+[ab] '); -- '14m'
regexp_replace(string, pattern, replacement) → varchar
替换字符串中pattern匹配的子字符串的每个实例。 可以使用$ g作为替代使用的引用组，也可以使用$ {name}来引用。 替换中的美元符号（$）可能会需要用（\ $）进行转义

SELECT regexp_replace('1a 2b 14m', '(\d+)([ab]) ', '3c$2 '); -- '3ca 3cb 14m`
regexp_split(string, pattern) → array
使用pattern模式拆分字符串并返回数组。结尾的空字符串被保留

SELECT regexp_split('1a 2b 14m', '\s*[a-z]+\s*'); -- [1, 2, 14, ]
JSON函数
转换为JSON
支持由BOOLEAN, TINYINT, SMALLINT, INTEGER, BIGINT, REAL, DOUBLE或VARCHAR转换。
当ARRAY中的元素是以上类型时支持转换。
当map的key的类型是VARCHAR，value类型是以上类型时支持转换。

SELECT CAST(NULL AS JSON); -- NULL
SELECT CAST(1 AS JSON); -- JSON '1'
SELECT CAST(9223372036854775807 AS JSON); -- JSON '9223372036854775807'
SELECT CAST('abc' AS JSON); -- JSON '"abc"'
SELECT CAST(true AS JSON); -- JSON 'true'
SELECT CAST(1.234 AS JSON); -- JSON '1.234'
SELECT CAST(ARRAY[1, 23, 456] AS JSON); -- JSON '[1,23,456]'
SELECT CAST(ARRAY[1, NULL, 456] AS JSON); -- JSON '[1,null,456]'
SELECT CAST(ARRAY[ARRAY[1, 23], ARRAY[456]] AS JSON); -- JSON '[[1,23],[456]]'
SELECT CAST(MAP(ARRAY['k1', 'k2', 'k3'], ARRAY[1, 23, 456]) AS JSON); -- JSON '{"k1":1,"k2":23,"k3":456}'
请注意，从NULL转换为JSON并不是直接转换的。 从独立的NULL转换将产生一个SQL NULL而不是JSON'null'。 但是，当从数组或包含NULL的映射转换时，生成的JSON将具有null。

从JSON转换为其他格式
支持转换为BOOLEAN, TINYINT, SMALLINT, INTEGER, BIGINT, REAL, DOUBLE或VARCHAR。
当ARRAY中的元素是以上类型时支持转换。
当map的key的类型是VARCHAR，value类型是以上类型时支持转换。

SELECT CAST(JSON 'null' AS VARCHAR); -- NULL
SELECT CAST(JSON '1' AS INTEGER); -- 1
SELECT CAST(JSON '9223372036854775807' AS BIGINT); -- 9223372036854775807
SELECT CAST(JSON '"abc"' AS VARCHAR); -- abc
SELECT CAST(JSON 'true' AS BOOLEAN); -- true
SELECT CAST(JSON '1.234' AS DOUBLE); -- 1.234
SELECT CAST(JSON '[1,23,456]' AS ARRAY(INTEGER)); -- [1, 23, 456]
SELECT CAST(JSON '[1,null,456]' AS ARRAY(INTEGER)); -- [1, NULL, 456]
SELECT CAST(JSON '[[1,23],[456]]' AS ARRAY(ARRAY(INTEGER))); -- [[1, 23], [456]]
SELECT CAST(JSON '{"k1":1,"k2":23,"k3":456}' AS MAP(VARCHAR, INTEGER)); -- {k1=1, k2=23, k3=456}
JSON数组可以具有混合元素类型，JSON映射可以具有混合值类型。 这使得在某些情况下无法将其转换为SQL数组和映射。 为了解决这个问题，Presto支持部分转换数组和映射：

SELECT CAST(JSON '[[1, 23], 456]' AS ARRAY(JSON)); -- [JSON '[1,23]', JSON '456']
SELECT CAST(JSON '{"k1": [1, 23], "k2": 456}' AS MAP(VARCHAR, JSON)); -- {k1 = JSON '[1,23]', k2 = JSON '456'}
SELECT CAST(JSON '[null]' AS ARRAY(JSON)); -- [JSON 'null']
JSON函数
json_array_contains(json, value) → boolean
确定json中是否存在值（包含JSON数组的字符串）

SELECT json_array_contains('[1, 2, 3]', 2);
json_array_get(json_array, index) → varchar
将指定index处的元素返回到json数组中,index从0开始计数

SELECT json_array_get('["a", "b", "c"]', 0); -- 'a'
SELECT json_array_get('["a", "b", "c"]', 1); -- 'b'
此函数还支持通过负的index从数组末尾读取元素索引

SELECT json_array_get('["c", "b", "a"]', -1); -- 'a'
SELECT json_array_get('["c", "b", "a"]', -2); -- 'b'
如果指定索引处的元素不存在，则函数返回null：

SELECT json_array_get('[]', 0); -- null
SELECT json_array_get('["a", "b", "c"]', 10); -- null
SELECT json_array_get('["c", "b", "a"]', -10); -- null
json_array_length(json) → bigint
返回json的数组长度（包含JSON数组的字符串）

SELECT json_array_length('[1, 2, 3]');
json_extract(json, json_path) → json
评估json上的JSONPath表达式json_path（包含JSON的字符串），并将结果作为JSON字符串返回：

SELECT json_extract(json, '$.store.book');
json_extract_scalar(json, json_path) → varchar
和json_extract（）类似，但返回结果值作为一个字符串（而不是编码为JSON）。 json_path引用的值必须是scalar（boolean, number or string）：

SELECT json_extract_scalar('[1, 2, 3]', '$[2]');
SELECT json_extract_scalar(json, '$.store.book[0].author');
json_format(json) → varchar
将json作为字符串返回

SELECT json_format(JSON '[1, 2, 3]'); -- '[1,2,3]'
SELECT json_format(JSON '"a"'); -- '"a"'
json_parse(string) → json
解析字符串作为json

SELECT json_parse('[1, 2, 3]'); -- JSON '[1,2,3]'
SELECT json_parse('"a"'); -- JSON '"a"'
json_size(json, json_path) → bigint
跟json_extract（）一样，但返回size的大小。对于对象或数组，大小是成员数，scalar 的大小为零：

SELECT json_size('{"x": {"a": 1, "b": 2}}', '$.x'); -- 2
SELECT json_size('{"x": [1, 2, 3]}', '$.x'); -- 3
SELECT json_size('{"x": {"a": 1, "b": 2}}', '$.x.a'); -- 0
URL函数
提取函数
URL方法用于从HTTP URLs（或者是任何满足RFC 2396标准的有效URIs）中提取相应的信息。URL方法支持如下的语法：

[protocol:][//host[:port]][path][?query][#fragment]
被从URLs中提取出来的部分，不会包括URI的语法分隔符（如:或者?）

url_extract_fragment(url) → varchar
从URL返回fragment标识符

url_extract_host(url) → varchar
从url返回host

url_extract_parameter(url, name) → varchar
从url返回名为name的第一个查询字符串参数的值。 参数提取按照RFC 1866＃section-8.2.1规定的典型方式处理

url_extract_path(url) → varchar
从url返回path

url_extract_port(url) → bigint
从url返回port值

url_extract_protocol(url) → varchar
从url返回protocol

url_extract_query(url) → varchar
从url返回extract_query

Encoding函数
url_encode(value) → varchar
通过编码来转义值，以便它可以安全地包含在URL查询参数名称和值中

Alphanumeric字符不进行编码
字符.,-, * and _ 不进行编码
ASCII空格字符编码为+
所有其他字符都转换为UTF-8，字节编码为字符串％XX，其中XX是UTF-8字节的大写十六进制值
url_decode(value) → varchar
解除URL编码的值。 这个函数是url_encode（）的反向。

聚合函数
聚合函数作用于一个数据集，计算出一个单独的结果。

除了 count()、 count_if() 、 max_by() 和 approx_distinct() ， 所有聚合函数都忽略空值，如果没有输入或全部输入都为空时，返回空。 例如，sum()返回空，而不是0。 avg()会将数据中的空值进行计数。 coalesce函数可以将空转换为0。

一般聚合函数
arbitrary(x) → [与输入相同]
返回 x 的任意非空值（如果存在的话）。

array_agg(x) → array<[same as input]>
返回从输入x元素创建的数组。

avg(x) → double
返回所有输入值的平均数（算术平均数）。

avg(time interval type) → time interval type
返回所有输入值的平均间隔长度。

bool_and(boolean) → boolean
如果所有输入值都为 TRUE 返回 TRUE ，否则返回 FALSE 。

bool_or(boolean) → boolean
如果任何一个输入值为TRUE返回 TRUE ，否则返回 FALSE 。

checksum(x) → varbinary
返回给定值的不受顺序影响的校验和。

count(*) → bigint
返回输入行的数量。

count(x) → bigint
返回非空输入值的数量。

count_if(x) → bigint
返回输入值为TRUE 的数量。 本函数与count(CASE WHEN x THEN 1 END) 相同。

every(boolean) → boolean
bool_and() 的另一种表达。

geometric_mean(x) → double
返回所有输入值的几何平均值。

max_by(x, y) → [与x相同]
返回x 与 y 的最大值进行关联的结果，通过全部输入值进行关联。

max_by(x, y, n) → array<[same as x]>
以y的降序返回与y的所有输入值的n个最大值相关联的x的n个值。

min_by(x, y) → [与x相同]
返回x与y的最小值进行关联的结果，通过全部输入值进行关联。

min_by(x, y, n) → array<[same as x]>
以y的升序返回与y的所有输入值的n个最小值相关联的x的n个值。

max(x) → [与输入相同]
返回全部输入值的最大值。

max(x, n) → array<[same as x]>
返回x的所有输入值的n个最大值。

min(x) → [与输入相同]
返回全部输入值的最小值。

min(x, n) → array<[same as x]>
返回x的所有输入值的n个最小值

sum(x) → [与输入相同]
返回全部输入值的和。

按位聚合函数
bitwise_and_agg(x) → bigint
Returns the bitwise AND of all input values in 2’s complement representation.

bitwise_or_agg(x) → bigint
Returns the bitwise OR of all input values in 2’s complement representation.

Map聚合函数
histogram(x) → map
返回包含每个输入值发生次数的计数的map。

map_agg(key, value) → map
返回一个由 key / value 键值对构成的map。

map_union(x) → map
返回所有map的并集。 如果在多个map中找到一个键，则该结果的键值来自任意map。

multimap_agg(key, value) → map>
返回从输入键/值对创建的多重复合。 每个键可以与多个值相关联。

近似聚合函数
approx_distinct(x) → bigint
返回不重复输入值的近似数量。 本函数给出 count(DISTINCT x) 的近似值。 如果所有输入值都为空则返回0。

本函数会产生2.3%的误差， （近似正态）误差分布的标准偏差会覆盖全部数据集。 对于任意指定的输入， 不保证误差上限。

approx_distinct(x, e) → bigint
返回不重复输入值的近似数量。 本函数给出count(DISTINCT x)的近似值。 如果所有输入值都为空则返回0。

本函数会产生不超过 e的误差， （近似正态）误差分布的标准偏差会覆盖全部数据集。 对于任意指定的输入， 不保证误差上限。 目前的函数实现要求 e在[0.01150, 0.26000]范围之间。

approx_percentile(x, percentage) → [与输入相同]
按照百分比percentage，返回所有 x输入值的近似百分比。percentage 的值必须在0到1之间， 并且所有输入行必须为常量。

approx_percentile(x, percentages) → array<[same as x]>
按照百分比percentage,返回每个x的所有输入值的近似百分位数。 percentage数组的每个元素必须在0和1之间，并且所有输入行的数组必须是常量。

approx_percentile(x, w, percentage) → [与输入相同]
按照百分比percentage，返回所有 x输入值的近似百分比。 每一项的宽度使用w。 至少有一个宽度为整数。 x 设置有效的百分位。percentage 的值必须在0到1之间， 并且所有输入行必须为常量。

approx_percentile(x, w, percentage, accuracy) → [same as x]
按照百分比percentage的每项权重w返回x的所有输入值的近似百分位数，其精度为accuracy。 权重必须是至少为1的整数值。 它实际上是百分位数集合中的值x的复制计数。 percentage的值必须在0和1之间，并且对于所有输入行必须是常数。 精度必须是大于零且小于1的值，并且对于所有输入行，其精度必须是常数。

approx_percentile(x, w, percentages) → array<[same as x]>
按照百分比percentage的每个项目权重w返回x的所有输入值的近似称重百分位数。 权重必须是至少为1的整数值。 它实际上是百分位数集合中的值x的复制计数。 数组的每个元素都必须介于0和1之间，并且所有输入行的数组必须是常量。

numeric_histogram(buckets, value, weight) → map
按照 buckets 桶的数量，为所有的 value计算近似直方图， 每一项的宽度使用weight 。 本算法大体上基于：

Yael Ben-Haim and Elad Tom-Tov, "A streaming parallel decision tree algorithm",
J. Machine Learning Research 11 (2010), pp. 849--872.
buckets 必须是 bigint 。 value 和 weight 必须是数值。

numeric_histogram(buckets, value) → map
按照buckets 桶的数量，为所有的 value 计算近似直方图， 本函数与 numeric_histogram() 相同， 只是没有 weight 参数，每一项的宽度都为 1 。

统计聚合函数
corr(y, x) → double
返回输入值的相关系数。

covar_pop(y, x) → double
返回输入值的总体协方差。

covar_samp(y, x) → double
返回输入值的样本协方差。

kurtosis(x) → double
返回所有输入值的过多峰度。 使用以下表达式进行无偏估计：

kurtosis(x) = n(n+1)/((n-1)(n-2)(n-3))sum[(x_i-mean)^4]/stddev(x)^4-3(n-1)^2/((n-2)(n-3))
regr_intercept(y, x) → double
返回输入值的线性回归截距。 y是依赖值。 x是独立值。

regr_slope(y, x) → double
返回输入值的线性回归斜率。 y是依赖值。 x是独立值。

skewness(x) → double
返回所有输入值的偏度。

stddev(x) → double
stddev_samp() 的另一种表达。

stddev_pop(x) → double
返回全部输入值的总体标准偏差

stddev_samp(x) → double
返回全部输入值的样本标准偏差。

variance(x) → double
var_samp() 的另一种表达。

var_pop(x) → double
返回全部输入值的总体方差。

var_samp(x) → double
返回全部输入值的样本方差。

窗口函数
窗口函数主要用于在查询结果的所有行之间进行计算。窗口函数运行在HAVING语句之后，但是运行在ORDER BY语句之前。如果想要调用窗口函数，需要使用OVER语句来指定窗口。一个窗口有3个组成部分：

The partition specification, which separates the input rows into different partitions. This is analogous to how the GROUP BY clause separates rows into different groups for aggregate functions.
The ordering specification, which determines the order in which input rows will be processed by the window function.
The window frame, which specifies a sliding window of rows to be processed by the function for a given row. If the frame is not specified, it defaults to RANGE UNBOUNDED PRECEDING, which is the same as RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW. This frame contains all rows from the start of the partition up to the last peer of the current row.
例如：下面的查询将orders表中的信息按照每个出纳员营业额的大小进行排序:

SELECT orderkey, clerk, totalprice,
       rank() OVER (PARTITION BY clerk
                    ORDER BY totalprice DESC) AS rnk
FROM orders
ORDER BY clerk, rnk
聚合函数
所有聚合函数可以通过添加OVER子句用作窗口函数。 为当前行的窗口框架中的行上的每一行计算聚合函数。

例如，以下查询为每位店员按日期生成订单价格滚动总和：

SELECT clerk, orderdate, orderkey, totalprice,
       sum(totalprice) OVER (PARTITION BY clerk
                             ORDER BY orderdate) AS rolling_sum
FROM orders
ORDER BY clerk, orderdate, orderkey
排序函数
cume_dist() → bigint
返回一组值中的值的累积分布。 结果是窗口分区的窗口排序中的行前面或对等的行数除以窗口分区中的总行数。 因此，排序中的任何关联值将计算为相同的分布值。

dense_rank() → bigint
返回一组值中的值的等级。与rank（）类似，除了tie值不会在序列中产生空白。

ntile(n) → bigint
将每个窗口分区的行划分为n个桶，范围从1到最多n。 Bucket值将不同于1。如果分区中的行数并未均匀分配到桶数中，则剩余值将从第一个桶开始分配一个桶。

例如，对于6行4个桶，桶值将如下所示：1 1 2 2 3 4

percent_rank() → bigint
返回值组中值的百分比排序。 结果是（r-1）/（n-1）其中r是行的rank（），n是窗口分区中的总行数。

rank() → bigint
返回一组值中的值的等级。 排名是一行加上不与该行对等的行之前的行数。 因此，排序中的关联值将在序列中产生间隙。 对每个窗口分区执行排名。

row_number() → bigint
根据窗口分区中的行的顺序，为每行返回一个唯一的，顺序的数字，从一开始。

Value函数
first_value(x) → [same as input]
返回窗口的第一个值。

last_value(x) → [same as input]
返回窗口的最后一个值。

nth_value(x, offset) → [same as input]
从窗口开始返回指定偏移处的值。 偏移开始于1，偏移量可以是任何标量表达式。 如果偏移量为空或大于窗口中的值的数量，则返回null。 偏移量不可以为零或负值

lead(x[, offset[, default_value]]) → [same as input]
返回窗口中当前行之后的偏移行的值。偏移量从0开始，这是当前行。 偏移量可以是任何标量表达式。 默认偏移量为1,如果偏移量为空或大于窗口，则返回default_value，否则返回null。

lag(x[, offset[, default_value]]) → [same as input]
返回偏移行之前的值，该窗口中Offsets从0开始，即当前行。 偏移量可以是任何标量表达式。 默认偏移量为1.如果偏移量为空或大于窗口，则返回default_value，否则返回null。

颜色函数
bar(x, width) → varchar
在ANSI条形图中使用默认的low_color（红色）和high_color（绿色）呈现单条。 例如，如果x为25％，宽度为40则通过该功能。 将绘制一个10个字符的红色条形，然后绘制30个空格，以创建一个40个字符的栏。

bar(x, width, low_color, high_color) → varchar
在ANSI条形图中呈现指定宽度的单行。 参数x是[0,1]之间的双重值。 落在范围[0,1]之外的x的值将被截断为0或1值。 low_color和high_color捕获用于水平条形图的任一端的颜色。 例如，如果x是0.5，宽度是80，low_color是0xFF0000，而high_color是0x00FF00，这个函数将返回一个从红色（0xFF0000）和黄色（0xFFFF00）变化的40个字符栏，而80个字符栏的剩余部分将是 填充空格。

color(string) → color
返回从格式“＃000”的4个字符的字符串捕获解码的RGB值的颜色。 输入字符串应该是包含CSS样式的短rgb字符串或black, red, green, yellow, blue, magenta,cyan, white之一的varchar格式。

color(x, low, high, low_color, high_color) → color
使用双参数x，low和high返回在low_color和high_color之间插值的颜色，以计算一个分数，然后传递给如下所示的颜色（fraction，low_color，high_color）函数。 如果x超出了由低和高定义的范围，它的值将被截断以适合此范围。

color(x, low_color, high_color) → color
根据0到1.0之间的双参数x返回在low_color和high_color之间插值的颜色。 参数x是[0,1]之间的双重值。 落在范围[0,1]之外的x的值将被截断为0或1值。

render(x, color) → varchar
使用ANSI颜色代码使用特定颜色渲染值x。 x可以是double，bigint或varchar。

render(b) → varchar
接受布尔值b，并使用ANSI颜色代码呈现绿色true或红色false。

rgb(red, green, blue) → color
返回一个颜色值，捕获以0到255之间的int参数提供的三分量颜色值的RGB值：red, green, blue。

数组函数和运算符
下标运算符:[]
[] 运算符用于访问数组中的元素，索引从1开始:

SELECT my_array[1] AS first_element
连接运算符:||
|| 运算符可以将数组与数组进行连接，或者将数组与一个相同类型的元素进行连接:

SELECT ARRAY [1] || ARRAY [2]; => [1, 2]
SELECT ARRAY [1] || 2; => [1, 2]
SELECT 2 || ARRAY [1]; => [2, 1]
数组函数
array_distinct(x) → array
从数组x中删除重复的值。

array_intersect(x, y) → array
返回x和y的交集中的元素的数组，没有重复。

array_union(x, y) → array
返回x和y的并集中的元素的数组，没有重复。

array_except(x, y) → array
返回x中的元素数组，但不返回y，而不重复。

array_join(x, delimiter, null_replacement) → varchar
使用分隔符和可选字符串来连接给定数组的元素以替换空值。

array_max(x) → x
返回输入数组的最大值。

array_min(x) → x
返回输入数组的最小值。

array_position(x, element) → bigint
返回数组x中元素第一次出现的位置（如果未找到则返回0）。

array_remove(x, element) → array
从数组x中删除所有与element相等的元素。

array_sort(x) → array
对数组 x排序并返回结果。 x 中的元素必须是可排序的。

arrays_overlap(x, y) → boolean
测试数组x和y是否具有任何非空元素。 如果没有共同的非空元素，但是数组包含null，则返回null。

cardinality(x) → bigint
返回数组 x的基数（大小）。

concat(array1, array2, ..., arrayN) → array
连接数组array1,array2,..., 和arrayN 。本函数与连接符(||)功能相同。

contains(x, element) → boolean
如果数组 x中包含元素element返回true。

element_at(array, index) → E
返回给定索引处的数组元素。 如果index> = 0，则该函数提供与SQL标准下标运算符（[]）相同的功能。 如果index <0，则element_at会从最后一个元素开始访问。

filter(array, function) → array
参见filter()。

flatten(x) → array
通过连接包含的数组来将array（array（T））映射到array（T）。

reduce(array, initialState, inputFunction, outputFunction) → x
参见reduce()。

repeat(element, count) → array
重复element计数次数。

reverse(x) → array
返回一个与数组x反序的数组。

sequence(start, stop) → array
从start到stop生成整数序列，如果start小于或等于stop，则递增1，否则为-1。

sequence(start, stop, step) → array
从start到stop生成整数序列，按step递增。

sequence(start, stop, step) → array
从start到stop生成一个时间戳序列，按step递增。 步骤类型可以是 INTERVAL DAY TO SECOND或INTERVAL YEAR TO MONTH。

shuffle(x) → array
生成给定数组x的随机排列。

slice(x, start, length) → array
子集数组x从start开始（或从起始为负）开始，长度为length。

transform(array, function) → array
参见transform().

zip(array1, array2[, ...]) → array
将给定的数组（以元素方式）合并到单个行数组中。 第N个参数的第M个元素将是第M个输出元素的第N个字段。 如果参数的长度不均匀，则缺少值将填充为NULL。

SELECT zip(ARRAY[1, 2], ARRAY['1b', null, '3b']); -- [ROW(1, '1b'), ROW(2, null), ROW(null, '3b')]
zip_with(array1, array2, function) → array
参见zip_with()

Map函数和运算符
下标运算符: []
[] 运算符用于取出map中指定键的值:

SELECT name_to_age_map['Bob'] AS bob_age
Map函数
cardinality(x) → bigint
返回map x的基数（大小）

element_at(map, key) → V
返回给定key的值，如果key不包含在映射中，则返回NULL。

map() → map
返回一个空map。

SELECT map(); -- {}
map(array, array) → map
返回使用给定key/value数组创建的map。

SELECT map(ARRAY[1,3], ARRAY[2,4]); -- {1 -> 2, 3 -> 4}
此外可以用map_agg()和 multimap_agg() 创建一个map聚合。

map_concat(map1, map2, ..., mapN) → map
返回所有给定map的联合。 如果在多个给定的地图中找到一个key，那么该结果图中该key的值来自这些映射中的最后一个

map_filter(map, function) → map
参见map_filter().

transform_keys(map, function) → MAP
参见transform_keys().

transform_values(map, function) → MAP
参见transform_values().

map_keys(x) → array
返回map x里所有的keys。

map_values(x) → array
返回map x里所有的values.

Lambda表达式和函数
Lambda表达式
Lambda表达式写为->:

x -> x + 1
(x, y) -> x + y
x -> regexp_like(x, 'a+')
x -> x[1] / x[2]
x -> IF(x > 0, x, -x)
x -> COALESCE(x, 0)
x -> CAST(x AS JSON)
大多数SQL表达式可以在lambda体中使用，除了一些例外：

不支持子查询。 x -> 2 + (SELECT 3)
不支持聚合。 x -> max(y)
TRY功能尚不支持。 (try_cast() is supported.)

Lambda函数
filter(array, function) → ARRAY
从数组的元素构造数组，函数返回true：

SELECT filter(ARRAY [], x -> true); -- []
SELECT filter(ARRAY [5, -6, NULL, 7], x -> x > 0); -- [5, 7]
SELECT filter(ARRAY [5, NULL, 7, NULL], x -> x IS NOT NULL); -- [5, 7]
map_filter(map, function) → MAP
从map的条目构造map，函数返回true

SELECT map_filter(MAP(ARRAY[], ARRAY[]), (k, v) -> true); -- {}
SELECT map_filter(MAP(ARRAY[10, 20, 30], ARRAY['a', NULL, 'c']), (k, v) -> v IS NOT NULL); -- {10 -> a, 30 -> c}
SELECT map_filter(MAP(ARRAY['k1', 'k2', 'k3'], ARRAY[20, 3, 15]), (k, v) -> v > 10); -- {k1 -> 20, k3 -> 15}
reduce(array, initialState S, inputFunction, outputFunction) → R
返回从数组中减少的单个值。 将为数组中的每个元素按顺序调用inputFunction。 除了获取元素之外，inputFunction将接收当前状态，最初为initialState，并返回新状态。 将调用outputFunction将最终状态转换为结果值。 它可能是身份功能（i - > i）。 例如：

SELECT reduce(ARRAY [], 0, (s, x) -> s + x, s -> s); -- 0
SELECT reduce(ARRAY [5, 20, 50], 0, (s, x) -> s + x, s -> s); -- 75
SELECT reduce(ARRAY [5, 20, NULL, 50], 0, (s, x) -> s + x, s -> s); -- NULL
SELECT reduce(ARRAY [5, 20, NULL, 50], 0, (s, x) -> s + COALESCE(x, 0), s -> s); -- 75
SELECT reduce(ARRAY [5, 20, NULL, 50], 0, (s, x) -> IF(x IS NULL, s, s + x), s -> s); -- 75
SELECT reduce(ARRAY [2147483647, 1], CAST (0 AS BIGINT), (s, x) -> s + x, s -> s); -- 2147483648
SELECT reduce(ARRAY [5, 6, 10, 20], -- calculates arithmetic average: 10.25
              CAST(ROW(0.0, 0) AS ROW(sum DOUBLE, count INTEGER)),
              (s, x) -> CAST(ROW(x + s.sum, s.count + 1) AS ROW(sum DOUBLE, count INTEGER)),
              s -> IF(s.count = 0, NULL, s.sum / s.count));
transform(array, function) → ARRAY
返回将数组应用于数组的每个元素的数组：

SELECT transform(ARRAY [], x -> x + 1); -- []
SELECT transform(ARRAY [5, 6], x -> x + 1); -- [6, 7]
SELECT transform(ARRAY [5, NULL, 6], x -> COALESCE(x, 0) + 1); -- [6, 1, 7]
SELECT transform(ARRAY ['x', 'abc', 'z'], x -> x || '0'); -- ['x0', 'abc0', 'z0']
SELECT transform(ARRAY [ARRAY [1, NULL, 2], ARRAY[3, NULL]], a -> filter(a, x -> x IS NOT NULL)); -- [[1, 2], [3]]
transform_keys(map, function) → MAP
返回一个将地图应用于地图的每个条目并转换键的地图：

SELECT transform_keys(MAP(ARRAY[], ARRAY[]), (k, v) -> k + 1); -- {}
SELECT transform_keys(MAP(ARRAY [1, 2, 3], ARRAY ['a', 'b', 'c']), (k, v) -> k + 1); -- {2 -> a, 3 -> b, 4 -> c}
SELECT transform_keys(MAP(ARRAY ['a', 'b', 'c'], ARRAY [1, 2, 3]), (k, v) -> v * v); -- {1 -> 1, 4 -> 2, 9 -> 3}
SELECT transform_keys(MAP(ARRAY ['a', 'b'], ARRAY [1, 2]), (k, v) -> k || CAST(v as VARCHAR)); -- {a1 -> 1, b2 -> 2}
SELECT transform_keys(MAP(ARRAY [1, 2], ARRAY [1.0, 1.4]), -- {one -> 1.0, two -> 1.4}
                      (k, v) -> MAP(ARRAY[1, 2], ARRAY['one', 'two'])[k]);
transform_values(map, function) → MAP
返回一个映射，它将函数应用于map的每个条目并转换值：

SELECT transform_values(MAP(ARRAY[], ARRAY[]), (k, v) -> v + 1); -- {}
SELECT transform_values(MAP(ARRAY [1, 2, 3], ARRAY [10, 20, 30]), (k, v) -> v + 1); -- {1 -> 11, 2 -> 22, 3 -> 33}
SELECT transform_values(MAP(ARRAY [1, 2, 3], ARRAY ['a', 'b', 'c']), (k, v) -> k * k); -- {1 -> 1, 2 -> 4, 3 -> 9}
SELECT transform_values(MAP(ARRAY ['a', 'b'], ARRAY [1, 2]), (k, v) -> k || CAST(v as VARCHAR)); -- {a -> a1, b -> b2}
SELECT transform_values(MAP(ARRAY [1, 2], ARRAY [1.0, 1.4]), -- {1 -> one_1.0, 2 -> two_1.4}
                        (k, v) -> MAP(ARRAY[1, 2], ARRAY['one', 'two'])[k] || '_' || CAST(v AS VARCHAR));
zip_with(array, array, function) → array
使用函数将两个给定的数组（以元素方式）合并到单个数组中。 两个数组必须具有相同的长度：

SELECT zip_with(ARRAY[1, 3, 5], ARRAY['a', 'b', 'c'], (x, y) -> (y, x)); -- [ROW('a', 1), ROW('b', 3), ROW('c', 5)]
SELECT zip_with(ARRAY[1, 2], ARRAY[3, 4], (x, y) -> x + y); -- [4, 6]
SELECT zip_with(ARRAY['a', 'b', 'c'], ARRAY['d', 'e', 'f'], (x, y) -> concat(x, y)); -- ['ad', 'be', 'cf']
Teradata函数
这些功能提供与Teradata SQL的兼容性。

字符串函数
char2hexint(string) → varchar
返回字符串的UTF-16BE编码的十六进制表示形式。

index(string, substring) → bigint
同strpos()

substring(string, start) → varchar
同substr()

substring(string, start, length) → varchar
同substr()

日期函数
本节中的功能使用与Teradata datetime功能兼容的格式字符串。 下表根据Teradata参考手册介绍了支持的格式说明符：

运算符	Description
- / , . ; :	标点符号被忽略
dd	Day of month (1-31)
hh	Hour of day (1-12)
hh24	Hour of the day (0-23)
mi	Minute (0-59)
mm	Month (01-12)
ss	Second (0-59)
yyyy	4-digit year
yy	2-digit year
Note
目前不支持大小写区分，所有说明符必须为小写

to_char(timestamp, format) → varchar
将时间戳格式化为字符串。

to_timestamp(string, format) → timestamp
将字符串解析成TIMESTAMP。

to_date(string, format) → date
将字符串解析为DATE。


```

### Created by:along
### Created time:2019-1-31

```
--当前日期,  与current_date一样 ,  当前时间,  当前时间戳,   当前时间+时区,    日期时间时区
select current_date, now(),  localtime, localtimestamp, current_time , current_timestamp
--时区,  时间戳对应日期时间(unixtime), 时间戳对应日期时间时区(unixtime, hours, minutes)时分用来计算时区偏移量, timestamp转换为时间戳
select current_timezone(), from_unixtime(0), from_unixtime(0, 10, 12),to_unixtime(now())


--日期时间加减运算
select
    date '2019-01-17' + interval '2' day  as "日期相加" ,
    time '19:00' + interval '3' hour as "时间相加",
    timestamp '2019-01-17 19:00' + interval '29' hour  as "日期时间相加1",
    timestamp '2012-10-31 19:00' + interval '1' month  as "日期时间相加2",
    interval '2' day + interval '3' hour as "日期时间运算1",
    interval '3' year + interval '5' month as "日期时间运算2",
    date '2019-01-17' - interval '2' day as "日期时间运算3",
    time '19:00' - interval '3' hour  as "时间相减1",
    timestamp '2019-01-17 19:00' - interval '29' hour  as "时间相减2",
    timestamp '2019-01-17 19:00' - interval '13' month  as "时间相减3",
    interval '2' day - interval '3' hour  as "时间相减4",
    interval '3' year - interval '5' month as "时间相减5";



-- 时区转换 AT TIME ZONE
select timestamp '2019-01-17 19:00 UTC';

select timestamp '2019-01-17 19:00 UTC' AT TIME ZONE 'America/Los_Angeles', timestamp '2019-01-17 19:00 UTC' AT TIME ZONE  'Asia/Shanghai'

-- 截取函数 (second,minute,hour,day,week,month,quarter, year)
select date_trunc('hour', timestamp '2019-01-17 19:00:00.100');

-- 增加时间日期
select date_add( 'day', -2 ,cast('2019-01-17' as date) );
-- 减少时间日期 (timestamp2 - timestamp1)
select date_diff('day',cast('2019-02-05' as date),cast('2019-01-17' as date));


--时间函数
-- ns  Nanoseconds
-- us  Microseconds
-- ms  Milliseconds
-- s Seconds
-- m Minutes
-- h Hours
-- d Days
select parse_duration('42.8ms'), parse_duration('3.81 d') ,parse_duration('5m')

--时间格式化
--date_format(timestamp, format) → varchar
--date_parse(string, format) → timestamp

select date_format(cast('2019-01-17' as date),'%Y%m%d');
select date_parse('20190117','%Y%m%d');


--时间日期截取
-- YEAR  year()
-- QUARTER quarter()
-- MONTH month()
-- WEEK  week()
-- DAY day()
-- DAY_OF_MONTH  day()
-- DAY_OF_WEEK day_of_week()
-- DOW day_of_week()
-- DAY_OF_YEAR day_of_year()
-- DOY day_of_year()
-- YEAR_OF_WEEK  year_of_week()
-- YOW year_of_week()
-- HOUR  hour()
-- MINUTE  minute()
-- SECOND  second()
-- TIMEZONE_HOUR timezone_hour()
-- TIMEZONE_MINUTE timezone_minute()
select day_of_year(now());
select  week_of_year(cast('2019-01-17 19:30:30.300' as timestamp));

select  yow(cast('2019-01-17 19:30:30.300' as timestamp)), dow(cast('2019-01-17 19:30:30.300' as timestamp));

```