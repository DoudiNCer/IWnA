# Docker学习笔记

## Docker介绍

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
" > /etc.docker/daemon.json
```

