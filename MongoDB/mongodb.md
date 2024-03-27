## MongoDB基础

### MongoDB是什么？

分布式文件存储，NoSQL，C++编写；提供 **面向文档** 的存储；二进制的BSON存储数据

### MongoDB的存储结构？

- 文档：基本单元，由BSON键值对组成，相当于mysql中的一条**记录**；
- 字段：相当于 **列**
- 集合：相当于mysql中的 **表**；
- 数据库：相当于 database

```mariadb
{
  "_id": ObjectId("5f150af9df9db5a9add782ba"),  // 一个唯一的文档标识符
  "username": "john_doe",
  "age": 30,
  "email": "john.doe@example.com",
  "address": {
    "street": "123 Main St",
    "city": "Anytown"
  },
  "hobbies": ["hiking", "reading", "photography"]
}
```

### MongoDB的特点

- 记录以BSON文档存储
- 聚合管道实现聚合任务
- 支持多种索引
- 支持分片集群
- 支持存储大文件
- 数据压缩

### MongoDB的应用场景

- 单记录查询多的场景，联表查询少的场景；（一个用户下的所有搜索记录，只查一个用户id就行，将本来关系型的一对多数据全以BSON形式存下来）
- 键值对文档形式的数据；
- 读多写少场景

## MongoDB存储引擎

和mysql一样是插件式存储引擎；实现server层和引擎层的解耦；

MongoDB默认使用B树，默认引擎是**WiredTiger**，实际使用B+ 树；

### 为什么使用B树？

- MongoDb是非关系型数据库，追求读写单个记录的性能，应用读多写少的场景多；
- 追求**查询一条数据**的平均随机IO次数，因此B树的非叶子节点也能存数据，相对应的范围查询要比B+树慢；实际上没差多少，本质上是使用B+树

## MongoDB聚合

- 将多个文档的值合在一起
- 对集合中的数据进行一系列操作
- 分析数据随时间的变化

### 有哪几种聚合方法？

- 单一目的的聚合方法：count()、distinct()
- 聚合管道，有多个阶段，每个阶段接收前一阶段处理的数据； `db.collection.aggregate()`, 有、\$match 、\$limit等

## MongoDB索引

- 单字段索引
- 复合索引：有顺序影响，遵循最左前缀原则
- ttl索引：对每个文档设置过期机制

## MongoDB高可用

- 复制集群：主从复制
- 分片集群：横向扩展，根据分片键进行数据分区







































