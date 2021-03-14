- nohup xxxx 2>&1 &
  - >表示重定向
  - 2表示异常信息
  - 1表示按标准情况输出
  - &表示后台
  - nohup输出为启动目录的nohup.out文件

- The Cluster ID lWXm_NMhTdyWWb0VhVJqLw doesn’t match stored clusterId Some(5_QiXBs-SESQnOPIRAd9EA) in meta.properties. The broker is trying to join the wrong cluster. Configured zookeeper.connect may be wrong.
  - 系统的默认log文件位置有误
