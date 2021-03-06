# 集群

#### 集群是什么鬼？
- 集群即计算机集群，可以将其堪称一台计算机，这是因为集群内的多个计算机之间通过软件和硬件形成了一个高度紧密协作的整体，可以作为一个整体来高效的提供服务。开发中通常将集群中的一台计算机称为一个节点


#### 常见的集群

###### 高可用性集群
- [高可用性](clusters.md)指计算机可以持续的提供服务的能力，在高可用集群中，一旦**集群中的某一个节点出现故障，那么该服务器上的任务会自动的转移到其他节点上，保证整个服务器集群为外部正常提供服务**

###### 负载均衡集群
- [负载均衡](load_balancing.md)指对于集群中的不同节点服务器而言，他们之间的性能是有区别的，因此需要根据每一个节点的性能状态来有针对性的分配负载。**负载均衡集群中会存在一个或多个负载均衡器，服务端收到的请求会首先通过负载均衡器，然后负载均衡器再将请求按照均衡分配策略分配至指定服务器节点处理**
	- 如果不使用负载均衡器，性能最差的服务器节点可能会出现过载甚至是宕机，而同一集群中性能优越的服务器节点可能只发挥了其性能资源的一小部分，因此要通过的负载均衡集群实现服务器性能资源的最优配置


#### 集群的特点
- 集群中的多个计算机共同工作，提高的服务器的效率
- 高可用性，集群中一个节点出现故障，该节点上的任务可以转移到其他节点上完成
- 负载均衡，实现了服务器资源的优化配置

#### 双机热备
- 又叫双机热备份，双击热备其实就是**用网络将两台服务器链接起来，平时互相备份，共同执行同样的服务当其中一台服务器发生故障宕机的时候，另一台服务器就会接管出现故障的服务器的业务，保证正常的向外提供服务**

###### 三种模式
- 主从模式
	- active服务器处于工作状态，而standby服务器处于监控准备状态，服务器数据包括**数据库数据，同时写入两台服务器或共享数据的磁盘阵列存储系统，保证数据的即时同步。当active服务器出现故障的时候，通过软件诊测或手工方式将standby机器激活，保证应用在短时间内完全恢复正常。**
	- 这种模式和MongoDB中的复制集很相似
- 互备模式
- 并行模式
