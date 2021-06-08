### 1.es对复杂分布式机制的透明隐藏特性

**分片机制**：document插入到es集群中，没有关系数据如何分片，分到哪个shard

**cluster discover**：启动第二个es进程，会自动被发现，加入进集群，同时接受数据，replica shard

**shard负载均衡：**假设有25个shard，共 3个节点，es会自动进行均匀分配，保持节点的均衡读写负载

shard副本，请求路由（将请求分配到其他节点去），集群扩容，shard重分配

**扩容方案**：1.垂直扩容：重新购置2t服务器，替换原有1t服务器   2.水平扩容：重新购置两台1t服务器，加入到原服务器中去

常见采用水平扩容：成本低

**rebalance**：假设共7个shard，6台服务器总有一台服务器承载更重，但是添加服务器之后，会rebalance，降低那台服务器压力

**master**：1.管理索引元数据，索引的创建和删除，维护索引元数据，节点的增加和删除，维护集群的元数据。2.会自动选择一台节点作为master。3.master节点不会承载所有的请求

**节点对等的分布式架构**：每个节点都能接受所有的请求，接收到请求的节点会自动路由到对应到有该documnet的shard节点，最后收集数据并响应回去。

### 2.shard和replica机制的梳理

1.一个index会包含多个shard，一个3T的index，可以分配到三个shard，三台服务器上

2.每个shard都是一个最小工作单元，承载部分数据，lucene实例，完成的建立索引和处理请求的能力

3.增减节点，shard会自动在nodes中负载均衡，如节点A承载两个shard，新增节点B,则会分配一个shard到节点B

4.每个document只会存在某一个primary shard，以及其对应的replica shard，不可能存在多个primary shard。

5.replica shard是primary shard的副本，负责容错，以及承担读请求负载

6.primary shard数量在创建索引的时候就固定了，replica shard的数量可以更改，通常为5个primary shard，1个replica shard，也就是10个节点

7.primary shard 不能和自己的replica shard存放在同一个节点，否则节点宕机，但是可以和其他的primary shard的replica shard放在同一个节点

### 3.多node环境下创建index

> 单node情况下

1.假设创建一个3primary shard，3replica shard index，详见put 索引

2.集群status 是yellow， 详见查看集群状态，因为replica shar没法分配

3.3个primary shard 会在一个node中，3个replica shard 无法分配

4.集群可以正常工作，到哪宕机将无法使用，无法承接任何请求

> 多node

1.从单node，扩展成多node，那么replica shard会自动分配过去

2.primary 和replica shard会自动拷贝

3.读请求，同时承载

### 4.node扩容以及容错机制

> 扩容及容错

1.假设1节点存在a,b,c三个primary shard，2节点存在A，B，C对应replica shard

2.扩容3节点之后，3节点可能自动负载均衡将c，以及Bshard到3节点，每个node中shard数量更少，io/cpu/memory资源给每个shard分配更多，每个shard性能更好

3.如何超出系统扩容瓶颈，比如3个节点只有6个shard，但是要扩容到9个shard，增加replica shard数量，primary shard数量不能改变，replica shard变为2，所以3个primary shard变成9个shard

4.容错性：假设3个节点，9个shard，每个节点都会包含3个shard，因此可以容纳两个节点丢失数据。假设6个shard（3primary，3replica），每个节点有两个shard，允许1个节点丢失数据。

> 容错机制

1.假设3节点，9个shard，1节点a，b，cshard，2，3节点存replica shard

2.当master 节点发生宕机，会导致red

3.replica容错，replica提升到primary shard，yellow（这里是因为少了一个replicsa shard）

4.重启故障的node，会讲丢失的副本copy到该node中去，该node只是会使用之前已有的shard数据，只是同步一下宕机之后发生过的修改



