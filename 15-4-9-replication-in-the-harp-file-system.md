
1. 什么是"primary copy replication"?

   多个server中有一个是primary, 其余都是backup. 这一组server用于存储file的副本. 客户端首先和primary通信,然后primary和其余的backup通信.

  可参考文献:

 [1] Viewstamped Replication: A New Primary Copy Method to Support Highly-Available Distributed Systems.

2. "replication"可以提高可用性("availability")和响应时间("response time")

  可用性是指在server失败时,用户仍然可以访问server. 复制技术保存多个备份,可以容忍失败.复制技术可以让用户尽快访问到你自己最近的数据.

  加快响应时间的同时,也需要想办法保证数据的一致性.

  Coda和Locus两个文件系统使用复制技术来提高可用性,同时加快响应时间,  当他们没有很好的解决一致性问题

3. Harp 使用replicaiton只是用于提高可用性和可靠性

4. 什么是"partition"? 分布式系统中经常会遇到partition的问题.

  来自wikipedia的[解释](http://en.wikipedia.org/wiki/Network_partition):

  A network partition refers to the failure of a network device that causes a network to be split.

  For example, in a network with multiple subnets where nodes A and B are located in one subnet and nodes C and D are in another, a partition occurs if the switch between the two subnets fails. In that case nodes A and B can no longer communicate with nodes C and D.

  Some systems are partition-tolerant. This means that even after they are partitioned into multiple sub-systems, they work the same as before.

  [mongodb network partition]  (http://blog.mongodb.org/post/505822180/on-distributed-consistency-part-3-network)

  [errors in DB](http://cacm.acm.org/blogs/blog-cacm/83396-errors-in-database-systems-eventual-consistency-and-the-cap-theorem/fulltext)

5. 什么是Write-ahead log?

  Bernstein, P. A., Hadzilacos, V., and Goodman, N..
Concurrency Control and Recovery in Database Systems.
Addison Wesley, 1987. (值得一看)

6. unix fsck?

   The system utility fsck (for "file system consistency check") is a tool for checking the consistency of a file system in Unix and Unix-like operating systems, such as Linux and Mac OS X.[1] (Wikipedia)

7. fail-stop procesors?

   A fail-stop processor never performs an erroneous state transformation due to a failuer. Instead, the processor halts and its state is irretrievably lost. The contents of stable storage are unaffected by any failure. 
    
8. clock synchronized? 

   Clock synchronization is a problem from computer science and engineering which deals with the idea that internal clocks of several computers may differ. Even when initially set accurately, real clocks will differ after some amount of time due to clock drift, caused by clocks counting time at slightly different rates. (Wikipedia)

9. View change algorithms

  参考: [Maintaining Availability in Partitioned Replicated Databases](http://www.cs.rice.edu/~alc/old/comp520/papers/Avail-Databases.pdf)

10. 

  Harp使用2n+1个servers来容忍n个servers同时失效,以此持续地向用户提供服务. 为什么是2n+1? 我想是因为要保证在n个server失效的时候还能做到大多数的(majority)的server能够达成一致性地意见.

  但是Harp只存储n+1个副本,Harp认为这样就可以保证n个server失效的话,信息不会丢失. 另外n个server不存储副本,但是要参与view change算法, 确保只有一个新的view被选中,即使是有network partition的情况下. 这些额外的n个server被叫做witness. 这种说法来自[Voting With Witnesses: A Consistency
Scheme for Replicated Files](http://www2.cs.uh.edu/~paris/MYPAPERS/Icdcs86.pdf)

  Primary和backup都在内存中维持一个log，保存修改操作的事件序列。而且都使用索引变量commit point(CP)来表示在CP之前的事件已经被commit了。

  Primary接收到客户端的操作请求后，Primary创建一个事件记录(event record)，将该事件记录append到log中，然后将日志信息发送给backup，backup接收到之后，也将事件记录append到log上，并将确认信息发送给Primary,在确认信息中,告知Primary目前已经收到索引n之前的所有事件记录.当Primary收到来自backup的告知确认信息后,Primary向前移动CP,提交事件.Primary将CP值发送给backup,backup也维持一个CP,存储着接收到的最大的CP.

  被commit的事件记录通过一个单独的进程:apply process来执行.Apply进程使用异步Unix文件系统操作来执行事件记录中的读写操作.Apply进程也维持一个索引指针 AP(Application point),记录着该进程的进度. 由于使用异步文件操作,所以Harp还使用了另外一个进程来追踪Apply进程执行的操作是否结束, 并维持一个索引指针:LB(Lower bound), <=LB的事件记录是全部作用到硬盘上的文件系统上了. 

  backup和primary一样,维持有LB,AP,CP指针,并且Primary和backup互相将自己的LB指针发送给对方.Primary和backup都维持一个GLB(Global LB)指针,GLB是Primary和backup之间最小的LB值, <=GLB的Log项都被删除掉.

  
 