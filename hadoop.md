- 理解多进程处理共享资源的概念
    - 在同一台主机时多个进程处理可以通过锁的概念进行控制
    - 在不同主机的多个进程处理时就需要分布式系统处理
- 集群：
    - 角色：leader,learner发起投票(follower,observer不参与投票),vlient
    - zookeeper: 数据发布/订阅模式