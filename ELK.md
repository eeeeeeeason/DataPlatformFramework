# 理解ElasticSearch概念模型
 - 背景: master&worker/主从/主备
![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/35fce1fe748a4a26bc4634e84af9bd27~tplv-k3u1fbpfcp-watermark.image)
- master&worker核心思想是分而治之，将一个原始任务分解为若干个语义等同的子任务,并由专门的工作者线程来并行执行这些任务,原始任务的结果是通过整合各个子任务的处理结果形成的
 	- 现实表现为两种模式主从和主备
    	- 主备：备份机器专心将主节点数据备份，耐心等待主机挂掉的时候取代，有瓶颈受限于主节点的读写能力，且在网络波动或其他异常时候可能导致备份结点误判导致主主模式
        ![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/87c18b8189364456a9c30817f78f1fe0~tplv-k3u1fbpfcp-watermark.image)
    	- 主从：从节点负责信息读取的功能，主节点负责写入，并输出到从节点
        ![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/42f5936daa0e43f3bd20fc4e0d0aef75~tplv-k3u1fbpfcp-watermark.image)
## es模型
  - 两套主从，既保证了稳定性和容错性
  	- master&worker
    	- 1.管理从节点状态/master
        - 2.对索引库curd/master
        - 3.数据存储查询/worker
    - 主shard和分片shard
    	- 写入时只对主shard写入，读取时二者皆可
    ![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bd2c959e4e554ea8a9e3f08ee9e487ba~tplv-k3u1fbpfcp-watermark.image)
----------------------------------------------
- 搜索工具发展进程
    - lucene jar包
    - solr
    - es与solr比较
      - es需要json,自行管理分布式状态
      - solr需要zookeeper管理分布式状态，传统搜索应用中效率高，实时搜索效率低
      - https://www.cnblogs.com/jajian/p/9801154.html
 - es索引库结构与关系型数据库比较、
 	- index ==== database
    - type  ==== table
    - document ==== record
    - field ==== field
    - mapping约束字段
-------------------------------------------------
 - es性质
 	- 全文搜索引擎，采用的是倒排索引，倒排索引即将分词后的各个关键词与对应文章关联后插入索引表。实现找出该词的文章时间复杂度在o(1)
 - 分词
    - 需求：客户输入关键词，需要将相关文章返回到客户端
    - 步骤1.先构建自身文章的索引。倒排索引
      - 选择合适的分词工具，standard分词，ik分词
      - 构建文档索引
    - 步骤2.基于索引查询关联文章并返回
    
    - 精确模式
      - 我爱北京天安门  =>  我 爱  北京  天安门
    - 详细模式
      - 我爱北京天安门  =>  我 爱 我爱 北京 我爱北京  天安门 北京天安门
    - ik分词
      - （词典，停用词典）加载、预处理、分词器分词、歧义处理、善后结尾 
    - jieba分词python
    - 准确数据类型：是keyword的话直接索引匹配
    - 全文文本类型：需要分词
    - 关系型数据库搜索弊端
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e111af9f8e5e4be3b39dcac9d20ddb2f~tplv-k3u1fbpfcp-watermark.image)
