# Intro to CURVE

### What is CURVE?
CURVE is a distributed storage system designed and developed independently by NetEase, featured with high performance, high availability, high reliability and well scalability, and it can serve as a storage base for different scenario (e.g. block storage, object storage and cloud database). During the development of CURVE we stuck to three principles:
- Follow the trend of storage device and infrastructure nowadays to build top-level storage products that coordinate hardware and software perfectly.
- 'Simple can be harder that complex'. As long as we can solve the problem well, we'll choose the most simple way.
- Embrace open source components. We use excellent open source components assessed and tested by us to avoid 'reinventing the wheel'.

So far, we have implemented a high performance block storage system, which supports snapshot, clone and recovery, and can be attached on QEMU virtual machine or physical NBD. CURVE has been served as an elastic block storage inside NetEase for a certain time, during which high performance and reliability have shown.

### CURVE Architecture

### Basic structure

To dig into CURVE, it would be better is we first have an overview on it. CURVE cluster is composed of three core components: MDS, Chunkserver and Client.

![image-20200709165154104](https://opencurve.github.io/image/architecture.png)

#### MDS

MDS serve as the center node of CURVE, responsible for managing metadata and scheduling. Metadata includes topology information, name space data of the file system (e.g. tree-based directory, directory metadata, etc.) and copyset placement data. Scheduling is to monitor the status of the cluster and coordinate their running, including detecting on/offline status and collecting workload data of chunkservers, balancing load of clusters and recovering data from failures. MDS support high availability by leader election of etcd clusters. Data between leader-MDS and follower-MDS will not be synchronized, instead, follower-MDS will restart the service by reloading data from etcd.

#### Chunkserver

A chunkserver is a datanode that store actual file data. The unit of data storing is chunks, while the unit of data managing is copyset, and overwrite is supported. Chunkservers replicate data following Raft algorithm, which is crucial for the consensus of copies and disaster recovery. Copies are managed in copysets, and copysets on different nodes,  which are replicas of each other, composite a Raft group. MDS will manage the load balance between chunkservers, also in unit of copyset.

#### Client

Client provides near-POSIX interface for applications, and implements metadata and file data operations by the interactions with MDS and chunkservers, also, it splits the I/O, operates specified QoS control on IOPS and bandwidth. Hot upgrade is supported on client, without any effect on running services.

### Snapshot & Clone

Snapshot and clone service is provided by SnapShotCloneServer independent of core services. CURVE supports both incremental and full snapshot, and for saving spaces, snapshot of files will be uploaded to S3 clusters.

![image-snap](https://opencurve.github.io/image/architecture_snap.png)

### Core features

#### High performance

High performance is one of the core features of CURVE. For RPC implementation we used [brpc](https://github.com/apache/incubator-brpc), an open-sourced high performance and low latency industrial-grade RPC framework. As for consensus of replicas, we implemented with [braft](https://github.com/baidu/braft), which is an open-source quorum-based Raft algorithm implementation. In the aspect of protocol, quorum mechanism can do better than strong replica consistency model in latency. In our implementation of Raft we optimized in two ways, including implementing a lightweight snapshot of braft and the using of chunkfilepool (specify a certain portion of space for chunk during the initialization of the cluster) on the implementation of state machine to achieve zero write amplification. Also, CURVE implements a finer grain of data address hashing on chunks to achieve read/write splitting and reduce I/O collision, which improve I/O performance furthermore.

#### High availability

High availability is another core feature of CURVE. Multi-instances is supported by MDS, ChunkServer and SnapShotCloneServer, protect the the whole cluster from single point of failure.

- **MDS**

  MDS is stateless, we recommend that at least two instances should be deployed. Multiple MDS will do leader election through etcd, and the switch between instances can be finished within seconds when one of the instance fail. Client and SnapShotCloneServer will retry the request that the failed instance was processing, in order not to affect the availability of the cluster.

- **SnapShotCloneServer**

  Similar to MDS, SnapShotCloneServer also elect for leader by etcd, but it provide services through load balance. Retries on request during the failure are idempotent, without affecting the correctness and availability of the system.

- **ChunkServer**

  Chunkserver is implemented in clusters, and maintain data consensus through Raft protocol, its load balance of is maintained by MDS. When single point of failure happens, every copyset on this chunkserver will be affected. In this case, leader node of a copyset will interrupt its service and wait for reelection. But for those follower nodes, the service will still go on. When a chunkserver fail and not able to recover in a certain period of time, MDS will migrate all its data to another node that functions well.
  

### Project Page

[Github link for source code](https://github.com/opencurve/curve)

### Contact

Wechat group：CURVE沟通交流群

<img src="image/curve-wechat.jpeg" style="zoom: 75%;" />