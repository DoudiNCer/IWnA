# Docker学习笔记

感谢**BV1og4y1q7M4**，**BV1kv411q7Qc**

> 弱小和无知不是生存的障碍，傲慢才是。——刘慈欣《三体》

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
docker run --name=$containerName		# container name
           -d							# Run container in background and print container ID
           -it							# Interactive(STDIN avalible and a tty)
(-p(($hostIP)$hostPort:))$containerPort	# Open container's port to the host
           -P							# Publish all of the container's ports
           --rm							# auto delete the container after stop
           -e $envKey=$envValue			# add a environment variable
```

> Tips:"-d" need a running foreground program!!

docler ps:

```shell
docler ps -a							# show all of the container(even if it's not running)
          -n=$number					# show the latest $number container(s)
          -q							# Only show the containers' ID
```

```shell
docker rm (-f） $containerName/$containerID	# remove container(s) (force?)
docker start/stop/restart/kill $container
docker attach $container					# show the background container
docker exec $container $command				# exec a new command in the container
docker cp $container:$srcPath $machinePath	# copy file(s)
docker stats $container						# show container's resource usage
```

### Logs

```shell
docker logs -tf --tail $number $container	# show logs with timestamps and fresh
docker inspect $container					# show container's detail information
```

累了？下个portainer玩玩吧

```shell
docker volume create portainer_data
docker run -d -p 8000:8000 -p 9443:9443 --name portainer \
    --restart=always \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v portainer_data:/data \
    cr.portainer.io/portainer/portainer-ce:2.9.3
```

### Commit

```shell
docker commit (-m=$commitMessage) (-a=$author) $container ($repository(:$tag))
# commit a container as a image
```

## 容器数据卷

（前方高能）

### Docker存储原理

> &emsp;&emsp;Docker的数据采用层状存储模式，构建镜像时不断向上叠加新的只读层，创建容器时在镜像的基础上添加一个读写层，对容器的所有操作对视在这个读写层上进行的。
>
> &emsp;&emsp;新文件写入时，若该文件不存在，则按需在宿主机上分配空间（allocate-on-demand）；若同名文件位于只读层，则从只读层复制该文件的副本到读写层并隐蔽原文件（copy-on-write）；若要删除只读层色文件，则在读写层添加一个删除标记。
>
> &emsp;&emsp;当执行commit命令时，会将当前的读写层转换为只读层并添加新的读写层。
>
> &emsp;&emsp;Docker的层状存储结构依靠存储驱动维护，早期为AUFS，现在为Overlay2。当然，根据宿主机使用的文件系统不同。Docker可以使用的存储引擎也不同。
>
> &emsp;&emsp;下文提到的数据卷不通过存储驱动，直接挂载到读写层中，以实现数据持久化。

数据卷的作用：

- 数据持久化
- 不同容器间数据同步

### Mount by command

```shell
docker run -v ($localVolumnName:)$containerDirectory(:ro/rw)
# Local data is /var/lib/docker/volumns/$localVolumnName/_data
			-v $localDirectory:$containerDirectory
			--volumes-from $container	# share another vontainer's file
```

### Volume operation

```shell
docker volume create 
			inspite		# show volume detail info
			ls
			prune		# clean unused volumes
			rm
```

## Dockerfile

> Dockerfile是一个用来构建Docker**镜像**的文本文件，通过``docker build -f``来构建

### Dockerfile语法

```dockerfile
FROM scratch				# 基础镜像
MAINTAINER auther<e-mail>	# 维护者信息

RUN exec					# 构建过程运行的命令，每个RUN会给镜像增加一层
ADD src dest				# 提供镜像所需文件（可通过URL或tar包提供）
COPY src dest				# 复制本地文件到docker镜像中
VOLUME dir					# 添加数据卷

USER daemon					# 指定容器运行时使用的用户
WORKDIR	dir					# 工作目录，给cd改了个名
EXPOSE [ports...]			# 对外暴露端口
CMD exec					# 容器运行时执行的命令，会被docker run覆盖，仅最后一个有效
CMD ["executable",params...]
ENTRYPOINT exec				# 容器运行时运行的命令，不会被docker run覆盖，仅最后一个有效，可追加参数
ENTRYPOINT ["executable",param1...]
ONBUILD	exec				# 被继承时触发执行
ENV name value				# 添加环境变量
```

### 查看镜像/容器变更历史

```shell
docker history $name
```

### 构建镜像

docker build:

```shell
docker build -f $dockerfile -t $imageName:$tag $workDir
```

### 发布镜像

```shell
docker login
docker push
docker pull
docker logout
```

### 备份/恢复镜像

```shell
docker save
docker load
```

## Docker网络

（前方高能）

### Docker网络原理

> &emsp;&emsp;Docker通过veth-pair技术为每个容器配置了一对虚拟网卡，用于容器和宿主机之间连接。默认情况下宿主机端的网卡以docker作为网关并由docker0调度通信。
>
> &emsp;&emsp;默认情况下，宿主机通过docker0与容器接入一个虚拟局域网内，容器通过暴露端口和端口映射使外部设备可通过宿主机指定端口访问容器。

### Docker网络配置

&emsp;&emsp;在默认情况下，可通过--link绑定一对容器以实现容器名访问对方其本质为修改容器的hosts文件。该方法如今已很少使用。

#### **Docker的网络连接模式**：

在创建容器时通过"--network="指定所用的网络连接模式

> - **bridge**：默认的桥接模式，通过docker0连接宿主机和各个容器（默认）
> - **host**：容器和宿主机共享网络
> - **none**：容器不具有网络连接
> - **container**：和指定的容器共享网络，使用 --net=container:$container 指定
> - 各种自定义模式

docker neiwork：

```shell
docker network connect $network $container		# Connect a container to a network
			disconnect (-f) $network $container	# Disconnect a container from a network
			inspect $networks...				# Show details of neiwork(s)
			ls
			prune								# Clean unused network(s)
			rm $neywork

			create --drive $driver 
			--subnet $subnet		# subnet address and mask
			--getway $getway		# host address
			$networkName						# Create a network
```

## Docker Compose

> Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application’s services. Then, with a single command, you create and start all the services from your configuration. To learn more about all the features of Compose, see [the list of features](https://docs.docker.com/compose/#features).
>
> Compose works in all environments: production, staging, development, testing, as well as CI workflows. You can learn more about each case in [Common Use Cases](https://docs.docker.com/compose/#common-use-cases).
>
> Using Compose is basically a three-step process:
>
> 1. Define your app’s environment with a `Dockerfile` so it can be reproduced anywhere.
> 2. Define the services that make up your app in `docker-compose.yml` so they can be run together in an isolated environment.
> 3. Run `docker compose up` and the [Docker compose command](https://docs.docker.com/compose/cli-command/) starts and runs your entire app. You can alternatively run `docker-compose up` using the docker-compose binary.

### 安装

```shell
 sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
 sudo chmod +x /usr/local/bin/docker-compose
```

### docker-compose,yaml

```yaml
version: '3'	# compose 版本，参照 https://docs.docker.com/compose/compose-file
services: 
	serviceName: 
		build: 					# 指定build命令参数
		image: imageName
		container_name: cName	# 不指定的话为“目录名_服务名_1”
		environment: 
			KEY: "VAlue" 
		ports: 
			- "hostPort:clientPort"
		volumes:
			- hostDir: containerDir
		networks: 
			- docker network
		depends_on: 
			- anotherService	# 在启动该服务前启动以下服务
		...						# 参考docker命令可用的参数
networks:
	neyworkName: 	
...		# 其他配置
```

### 运行

```shell
docker-compose 
				up (-d)		# 创建并启动服务（后台）
				down		# 停止并删除服务
				start
				stop
				restart
				exec 		# 执行容器内程序
				config (-q)	# 检查配置文件（仅输出错误）
```

## Docker Swarm

## CI/CD Jenkins
