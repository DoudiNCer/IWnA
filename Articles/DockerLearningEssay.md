# Docker学习笔记

感谢**BV1og4y1q7M4**

> 弱小和无知不是h生存的障碍，傲慢才是。——刘慈欣《三体》

## Docker专业术语

> - **镜像(Image)**：一套最小的Linux系统根目录，用于生成容器
> - **容器(Container)**：镜像的一个“实例”，可以被创建、启动、暂停、停止、删除等
> - **仓库(Repository)**：一个存储镜像的地方


## Docker在Linux下的安装和配置

（物理环境为Arch Linux，参考[ArchWiki](https://wiki.archlinux.org/title/Docker)）

（不熟悉的命令自觉man一下）

```shell
paru -S docker
sudo docker info				# 验证安装
sudo gpasswd -a ${USER} docker	# 将当前用户加入到docker组，选做
sudo systemctl start docker
sudo systemctl enable docker
sudo echo "
{
	"registry-mirrors": ["http://hub-mirror.c.163.com"]
}
" > /etc/docker/daemon.json
								# 配置Docker仓库
```

一些疑难杂症：

### 启用IP转发

```shell
sudo sysctl net.ipv4.ip_forward	# 如果输出结果为0，执行下面的操作
sudo echo "
net.ipv4.ip_forward=1
" >> /etc/sysctl.d/99-net.conf
```

## Docker命令

### 帮助&信息

```shell
docker info
docker ($command) --help
```

或者查看[Docker Docs](docs.docker.com)

### Images

docker images:

```shell
docker images -a 							# 显示所有镜像
              -q 							# 仅显示镜像ID
```

```shell
docker pull $imageName(:$imageTag)			# download a Image,default is the latest version
docker rmi -f $imageName:$imageTag/$imageID	# delete inage(s)
```

### Container

docker run:

```shell
docker run --name=$containerName			# container name
           -d							# Run container in background and print container ID
           -it							# Interactive(STDIN avalible and a tty)
(-p(($hostIP)$hostPort:))$containerPort	# Open container's port to the host
           -P							# Publish all of the container's ports
```

docler ps:

```shell
docler ps -a							# show all of the container(even if it's not running)
          -n=$number					# show the latest $number container(s)
          -q							# Only show the containers' ID
          
```

```shell
docker rm (-f） $containerName/$containerID	# remove container(s) (force?)
docker start/stop/restart/kill $containerID	
```

## DockerFile

## Docker网络原理

## Docker Compose

## Docker Swarm

## CI/CD Jenkins
