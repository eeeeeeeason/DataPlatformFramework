### 函数讲解
- nvl(t value , t default value) // 返回非空值，或默认值
- coalesce(t v1,tv2) // 返回参数中第一个非空值
- case when then
  - case a when b then c [when d then e]* [else f] end  -- a等于b时返回c，若部位b而为d返回e
    -   case 80 when 80 then c  -- 前面是一个变量
  - case when a then b [when c then d]* [else e] end -- a条件成立时返回b 
    -   case when 80=80 then c  -- 前面是一个判断条件

### 并行操作
- 默认同时只能编译一段hivesql,并上锁，编译查询限制
  -   编译查询限制：sql在执行时会先被编译，hive只对一个会话进行处理，开启hive.driver.parallel,compilation设置为true，
  -   hive.driver.parallel.compilation.global.limit 默认值为3，设置为0或者负值，表示无限制
- 并行执行操作
  -   在执行hive的sql的时候，一个sql可能会编译多个节点，而在某些情况下，各个阶段之间的依赖关系不强，没有先后顺序，此时可以安排其并行执行，从而提升效率，默认串行执行
    -  set hive.exec.parallel=true 默认为false
    -  set hive.exec.parallel.thread.number=16 设置最大并行度，默认8
### 小文件合并操作
- 小文件太多默认会生成大量map task，申请资源占用大量时间
- 当执行sql时，如果对应表中有大量小文件，输出时也有大量小文件，会对hdfs产生影响，对后面使用该数据的sql也会产生相同问题
- 解决，
  - hive.merge.mapfiles: 在map-only任务结束时合并小文件
  - hive.merge.mapredfiles: 合并reduce端小文件，map-reduce结束时合并小文件
  - hive.merge.size.per.task： 合并后mr输出文件的大小，默认为256m。
  - hive.merge.smallfiles.avgsize: 当输出文件的平均大小小于此设置值时，启动一个独立的mapreduce任务进行文件merge,默认值为16m
### 矢量化查询
- 默认查询执行引擎一次处理一行，矢量化查询是hive特性，目的是按照每批1024行读取数据
- set hive.vectorized.execution.enabled=true;
- 数据格式必须为orc格式存储数据
### 读取零拷贝
- orc可以使用新的hdfs缓存api和zerocopy读取器来避免扫描时额外的数据复制到内存中
- 在读取时只读相关的字段，减少读取量
- set hive.exec.orc.zerocopy=true
### 数据倾斜优化操作
- 执行mapreduce，一个reduce拿到的数据量远大于其他
- 如何检查sql是否存在数据倾斜，在jobhistory中查看该工作的reduce的数量
  - 默认一个reduce处理1gb数据，
- group by 
  - 提前聚合，combiner
    - 小combiner:在一个mr中通过combiner方式来解决数据倾斜问题
      - hive.map.aggr=true 开启map端提前聚合操作
    - 大combiner，启动两个mr第一个走combiner,第二个mr做最终聚合合并操作
    - 可以通过自定义分区将数据均分，
    - hive.groupby.skewindata=true
- join
  - 采用map join ,bucket mapjoin , smb join
  - 单独启动一个mr处理
  - 运行期
    - set hive.optimize.skewjoin=true  -- hive sql 在运行过程中，动态检查每一个组内的key，如果发现某个key中的数据量远大于其他key的数据量，认为此key存在倾斜问题，此时就会将倾斜的key剔除，单独放置在一个mr中运行即可
    - set hive.skewjoin.key=100000 -- 当超过10w就会移走，
  - 编译期
    - set hive.optimize.skewjoin.compiletime=true; 默认为false
    - 在执行之前，我们已经提前知道某些key的值可能会有倾斜，提前将其排除掉
    - create table list_bucket_single (key string , value string)
    - skewed by (key) on (1,5,6)
    - [stored as directories];
  - 不管运行期优化，还是编译期优化，都会将倾斜的key单独找一个mr来运行，运行后将得到的结果和之前的mr进行union all合并操作，如果不了解数据就开运行期，或者都开，非常熟悉就开编译期
    - set hive.optimize.union.remove=true
    - 一旦开启此配置之后，在执行union all合并操作时候，可以不走mr，也不会再合并为一个文件，执行让两个mr结果输出到目标地即可
### 关联优化
- set hive.optimize.correlation=true
- 同一条sql中有一些group by，join触发的shuffle是相同的可以选择让其共享，以此来
