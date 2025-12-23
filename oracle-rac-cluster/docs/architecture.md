# Oracle RAC 架构概览

本节提供一个参考级别的 Oracle RAC 部署拓扑：

- 多节点（2 或 3 节点）Oracle RAC，每个节点运行 Oracle 实例
- 共享存储：使用 iSCSI/NFS 或 vSphere 的共享磁盘（如 RDM / vSAN）用于 ASM 磁盘组
- 私有网络：用于集群内部通信与 ASM
- 公共网络：客户访问数据库服务

注意：实际细节（存储类型、网络划分、IOPS 要求）取决于业务负载与目标平台，部署前请基于性能需求进行容量规划。
