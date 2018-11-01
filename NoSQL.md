# NoSQL技术汇总

## Riak S3

- Riak S3很大程度上借鉴了Amazon Dynamo[Dynamo: Amazon’s Highly Available Key-value Store](https://docs.basho.com/riak/kv/2.2.3/learn/dynamo/)，这里[Riak vs. Cassandra – A Brief Comparison](http://basho.com/posts/technical/riak-vs-cassandra/)讲到了`Cassandra and Riak have architectural roots in Amazon’s Dynamo`。但是，Cassandra是宽表模型，即key/key/value存储，类似于hbase那样的列簇概念；而Riak是纯粹的key/value存储。
- Riak KV使用一致性哈希来分区和分布数据；
- Riak KV使用向量钟来解决对象版本问题，这里对向量钟有一个有助于理解的例子：[Why Vector Clocks are Easy](http://basho.com/posts/technical/why-vector-clocks-are-easy/)，这里又讲了该实现方式实际背后的一些困难：[Why Vector Clocks Are Hard](http://basho.com/posts/technical/why-vector-clocks-are-hard/)
- Riak KV使用Gossip协议来和其他节点交流哈希环状态和成员信息。

这篇文章介绍了一起时钟问题引起的故障，延伸阅读到有关向量钟等文章：[Clocks Are Bad, Or, Welcome to the Wonderful World of Distributed Systems](http://basho.com/posts/technical/clocks-are-bad-or-welcome-to-distributed-systems/)

Riak KV的一些基本概念：[Riak KV Glossary ](http://docs.basho.com/riak/kv/2.2.3/learn/glossary/)

Riak KV的冲突解决策略留待以后细看：[Conflict Resolution](https://docs.basho.com/riak/kv/2.2.3/developing/usage/conflict-resolution/)

Riak KV里对于一致性哈希的使用：[https://docs.basho.com/riak/kv/2.2.3/learn/concepts/clusters/](https://docs.basho.com/riak/kv/2.2.3/learn/concepts/clusters/)

![](resources/riak-ring.png)

![](resources/riak-data-distribution.png)

## HBase

