
1. 什么是"primary copy replication"?

   多个server中有一个是primary, 其余都是backup. 这一组server用于存储file的副本. 客户端首先和primary通信,然后primary和其余的backup通信.

2. "replication"可以提高可用性("availability")和响应时间("response time")

  可用性是指在server失败时,用户仍然可以访问server. 复制技术保存多个备份,可以容忍失败.复制技术可以让用户尽快访问到你自己最近的数据.

  加快响应时间的同时,也需要想办法保证数据的一致性.

  Coda和Locus两个文件系统使用复制技术来提高可用性,同时加快响应时间,当他们没有很好的解决一致性问题

3. Harp 使用replicaiton只是用于提高可用性和可靠性

4. 什么是"partition"? 分布式系统中经常会遇到partition的问题.

  来自wikipedia的[解释](http://en.wikipedia.org/wiki/Network_partition):

  A network partition refers to the failure of a network device that causes a network to be split.

For example, in a network with multiple subnets where nodes A and B are located in one subnet and nodes C and D are in another, a partition occurs if the switch between the two subnets fails. In that case nodes A and B can no longer communicate with nodes C and D.

Some systems are partition-tolerant. This means that even after they are partitioned into multiple sub-systems, they work the same as before.

[mongodb network partition](http://blog.mongodb.org/post/505822180/on-distributed-consistency-part-3-network)

[errors in DB](http://cacm.acm.org/blogs/blog-cacm/83396-errors-in-database-systems-eventual-consistency-and-the-cap-theorem/fulltext)

 
5. 什么是Write-ahead log?

6. unix fsck?

7. fail-stop procesors?

8. clock synchronized? 

9. 
 
