## kafka
- 分布式消息队列中间件
- 角色
  - consumer组 <-消费者
  - broker 本身
    - 面试题1:若broker没有返回ack：设置超时，配置对应的重试的次数，默认三次
    - 面试题2:多次需要ack返回，占用大量带宽：异步发送的方式+缓存池，统一校验
    - 面试题3:缓存池满了，，3.0清空  3.1溢写到临时缓冲区
  - producer
- topic主题
  - 逻辑划分
  - partition, 以segment段存储
- 保证数据不丢失
  - 生产端：ack   0（不等就发）  1（只等主节点）  -1 （等所有副本）
  - 存储：分区，备份
  - 消费：偏移量
- nohup xxxx 2>&1 &
  - >表示重定向
  - 2表示异常信息
  - 1表示按标准情况输出
  - &表示后台
  - nohup输出为启动目录的nohup.out文件

- The Cluster ID lWXm_NMhTdyWWb0VhVJqLw doesn’t match stored clusterId Some(5_QiXBs-SESQnOPIRAd9EA) in meta.properties. The broker is trying to join the wrong cluster. Configured zookeeper.connect may be wrong.
  - 系统的默认log文件位置有误

