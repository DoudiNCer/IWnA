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

## Docker网络原理

## Docker Compose

## Docker Swarm

## CI/CD Jenkins
