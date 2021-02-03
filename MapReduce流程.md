# mapReduce分布式计算

## wordCount单个小文件输入，默认分区分组

![image](https://image-static.segmentfault.com/191/712/1917124999-6018c9e4bc8b7)

* 切分与map:根据换行符切分分配到多次mapping中，map每次能够取到k1,v1分别为索引偏移量，该行内容，按照计数要求我们输出k2即为该单词，v2为1作为计数
* 默认shuffle：按照k2字母或数字顺序进行排列
* reduce：接收到shuffle后的内容例如Hadoop,<1,1,1> 通过求和得出该词总次数为3
* 合并所有reduce结果得到最终输出内容

## 大文件或多文件输入，自定义分区，分组，数据类型

### 为什么需要自定义分区，分组，数据类型

* 只有理解业务才能作出更合理的流程
    * 自定义数据类型：我们需要处理的数据类型往往不是简单的一个key而是一个复合的数据，这个时候就需要以一个自定义数据类型进行存储
    * 自定义分区：因为默认的shuffle机制会将key不同的数据分到不同，而复杂的自定义数据会被分到不同的reduce，我们需要通过partition将其以自定义数据中的某个标示进行划分，让相同标示的数据发往同一个reduceTask，每个reduceTask可以生成一个结果数据
    * 自定义分组：由于自定义数据类型数据内容多个，即使其中一个标示位相同如订单id相同，但其中的组成如商品id不同会导致同一个reduce底下的一次iter仍然不能取到全部数据相同分组的元素在经过自定义分组后能够在同一个iter

## MapTask

### 工作机制

* 1.读取数据组件inputFormat会通过getSplits方法对输入目录的文件进行逻辑切片规划得到block，几个block启动几个MapTask;
* 2.RecordReader对象对分块进行读取，\n为分隔符，读取一行返回\<k,v> (偏移值，该行文本内容)
* 进入自己继承的mapper类中，执行用户重写的map函数，RecordReader读取一行调用一次用户重写的map函数
* 3.mapper逻辑结束之后将结果通过context.write进行collect数据收集
* 3.0 Shuffle
    * 3.1 分区 collect中，先进行分区，默认使用HashPartitioner，根据key及reducer的数量决定当前的这对输出数据最终交由哪个reduce task处理 默认对 key hash后 模 reduce数量，可自定义分区
        * 3.1.1 将数据写入内存，内存中这个区域为环形缓冲区，缓冲区的作用是批量收集mapper结果，减少磁盘io影响，缓冲区有大小限制，默认100m，当超过用户设置的阈值如0.8即80m时会额外开启一条线程进行溢写spill操作，此时mapper的输出结果还可以往剩下的20mb中写，缓冲区大小会影响整个程序的执行效率，原则上说缓冲区越大，io次数越少，执行越快
    * 3.2 排序 排序是模型默认的行为，是对序列化的字节做的排序
        * 3.2.1 自定义对象实现WritableComparable后即可以对compareTo重写
    * 3.3 规约 在map阶段使用Combiner能够将相同key的value处理，某些条件下可以提前合并结果，减少发送往reducerd的数据量，发送次数，提高网络传输的效率。combiner父类就是reducer所以业务相似，但combiner是在每个maptask所在节点运行，而reducer是接收全局所有mapper的输出结果
    * 3.4 分组 继承WritableComparator 设置job.setGroupingComparatorClass，可以将需要的数据放到同一次reduce执行中
* 4.合并溢写文件，当mapper输出结果很大时会发生多次溢写，每次溢写都会产生一个临时文件，整个数据处理结束后对磁盘中临时文件进行合并

### 并行度机制

* 根据逻辑切片数创建相应的mapTask
    * 切片大小约128m

## ReduceTask

### 工作机制

* 1.copy
    * eventFetcher获取已完成的map列表
        * Fetcher线程采用http请求maptask进行copy数据，
        * 同时启动两个merge线程，分别为inMemoryMerger和onDiskMerger，分别将内存中的数据merge到磁盘，将磁盘中的数据进行merge。
* 2.sort
    * 对合并后的大文件进行排序
* 3.reduce
    * 键相同的键值对调用一次reduce，处理完毕后输入到hdfs文件中

### Reducetask并行度机制

* 与map不同可以手动设置 job.setNumReduceTasks(num);

## 性能优化

### 数据输入

* 将小文件合并，因为大量小文件会产生大量的map任务，增大map装载次数，map的每次装载耗时甚至可能超过执行时间，可采用CombineTextInputFormat来输入

### Map阶段

* 减少spill溢写次数
* 减少合并次数，增大merge文件数目，减少merge次数
* 尽量先进行combine操作
* mapred-default.xml配置项
| 配置 | 默认值 | 解释 |
|-----|-----|-----|
|mapreduce.task.io.sort.mb|100|设置环型缓冲区的内存值大小|
|mapreduce.map.sort.spill.percent|0.8|设置溢写的比例|
|mapreduce.task.io.sort.factor|10|设置一次合并多少个溢写文件|
|mapreduce.task.min.num.spills.for.combine|3|运行时所需的最少文件溢出数

### Reduce阶段
- 合理设置map和reduce个数：因为彼此会竞争资源
- map,reduce共存：slowstart.completedmaps使得map一定阶段后reduce可以开始运行，减少等待时间
- 规避reduce使用：setNumReduceTasks设置为0，某些情况下map+combiner可以完成任务就不必再进行reducer，因为reducer收集数据阶段会产生大量网络消耗
- 合理设置reduce端的buffer：默认情况下数据达到阈值会写入到磁盘，但reduce又要从磁盘中读取数据，这个过程可以通过配置参数使得reduce可以直接从内存中读取数据，而不再等数据写到硬盘后读硬盘
| 配置 | 默认值 | 解释 |
|-----|-----|-----|
|mapreduce.job.reduce.slowstart.completedmaps|0.05|map执行到5%就开始执行reduce|
|mapred.job.reduce.input.buffer.percent|0.0|内存中保存map输出的数据，如果reducer需要的内存较少可以增加这个参数|

### shuffle
| 配置 | 默认值 | 解释 |
|-----|-----|-----|
|mapred.map.child.java.opts|-Xmx200m|默认以1g内存启动mapTask会导致内存溢出|
|mapred.reduce.child.java.opts|-Xmx200m|同上|
