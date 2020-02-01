# flink
---

## what
分布式处理、状态、流式计算  
基于内存的存储  

1.streams：流

2.time
时间：事件发生时间、事件进入flink的时间、在flink事件中被处理的时间  
event-time&watermark  
watermark=max(event_time)-△  
解决消息延迟、无序等问题  

3.state
keyed state and operator state  
主要解决问题：增量计算&容错

checkpoint & savepoint
checkpoint的内容：barrier和state

4.api：通常分为三层，由上而下可分为SQL/table API 、processFunction


## flink新feature-融合
 离线（batch）、实时（streaming）和在线（application）一体化

## 双流join state 优化
