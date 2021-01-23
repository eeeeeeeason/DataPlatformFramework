### zookeeper

- 作用：分布式协调服务框架，解决分布式集群中应用系统的一致性问题，本质是一个文件存储系统，与es不同他仅有一套主从，主节点负责事务相关，从节点收到事务的请求转发给主节点，从节点负责读

- 性质

  - 全局一致性：每个服务器保存一份相同的数据
  - 可靠性：消息被一台接收，将被所有接收
  - 顺序性

    - 全局有序，在服务端，先插入的一定在后插入的前面
    - 偏序，客户端将数据插入a到zookeeper，在插入b到zookeeper，客户端在看数据时a在b前
  - 数据更新原子性：数据更新要么全部成功要么失败
  - 实时性：主节点数据发生改变后，其他结点会立即感知数据变化信息，以及实时能感知到服务器一些状态

- 与普通文件系统相似，内部各节点存在命名空间，是一种树形结构，在zookeeper中各个节点被称为znode

- 但是zookeeper的节点和普通系统节点存在区别

  - 节点具有兼具文件和目录两种特点，普通文件系统二者分离

    ```shell
    create -s /test/child1 acl  # 可以配置子节点就像目录一样,acl为权限
    create -s /test aaaaa acl   # 也可以输入文件内容,acl为权限
    ```

  - 原子性操作

  - 大小又限制znode<1M，实际使用远小于此值

  - 通过绝对路径引用

- 节点类型，在创建时就已经决定

  - 永久节点，只有用户真正执行删除操作才会被删除
    - 非序列化节点
    - 序列化节点。 create -s
      - 建立文件a.txt会在后边追加序列号10位初始为0，
  - 临时会话节点，会话结束临时节点删除，且不允许构建子节点
    - 非序列化节点
    - 序列化节点

- 操作
    - 创建节点

      ```shell
      create	/zk01	aaaaa # 永久
      create -s /zk02 bbbbb # 永久序列化
      create -e /zk03 ccccc # 临时
      create -e -s /zk04 dd # 临时序列化
      create /zk05/zk06 aaa # 带子节点的需要从父节点开始建
      ```

    - ls,get和ls2

      ```shell
      ls path [watch] # 查看根目录下有哪些节点
      ls2 path [watch] # 查看根目录下的详细信息
      #ls2 /
      #dataVersion 数据版本号，每次set+1，即使设置相同值仍会改变
      #cversion 子节点的版本号， znode子节点变化时cversion增加1
      #cZxid znode创建事务id
      #mZxid znode修改的事务id
      #ctime 创建的时间戳
      #mtime 更新发生的时间戳
      #ephemeralOwner 临时节点有该sessionid,不是临时节点
      get path [watch] # 指定节点的数据内容和属性信息
      ```

    - Set 更新

      ```shell
      set path data
      set /zk01 aaa
      每次修改覆盖原始危机
      ```

    - 删除节点

      ```shell
      rmr path #递归删除节点
      delete path [version]	# 一级一级删，如果有子节点无法删除
      ```

    - zookeeper限制quota

      ```shell
      Setquota -n|-b val path		# n为最多节点数， -b每个节点的数据量长度
      # -n 别忘了算本身，并且即使n与b都超出了也不过就是日志中warning，任务仍然能够进行，在哪个目录下启动，日志就会生成在哪个目录
      # -b 与 -n只有其中一个可以生效
      ```

    - 取消限制

      ```shell
      delquota [-n|-b] path   # 限制必须先删除后设置
      ```

      

    - 查看历史history

- 发布订阅者，观察者模式

  - 性质

    - 一次性触发，后续发生相同事件不再触发

    - 事件封装，WatchedEvent对象来封装服务端事件并传递

      ```
      通知状态（keeperState），事件类型（EventType）和节点路径（path）
      ```

    - **event异步发送**  :watcher通知事件从服务端发送到客户端是异步的

    - 先注册再发送

  - 步骤流程

    - 1.注册绑定进行监听

    - 2.事件发生触发watcher

      ```shell
      get 节点 watch 
      ```

  - 该模式可用作

    - zookeeper处理服务器上下线操作
      - 临时节点+watcher监听机制
      - 没有zookeeper就用心跳呗
    - 多节点选择主节点
      - 抢锁机制：临时节点(在master目录下创建，谁创建成功谁就是主节点)+watcher(从节点时刻舰艇master是否宕机，宕机就可以再重复这个抢master的步骤)+quota(限制只有一个子节点)

- java操作zookeeper

  - curator

  ```java
  // 1.创建zookeeper连接对象写去before
    @Before
    public void before(){
      ExponentialBackoffRetry retry = new ExponentialBackoffRetry(1000,3);
    String connectStr = "node1:2181,node2:2181,node3:2181";
    client = CuratorFrameworkFactory.newClient(connectStr, 5000, 5000, retry);
    // 启动客户端
    client.start();
    }
  // 2.修改数据 
  @Test
  public void test02() throws Exception{
  client.setData().forPath("/zk_java01", "我爱你,祖国".getBytes()); 
  // delete.forpath
  // 或deletechildrenifneeded
  // 或client.setData().forPath("/zk_java01", "我爱你,祖国".getBytes());
  // 设置后直接命令行是拿不到utf8的字串的需要在代码中处理为字符串
  // String operatorObjStr = new String(bytes);
  // System.out.println(operatorObjStr);
  }
  // 3.关闭连接
  @After
  public void after(){
    client.close();
  }

  ```

### 选举机制

- 第一种策略全新集体选举：在第一次启动zookeeper集群的时候
  - 整个集群配置为：3个节点
  - 当启动一台时，票投给自己，但集群启动数量没过半，即使投票了集群依然没有启动状态
  - 当启动第二台时，整个集群中已经启动过半，由于之前没有选举出老大，会让之前所有节点重新投票，默认情况下投给id最大的，第二台即为leader
  - 启动第三台时，由于已经有leader，id是2比3小但是无法再次选举
- 第二种策略非全新集体选举：往后启动zookeeper集群的时候
  - 当启动一台时，票投给自己，但集群启动数量没过半，即使投票了集群依然没有启动状态
  - 当启动第二台时，整个集群中已经启动过半，由于之前没有选举出老大，会让之前所有节点重新投票，此时票会投给数据量多的(一般不宕机数据量都一样，宕机的话数据量多表名信息更接近现实)，如果都一样还是投给id大的，过半产生老大

、

