# Linux 学习笔记

&emsp;&emsp;终于，我还是和i来系统性学习 Linux 了。

> - 我并不是零基础学习 Linux，这篇笔记可能省略了一些入门知识，请自行找资料学习

## 名言警句

- Troubleshooting any problem without the error log is like driving with your eyes closed.
- Remember, a few hours of trial and error can save your several minutes of looking at the README.
- 执行从其他地方看到的命令前，问问自己，这是在干啥？

## 找男人

> - 找男人能解决的事都不是事儿。
> - 不要跟我杠为什么不是“找女人”，杠就是你对了。

&emsp;&emsp;Linux 的命令辣么多，难道要把这些命令的用法都记住？当然不是！当我们需要使用一个没见过的命令或不熟悉的命令时，可以查询命令内置的帮助，一般是`-h`或`--help`，这些帮助一般很简洁实用。

&emsp;&emsp;多数可执行程序、库、系统函数和规范等都有自己的手册（ manual ），可以通过`man`命令查看。比如查看 man 的使用手册，可以使用：

```shell
man man
```

## SSH

> &emsp;&emsp;Secure Shell (SSH) is a cryptographic network protocol for operating network services securely over an unsecured network. Typical applications include remote command-line login and remote command execution, but any network service can be secured with SSH.

&emsp;&emsp;对于 Linux 运维，SSH 一般用于远程登录。

&emsp;&emsp;SSH 协议有多种实现，这里介绍常用的 OpenSSH 。

> 关于 OpenSSH 和 SSH Keys ，可参考ArchWiki相关页面 [OpenSSH](https://wiki.archlinux.org/title/OpenSSH) 和 [SSH Keys](https://wiki.archlinux.org/title/SSH_keys) 。

### 客户端

&emsp;&emsp;客户端登录较为简单，这里介绍使用`ssh`命令登录，当然你可以使用其他工具进行登录，如 putty 等。

```shell
ssh [username]@hostname[:port]
# ssh 默认使用 22 端口
```

&emsp;&emsp;客户端的配置文件为`~/.ssh/config`，在这里，可以配置常用的服务器和全局信息，如：

```ssh
# global options
User user

# host-specific options
Host myserver
    HostName server-address
    Port     port
```

&emsp;&emsp;关于客户端的配置文件，可参考手册`ssh_config(5)`和`/etc/ssh/ssh_config`这个充满注释的全局（相对于用户）配置文件。

### 服务器端

&emsp;&emsp;服务器端的配置文件为`/etc/ssh/sshd_config`。

#### sshd服务

&emsp;&emsp;OpenSSH 包括了两种 systemd 服务:

> - sshd.service：使 SSH 守护进程始终运行，并为每个入站连接创建子进程。适用于有大量 SSH 流量的系统。
> - sshd.socket + sshd@.service： 为每个连接生成 SSH 守护进程的实例。它意味着让 systemd 监听 SSH socket，并且只有在有连接传入时启动守护进程。几乎所有情况下都推荐使用sshd。

&emsp;&emsp;start 并 enable sshd.service 或 sshd.socket 中的任何一个都可以启动守护进程。

### SSH Key

&emsp;&emsp;相对于让网管头大且不是非常安全的密码登录，现在更流行使用密钥登录。

&emsp;&emsp;SSH 密钥都是成对生成的，其一称为公钥，另一则称为私钥。私钥只由您所知，必须安全保管。相对地，公钥可以向您想连接的任何 SSH 服务器自由地共享。

&emsp;&emsp;使用`ssh-keygen`生成密钥对，如：

```shell
ssh-keygen -t rsa -b 4096 -C "$(whoami)@$(uname -n)-$(date -I)"
# 默认为2048位的rsa
```

&emsp;&emsp;生成密钥对后，将私钥（默认为`~/.ssh/id_rsa`）放到自己的电脑上相同位置或使用客户端配置文件为要连接的服务器指定私钥；将公钥（默认为`~/.ssh/id_rsa.pub`追加到服务器要登录的用户的`~/.ssh/authorized_keys`文件中并确保其权限大于等于600：

```shell
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

