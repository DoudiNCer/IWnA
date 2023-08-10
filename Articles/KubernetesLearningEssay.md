# Kubernetes 学习笔记

&emsp;&emsp;Kubernetes（简称 k8s 或 Kube）是一个开源的 Linux 容器自动化运维平台。

> 前置知识：
>
> - Linux
> - Docker

## 基本概念

### 集群组件

&emsp;&emsp;Kubernates 集群的一个重要的物理单元是节点（Node），可分为负责控制集群的 Master Node 和真正工作的 Worker Node。

&emsp;&emsp;Master Node 包含以下几种组件：

- **API Server**：集群统一入口，对外提供 RESTful API
- **Scheduler**：负责 Worker Node 的调度
- **Controller Manager**：负责集群资源的统一管理

&emsp;&emsp;Worker Node 主要包含以下几种组件：

- **Kubelet**：当前 Node 的管理中心，受 Master Node 调度
- **Kube-Proxy**：负责构建整个集群的网络
- **Docker**

### 核心概念

- **Pod**：一组共享网络的 Container 的集合，是 Kube 集群部署的最小单元。Pod 的生命周期是短暂的
- **Controller**：控制 Pod 的生命周期、部署方式等
- **Service**：定义 Pod 的访问规则