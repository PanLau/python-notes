## hive

### 窗口函数
### 累计、分组求和
> 参考文章:https://blog.csdn.net/abc200941410128/article/details/78408942?utm_source=blogxgwz0

### SQL 按字段分组后取每组内最大或最小的值
```
SELECT t1.OrderId,t1.QueueId 
FROM (
    SELECT tq.OrderId,tq.QueueId,ROW_NUMBER() OVER(PARTITION BY tq.OrderId ORDER BY tq.QueueId DESC) rn
    FROM TransferQueue tq WITH(NOLOCK)
) t1
WHERE rn = 1    
```