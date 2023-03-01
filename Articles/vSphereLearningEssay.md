# VMware vSphere 学习笔记

## 概述

### 相关概念

- 操作系统：Operation System，管理物理资源并将其分配给应用的软件，如 Microsoft Windows
- 应用：Application，提供特定功能的软件，如 Microsoft Office
- 虚拟机应用：Virtual Machine，将硬件资源抽象处理为软件的专用应用
- 客户机：Guest，虚拟机应用抽象出来的计算机
- Hypervisor：提供虚拟化技术的引擎，如 ESXI、Fusion 等
- 主机（宿主机）：Host，为 Hypervisor 提供资源的物理计算机
- vSphere：VNware 提供的服务器虚拟化解决方案，包括 ESXI Hypervisor、vCenter Server 等
- 集群：Cluster，一组可共享资源给虚拟机的 ESXI 主机
- vCenter Server：用于管理 ESXI 集群的专用软件
- vSphere vMotion：一项功能特性，可在不中断服务的情况下进行客户机迁移
- vSphere HA：集群的一个功能特性，支持在集群中某个主机硬件故障后通过剩余主机恢复客户机（主从备份？）
- vSphere DRT：集群的一个功能特性，实现主机间硬件资源的平衡（负载均衡？）

### 资源虚拟化

#### CPU 虚拟化

&emsp;&emsp;与下述虚拟化类型不同，通过软件仿真模拟 CPU 的效率太低，因此对 CPU 的虚拟化仅是对 CPU 资源的重新分配，即硬件辅助虚拟化（Intel VT/AMD-V）。

#### 内存虚拟化

&emsp;&emsp;内存虚拟化的主要工作是对内存地址的映射，使每个客户机的操作系统识别到的内存都从 0 地址开始

#### 网络虚拟化

&emsp;&emsp;一台主机可创建多个虚拟交换机，将交换机的出口桥接到物理网卡上。

#### 存储虚拟化

&emsp;&emsp;ESXI 在物理块设备上部署 VMFS 文件系统，为客户机提供抽象的虚拟磁盘。

#### GPU 虚拟化
