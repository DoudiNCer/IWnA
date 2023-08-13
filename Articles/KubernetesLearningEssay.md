# Kubernetes 学习笔记

&emsp;&emsp;Kubernetes（简称 k8s 或 Kube）是一个开源的 Linux 容器自动化运维平台。

> 前置知识：
>
> - Linux
> - Docker

> 官方文档：https://kubernetes.io/zh-cn/docs/

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

## 集群搭建

&emsp;&emsp;部署 Kube 集群主要有两种方式：Kubeadm 和手动部署二进制程序。

&emsp;&emsp;对于每个节点，都应进行如下操作：

- 关闭 Firewalld：

    ```bash
    sudo systemctl disable firewalld --now
    ```

- 关闭 SELinux：

    ```bash
    sudo setendorc 0
    sudo sed -i 's/enforcing/disables/'/etc/selinux/config
    ```

- 关闭 swap：

    ```bash
    # 传统swap 分区
    sudo swapoff -a
    sudo sed -i '/ swap / s/^/#/' /etc/fstab
    # zram
    sudo swapoff /dev/zram0
    sudo modprobe -r zram
    # Systemd 接管的 swap
    systemctl --type swap
    sudo systemctl mask "dev-XYZ.swap"
    reboot
    ```

- 在 Master 的 Hosts 中添加 Worker 的相关信息

- 加载相关内核模块：

    ```bash
    cat > /etc/modules-load.d/k8s.conf << EOF
    overlay
    br_netfilter
    EOF
    # 验证
    lsmod | grep br_netfilter
    lsmod | grep overlay
    ```

- 将桥接的 IPv4 流量传递给 iptables 的域:

    ```bash
    cat > /etc/sysctl.d/k8s.conf << EOF
    net.bridge.bridge-nf-call-iptables  = 1
    net.bridge.bridge-nf-call-ip6tables = 1
    net.ipv4.ip_forward                 = 1
    EOF
    # 不重启生效
    sudo sysctl --system
    # 验证
    sysctl net.bridge.bridge-nf-call-iptables net.bridge.bridge-nf-call-ip6tables net.ipv4.ip_forward
    ```

### Kubeadm

&emsp;&emsp;Kubeadm 是 Kubernetes 社区推出的用于搭建 Kube 集群的工具，操作简单快速，受到广泛使用。

&emsp;&emsp;对于每个节点，安装 `docker`、`kubeadm`、`kubelet`和`kubectl`并进行`systemctl enable kubelet --now`

&emsp;&emsp;在 Master Node 初始化集群：

```bash
kubeadm init \
  # 当前节点的 IP 地址
  --apiserver-advertise-address=192.168.31.61 \
  # 镜像地址
  --image-repository registry.aliyuncs.com/google_containers \
  --kubernetes-version v1.17.0 \
  --service-cidr=10.96.0.0/12 \
  --pod-network-cidr=10.244.0.0/16
```

&emsp;&emsp;在 Master Node 完成基本配置：

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

&emsp;&emsp;在 Master Node 查看集群中的 Node：

```bash
kubectl get nodes
```

&emsp;&emsp;将 Worker Node 加入集群：

```bash
kubeadm join xxx:xxx --token xxx.xxx \
  -- discovery-token-ca-cert-hash xxx
```

&emsp;&emsp;默认 Token 有效期为 24 小时，过期后可在 Master Node 重新生成：

```bash
kubeadm token create --print-join-command
```

&emsp;&emsp;在 Master Node 部署 CNI：

```bash
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
```

### 手动部署

&emsp;&emsp;首先部署ETCD

## 集群操作

&emsp;&emsp;操作 Kube 集群的主要工具是`kubectl`，其命令格式为`kubectl [command] [TYPE] [NAME] [flags] `。

> 0. 更多信息参考https://kubernetes.io/zh-cn/docs/reference/kubectl/

&emsp;&emsp;为了使操作可以复用，可以编写资源清单文件。资源编排文件采用 YAML 格式如：

```yaml
# API 版本
apiVersion: apps/v1
# 资源类型
kind: Deployment
# 元数据
metadata: 
    name: nginx-deployment
    namespace: default
# 志愿规格
spec: 
    # 副本数量
    replicas: 3
    # 标签选择器
    selector: 
        matchLabels: 
            app: nginx
    # Pod 模板
    template: 
        metadata: 
            lanels: 
                app: nginx
        # Pod 规格
        spec: 
            # 容器元数据
            containers: 
                - name: nginx
                
```

&emsp;&emsp;可以通过`kubectl create` 的 `-o yaml --dry-run` 生成 资源清单文件而不进行实际操作，也可以使用`苦kubectl get` 的 `-o=yaml --export` 导出资源清单文件。如：

```bash
kubectl create deployment web --image=nginx -o yaml --dry-run
kubectl get deploy nginx -o=yaml --export
```

