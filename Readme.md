# 大数据背景。待补充
  - 我认为大数据的相关不应仅仅局限于技术理解，更应对于其背景有深度的分析，才能更好运用技术结合实际，充分阅读了许多资料之后，总结下来有以下几点，大数据也基于以下形成三个过程
    - 5g，移动应用兴起，各行业互联网化产生大量数据  ->   数据采集 ->  来源（日志，数据库，爬虫）
    - 企业对于大量数据清洗筛选有价值信息有较高的要求，有较高的实时分析需求   ->  数据分析
      - 采集和简单分析常用生态有
      - elk生态(elasticsearch+Logstash(fileBeats)+Kibana) 本部分我将会在后边做详细描述
      - flume+kafka+storm+redis
      - hadoop一条龙(hadoop,spark,flink)
    - 有价值的数据进行更深层次分析推理  ->  数据挖掘
      - 一般涉及算法，结合大数据构建数据模型，对未来进行预测
 - 传统数据分析与现今大数据分析对比
    - 传统数据处理强调纵向扩展：单机性能有瓶颈
    - 大数据处理通过网络将机器连接在一汽构成集群，提供分布式计算和分布式存储
## 为什么需要大数据，什么条件下需要大数据
    - 需要结合自身业务，并不是所有类型业务都套大数据。ppt公司除外
    - 对于部分业务处理半结构化数据采用传统的单机处理o(n)的时间很可能导致当天无法得到需要的报表，影响实时性
    - 对于超大规模数据有需要
## 公司开销：
    - 每年对于中型10-30人项目组开发费用350w(开发人员薪资按平均1.3w，20人，13薪)，搭建自己业务的前端，大数据群
    - 硬件：多核cpu，大容量固态硬盘(基于分布式一般2份备份数据)托管费（机位费，千兆带宽，电费维护费）对于超大型大数据项目，设备费甚至电费都会远超人工费用，但中型公司能够接触到整个环节，能够对于整个流程理解更加深刻。大约评估：以2020年数据，400tb存储+千兆带宽+intel4核高性能cpu+维护的设备费约60w，人工费400w。
    - 节约成本的方法： ....想省钱的放弃做大数据最省钱，短期大数据的变现能力和开销是需要各个企业做深度评估的
      - 基本操作：合理分配计算资源，存储资源，权限资源，业务资源
        - 存储：1.首先要意识到我们的数据中有大量的无效数据，要有意识的删除过期的数据，做好数据的生命周期管理与冷热分离。2.Snappy、Gzip，能压缩80%的空间，3.采用列式存储parquet可以不必将整条记录检索。
        - 计算资源管理：这个是一个很大的范畴，涉及dba，运维，大数据管理员
          - 硬件上：cpu,memory,network,io
          - 流程上：启动时的资源分配，磁盘异常使用的检测，超过常规数据量2倍或以上时的处理，集群扩容的评估，了解集群是否有机器错误率高，是否有人为sql错误导致的处理速度慢，队列管理等
        - 业务资源管理：理解业务，尽可能做到适合业务的发展，但无论是大数据技术还是如今的互联网需求变化都非常快，过去的技术栈可能成为未来的发展瓶颈，需求会随着热点变化，导致过去的技术结构无法适应，打补丁来弥补
        
## 大数据特征4v
    - 量大，数据指数级增长
    - 数据种类多：结构化数据(mysql)，半结构化（json），非结构化数据（视频，图片，语音等）
    - 离线批处理 -> 实时流处理
    - 数据有价值部分小，需要采用合理有效方式提取
