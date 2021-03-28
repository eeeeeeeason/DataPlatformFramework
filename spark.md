## spark课程介绍
### 内容
- mapreduce       1代
- hive -> shark   2代
- spark -> impala 3代
- flink           4代
### 一站式的计算平台
- 数据结构层面：完成半结构化结构化处理
- 机器学习

### spark概念
- RDDS   弹性分布式数据集 论文： Resilient Distributed Dataset A Fault-Tolerant Abstraction for In-Memory Cluster Computer
- ACCUMULATORS  累加器
- BROADCAST  广播
- GRAPHX   图计算，（社交网络图，最短路径）
### 启动方式
- local模式- 单机多线程
- stanalone-Master和worker
- standaloneHA
### sparksql 基于dataframe和dataset的数据结构---处理结构化数据的工具
- DSL和SQL查询风格--花式查询
### sparkStreaming基于DStream实现近实时的计算，处理秒级数据
- 类似于直梯，离散化流
### StructedStreaming结构化流---基于sparksql处理毫秒级别数据，实时
- 类似扶梯，实时
### 
存储：hdfs,hbase,redise
计算：pig,hive,spark.flink,druid,ck,doris
调度支持：azkaban,oozie,ds
资源调度：yarn
### 概念补充
- 数据：观测值和测量值
- 对数据加工和处理就变成了可信的数据，（信息）
- 数据挖掘（有价值的信息提取）
- 机器学习是一种方法，(数据挖掘是一件事情，通过机器学习方法解决数据挖掘的事情) (用户画像，推荐系统)，模式识别
- 深度学习：图像识别，语音。。。解决机器学习的不足，
- 4v补充 ，大数据无法直接解决价值密度低的问题，必须借助数据挖掘

### 
- Driver申请cpu-->经过appmaster---rs----nodemanager--container资源隔离--task
# 问题汇总综合
- client端启动为什么会导致网络传输数据激增，因为传输的仅仅是命令和结果
- spark的shuffle和hadoop的shuffle区别，为什么spark shuffle后分区数量不发生改变
- 另分区中内容乱序的目的是什么
