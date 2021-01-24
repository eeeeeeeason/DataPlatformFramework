## hadoop概念
- 分布式：
    整个业务拆解多个业务由不同机器完成
- 集群：
    - 多个机器通过网络连接，每台机器做一样的工作
- 怎么跟小白讲呢：从前有个餐厅只有一个厨师，做买菜，洗菜，炒菜
    - 客户多了，招多了一个厨师做一样的事，两个厨师就是集群
    - 随着客户增加招多了几个厨师就分为了几部分，1个做买菜，1个做洗菜，1个做炒菜 ， 分布式
    - 而炒菜1个不够，招多了1个，2个炒菜师傅就是分布式中的集群
- 负载均衡
    - 合理分配任务，让所有机器在一个合理范围内完成，若出现某些机子完成缓慢要及时修改任务分配
- hadoop
    - 1.hdfs 海量数据存储
    - 2.mapreduce 分布式计算框架，分析处理
    - 3.yarn 统一的资源调度平台，集群资源进行管理(spark)
- 性质
    - 扩容能力，集群可扩展到上万台
    - 成本低，对服务器单台性能要求不高
    - 高效率，节点数量多，可以共同承载并发操作
    - 可靠性，在hadoop中数据都是存在备份的
- 元数据：描述数据的数据，如位置，名字，大小等
## 架构
### 1.x 
![屏幕快照 2021-01-24 20.36.52.png](https://image-static.segmentfault.com/211/702/211702844-600d69fbf4099)
- ，namenode与jobtracker仅一个  弊端：1.单节点故障问题；2.jobtracker工作量太高；3.mapreduce集群资源分配后只能运行mapreduce程序，无法运行其他的计算任务
    - hdfs
        - namenode hdfs集群主节点 1.存储元数据(描述数据的数据)，2.管理从节点，3.负责数据读写操作
        - dataanode(出磁盘) hdfs集群从节点： 1.负责数据实际存储，2.和namenode保持心跳连接
        - secondarynode辅助节点：辅助hadoop中元数据信息的辅助管理，namenode元数据是需要持久化，每次重启将上一次editlog的数据合并到fsimage中，而secondarynamenode就是用来协助完成这项任务
    - mapreduce
        - jobtracker: mapreduce集群主节点 1.接收计算任务，2.分配任务给tasktracker，任务最终由tasktracker完成，3.分配资源
        - tasktracker(出内存和cpu): mapreduce集群从节点 1.接收主节点分配过来的任务， 2.根据资源要求，启动执行任务 3.将执行任务的结果交给jobtracker
    - 补充:windows中的删除其实是删除元数据，内容是没有删除的
### 2.x 
![屏幕快照 2021-01-24 20.57.54.png](https://image-static.segmentfault.com/423/504/4235045088-600d6eecdaaa6)
- hdfs集群
    - namenode
    - secondnamenode
    - datanode
- yarn(统一的资源调度平台)，支持除了mr以外的计算任务，只要这个计算任务需要资源，都可以部署在yarn平台上(yarn调度接口)
    - resourceManager
        - 接收计算任务，启动一个applicationmaster,负责管理任务
        - 负责资源分配调度
    - appMaster:每个任务都对应一个appmaster
        - 负责任务的全生命周期
        - 任务分配
        - 向主机申请资源
        - 负责连接nodemanager启动任务
    - nodeManager
        - 启动appmaster
        - 接收appmaster分配的任务
        - 执行任务与appmaster保持通信，并且与主机保持通信

![屏幕快照 2021-01-24 21.17.51.png](https://image-static.segmentfault.com/426/933/4269338719-600d73ae1c960)
### 高可用版本2.x，可以只对hdfs高可用，hdfs与yarn是独立的,jdk1.7
- hdfs
    - namenode 可以有两个，主备结构namenode(active) namenode(standby),与zookeeper的连接称为zkfc,借助zookeeper进行主节点确定与宕机管理，没有snn
        - 当两个namenode存储的元数据信息不一致就出现脑裂问题，因此诞生了journalnode(多台构成集群)，用于同步元数据，（为什么不用zookeeeper同步呢？）答：由于zookeeper有只支持体积很小的文件系统，不适用于大体积信息的同步，zookeeper真正的作用是检测active主节点的状态，宕机则代替
- yarn
    - rescourceManager可有多个，对外提供服务的仅一台active，与zookeeper的连接称为zkfc
    - journalnode 文件系统元数据信息管理，一般是奇数个
            
            
### 3.x  jdk1.8 与2.x区别
- hdfs
    - namenode 可以有多个
    - 3.0以前每份数据存份，3.0引入纠错编码0.5冗余校验数据存储方式+源文件
    - 改变最大的是hdfs 通过最近block块计算，根据最近计算原则，本地block块，加入到内存，先计算，通过IO，共享内存计算区域，最后快速形成计算结果，比Spark快10倍
### 4.1 部署模式
- Standalone mode（独立模式） 
- 伪分布式：在一个机器中，将Hadoop 集群中各个节点运行在一个服务器测试
- Cluster mode（群集模式）：会使用N台主机组成一个Hadoop集群。这种部署模式下，主节点和从节点会分开部署在不同的机器上

            
            
            
