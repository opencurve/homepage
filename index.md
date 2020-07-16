# CURVE 简介

### CURVE是什么

CURVE是网易自主设计研发的高性能、高可用、高可靠分布式存储系统，具有非常良好的扩展性。基于该存储底座可以打造适用于不同应用场景的存储系统，如块存储、对象存储、云原生数据库等。CURVE 的设计开发始终围绕三个理念：一是顺应当前存储硬件设施发展趋势，做到软硬件结合打造顶级的存储产品；二是秉持 "Simple Can be harder than complex"，了解问题本质情况下选择最简单的方案解决问题；三是拥抱开源，在充分调研的前提下使用优秀的开源项目组件，避免造轮子。

当前我们基于CURVE已经实现了高性能块存储系统，支持快照克隆和恢复 ,支持QEMU虚拟机和物理机NBD设备两种挂载方式, 在网易内部作为高性能云盘使用。

### CURVE架构

### 基本架构

要深入了解 CURVE 首先要了解 CURVE 整体架构。CURVE集群主要包括三个核心组件：MDS、Chunkserver、Client。

![image-20200709165154104](image/architecture.png)

#### MDS

MDS是中心节点。它有两方面职责：一是存储管理元数据信息，包括系统的拓扑信息、文件系统的Namespace ( 树形目录结构，文件和目录，目录元信息等 ) 、Copyset ( Raft 复制组) 位置信息。二是感知集群状态并进行合理调度，包括感知Chunkserver上下线、收集Chunkserver负载信息、集群负载均衡与故障修复。MDS通过Etcd进行选主实现高可用，Leader-MDS 和 Follower-MDS并不进行数据同步，Leader-MDS挂掉之后，Follower-MDS从Etcd加载数据后再启动服务。

#### Chunkserver

Chunkserver是数据节点，负责数据存储。数据存储的最小单元是 chunk，支持覆盖写，管理数据存储的基本单位是Copyset。Chunkserver使用 Raft 协议做复制，保持数据的一致性和容灾。副本以 Copyset 为单位进行管理，不同节点上的多个 Copyset 构成一个 Raft Group，互为副本。数据在多个 Chunkserver 之间的负载均衡由 MDS 调度，是以 CopySet 为单位进行调度。

#### Client

Client是客户端，向应用提供类Posix文件系统接口，与MDS交互实现对元数据的增删改查，与Chunkserver交互实现对数据的增删改查，对io进行切分，对IOPS和带宽进行指定的QoS控制。Client还支持热升级，可以在用户无感知的情况下进行底层版本变更。

### 快照克隆

CURVE块存储系统中快照克隆子系统是独立于CURVE核心服务的，快照克隆操作由单独的SnapShotCloneServer进行处理，用户创建的快照会上传到S3集群以节约存储空间，并且支持增量和全量两种快照方式。

![image-snap](image/architecture_snap.png)

### 核心特性

#### 高性能

高性能是 CURVE 的一大特点，也是我们创建CURVE项目的初衷。RPC 层面 CURVE 采用了高性能和低延迟并且已开源的 [brpc](https://github.com/apache/incubator-brpc)；在一致性层面 选择了基于 quorum 机制并且开源的 [braft](https://github.com/baidu/braft)，从协议层面来说 quorum 机制在延迟方面天生优于多副本强一致的方式。实现上CURVE 对 braft 快照的实现进行了优化，在状态机的实现上采用 chunkfilepool 的方式 ( 初始化集群的时候格式化出指定比例的空间用作 chunk ) 使得底层的写入放大为 0；此外CURVE还在chunk上进行更细力度的地址空间hash以达到读写分离、减小 IO碰撞等的效果，从而进一步提升IO性能。

#### 高可用

高可用是CURVE的另一大特点。MDS、ChunkServer 以及 SnapShotCloneServer都支持多实例部署，部分实例异常不影响整个集群的可用性。

- **MDS** 

  MDS是无状态的，推荐至少部署两个实例。通过Etcd进行选主。多个MDS实例通过Etcd进行选主，当单个实例失效时，可以秒级切换到另外一个实例。失效实例上正在处理的请求，Client和SnapShotCloneServer都会对其进行重试，以达到不影响集群可用性的效果。  

- **SnapShotCloneServer**

  SnapShotCloneServer与MDS类似, 也是通过Etcd进行选主，不同的是，它通过负载均衡对外提供服务。失效期间的请求失败重试都是幂等的，不影响任务的正确性以及集群的可用性。

- **ChunkServer**

  ChunkServer是一个集群，通过Raft协议保持数据一致性，并通过MDS做负载均衡。单个节点失效时，会影响到这个节点上存储的所有Copyset。对于Copyset上的Leader节点，会中断服务，等待重新选举；对于Copyset上的follower节点，服务不会受影响。当某个Chunkserver节点失效且在一段时间内无法恢复，MDS会将其上的数据迁移到其他节点上。

### 项目地址

[github源码地址](https://github.com/opencurve/curve)

### 联系方式

微信交流群：CURVE沟通交流群

<img src="image/CURVE-wechatgroup.jpeg" style="zoom: 25%;" />

