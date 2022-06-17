# Linux 学习笔记

&emsp;&emsp;终于，我还是来系统性学习 Linux 了。

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

```bash
man man
```

## 用户与权限

### 用户和组

&emsp;&emsp;用户的信息存储在`/etc/passwd`中，每一行表示一个用户，格式为：

```
用户名:密码:UID:GID:描述:家目录:登录 shell
```

> - 此处密码为处理后保存，为空时可不使用密码登录；为`x`时表示存储在`/etc/shadow`中；以“!”开头表示禁止使用该密码登录。

&emsp;&emsp;Linux 中使用 UID 和 GID 表示一个用户或组，可使用`id`命令查看一个用户的 UID 等信息。UID的范围划分如下：

> - 0： 系统管理员
> - 1-999： 用于Linux和其他软件的用户，一般不可登录
> - 1000及以上： 用于普通用户

&emsp;&emsp;为了安全起见，一般把账户的密码保存在`/etc/shadow`中，这个文件每一行包括：

> - 用户名
> - 处理后的密码
> - 上次更新密码的日期
> - 最小密码年龄
> - 最大密码年龄
> - 密码过期警告时间段
> - 密码失效宽裕期
> - 账户过期时间
> - 保留

&emsp;&emsp;可使用`passwd`命令修改自己或其他用户（需要是 root ）的密码。

&emsp;&emsp;每个组的信息保存在`/etc/group`中，其每一行包括组名、组管理员密码、 GID 和组成员的用户名。与`/etc/passwd`类似，密码用“x”表示密码储存在`/etc/gshadow`中。

&emsp;&emsp;可使用`newgrp`命令以一个已经加入的组的身份登录到另一个 shell 中。

### 用户和组的管理

&emsp;&emsp;可以使用`useradd`来添加用户，用`userdel`来删除用户，使用`usermod`修改创建用户时指定的信息，用`passwd`修改密码，用`chage`修改密码相关配置：

```bash
  useradd -s /usr/bin/zsh -mG wheel,audio,video,optical,storage doudi
  passwd doudi
```

&emsp;&emsp;创建用户时的默认参数由`/etc/default/useradd`提供，可使用`useradd -D`查看；用户家目录的模板为`/etc/skel`，可通过`-k`参数指定；用户密码的默认参数由`/etc/login.defs`提供。

&emsp;&emsp;类似用户，管理用户组可以使用`groupadd`、`groupdel`、`groupmod`、`gpasswd`，还可由组管理员使用`groupmems`管理用户组。

### 文件与目录的权限

&emsp;&emsp;通过`ls -l`可查看到文件与目录的权限信息，如 `/bin`的权限为`drwxr-xr-x`，第一位为文件类型，其余九位分三组，分别对应文件所有者（ u ）、文件所在组（ g ）和其他用户（ o ）的读（ r ）、写（ w ）和执行（ x ）权限。为了更方便的表示权限，可将权限信息用一个三位八进制数表示，如上述的`rwxr-xr-x`为`755`。

> - 目录的“执行”权限指可以“进入”该目录并获得其内容的属性信息

&emsp;&emsp;可通过`chmod`修改文件或目录的权限，可通过上述权限代码覆盖文件的权限或使用`+`或`-`设置每一位权限，如：

```bash
# 设置权限
chmod 400 ~/.ssh/id_rsa
# 修改权限
chmod go-r,a-w ~/.ssh/id_rsa.pub
```

> - 命令`lsmod`用于查看内核启用模块的信息

&emsp;&emsp;除此之外，还有三位不常用的特殊权限 SUID 、 SGID 和 SBIT：

> - SUID： Set UID ，文件所有者和执行者对该文件有 r 和 x 权限且时，设置 SUID 的文件被执行时执行者暂时取得文件所有者的权限（仅对二进制文件有效）
> - SGID： Set GID ，与 SUID 类似，使用者可暂时进入该文件所在组（可用于目录）
> - SBIT： Sticky Bit ，仅对目录有效，sypher对该目录有 w 和 x 权限时，可在该目录下创建属于自己的文件和目录且仅自己和root有权删除。

&emsp;&emsp;SUID 和 SGID 为设置在文件所有者和文件所在组 x 位上的的 s 权限， SBIT 为设置在其他用户 x 位上的 t 权限。

&emsp;&emsp;创建文件（0666）或目录（0777）时使用`umask`来确定默认权限，umask表示创建目录或文件时“拿掉”的权限，如当前的 umask 为022，则创建一个普通文件时权限为`0666 & (0666 ^ 0022) = 0644`。

&emsp;&emsp;使用`umask`命令来查看或修改当前的 umask 。

### ACL

&emsp;&emsp;ACL （Access Control Lists），访问控制列表，提供了一套针对单一用户、单一文件的权限控制系统。

&emsp;&emsp;使用`gerfacl`查看文件或目录的 ACL 权限配置信息，如：

```bash
getfacl ./test
# OUTPUT 
1:  # file: somedir/
2:  # owner: lisa
3:  # group: staff
4:  # flags: -s-
5:  user::rwx
6:  user:joe:rwx               #effective:r-x
7:  group::rwx                 #effective:r-x
8:  group:cool:r-x
9:  mask::r-x
10:  other::r-x
11:  default:user::rwx
12:  default:user:joe:rwx       #effective:r-x
13:  default:group::r-x
14:  default:mask::r-x
15:  default:other::---
```

&emsp;&emsp;其中前四行为文件名称、所有者、所在组及三个特殊权限；5、7、10为上一节中的权限；6、8为针对某一用户或组的权限；9为该文件或目录的最高权限；11-15行为配置了默认权限的目录中文件或目录的默认权限。

&emsp;&emsp;使用`setfacl`修改文件或目录的 ACL 权限配置信息：

```bash
setfacl
        -m|-x   # 添加/删除指定的ACL配置
        -b|-k   # 删除所有/默认的ACL配置
        -R      # 递归设置
        -d      # 设置默认的ACL配置
```

&emsp;&emsp;ACL 参数的格式为`[d[efault]:]类型:[对象:]权限`，“类型”包括`u`[ser]（针对用户）、`g[roup]`（针对用户组）、`m[ask]`（最大权限）和`o[ther]`。

### 用户切换

&emsp;&emsp;为了安全起见，一般使用低权限用户登录系统，执行某些操作时才切换到 root ，su和sudo就是用来切换用户的。

#### su

&emsp;&emsp;`su`是一个很普通的切换用户的命令，默认为使用非登录 shell 切换到root账户，低权限到高权限需要输入目标账户的密码。

#### sudo

&emsp;&emsp;为了提高效率，可将一些用户指定为系统管理员，允许 TA 们使用sudo来获取root权限。直接使用`sudo command`并输入当前用户的密码即可。

&emsp;&emsp;sudo 的配置文件为`/etc/sudoers`和`/etc/sudoers.d/`，为了保证配置文件格式正确，建议使用`visudo`来编辑配置文件：

```shell
# 用户名 登录主机=（可切换的用户） 可执行的命令
root ALL=(ALL) ALL
# %组名 登录主机=（可切换的用户）无需密码 可执行的命令
%wheel ALL=(ALL) NOPASSWD: ALL
```

## Systemd

### System V

&emsp;&emsp;在 Linux 系统上，有一些需要始终在后台运行的提供服务（service）的进程，叫做守护进程（daemon），通常以服务名+d命名，如`sshd`等。

&emsp;&emsp;在早期，这些守护进程由纯粹的 system V 管理，每个守护进程通过`/etc/init.d`下的启动脚本（rc）来启动（`start`）、停止（`stop`）、重新启动（`restart`）和查看状态（`status`）。或将这些 daemon 交给超级守护进程`inied`或`xinted`，在需要的时候启动。

### Systemd

&emsp;&emsp;从 CentOS 7 和 RHEL 7 开始，红帽（Red Hat）大力推行 systemd 以取代 System V，现在 systemd 已被多数 Linux 发行版采用。

### systemctl

&emsp;&emsp;操作 systemd 的主要命令是`systemctl`，其常见用法如下：

```bash
systemctl                   
            status			# 系统状态，后加单元可查看单元状态，使用 --failed 参数查看系统中失效的单元
            list-units      # 列出正在运行的但愿（也可直接 systemctl ）
            list-unit-files # 查看已安装的单元
            status          # 后接 pid ，查看该pid对应的进程的状态
            help            # 后接单元，查看该单元的帮助
            # 操作单元
            is-enabled
            is-active
            is-failed
            start
            stop
            restart
            try-restart         # 仅重启正在运行的单元
            reload
            kill                # 发送 kill 信号
            daemon-reload       # 重新加载 systemd 配置
            list-dependencies   # 查看依赖关系，使用 --reverse 参数反向查找（依赖该单元的单元）
            # 自动启动管理
            enable
            disable
            enable --now    # 相当于 enable + start
            reenable        # 相当于 disable + enable
            # 拉黑/取消拉黑
            mask
            unmask
            # 电源管理
            reboot
            poweroff
            susbend         # 休眠到内存
            hibernate       # 休眠到硬盘
            hybrid-sleep    # 混合休眠
```

> - 可通过`-H user@host`借助 ssh 管理远程主机/
> - 默认为管理当前系统，可通过`--user`管理用户态的 systemd

### 单元文件

&emsp;&emsp;Systemd 使用带元（unit）来管理每个事务，单元文件存储在`/run/systemd/`、`/usr/lib/systemd/system/`（软件包提供的）或`/etc/systemd/system/`（管理员创建的单元文件）中，具体可参考 [systemd.unit(5)](https://man.archlinux.org/man/systemd.unit.5) 。

&emsp;&emsp;根据行为的不同，单元文件有不同的类型，通过其扩展名区分。以下为一些常用的单元文件扩展名：

- service：一般的服务
- socket：内部程序数据交换使用的套接字，如让一个服务在需要时才启动。
- target：执行环境类型，即下一界中提到的目标，是一组 unit 的集合
- mount / automount：处理文件系统挂载的服务
- path：由特定目录变化触发的服务
- timer：systemd 提供的定时运行服务

> &emsp;&emsp;有一些单元的名称包含一个 `@` 符号（例如：`name@*string*.service` ），这意味着它是模板单元 `name@.service` 的一个 [实例](http://0pointer.net/blog/projects/instances.html)，实际文件名中不包括 `*string*` 部分（如 `name@.service`）。`*string*` 被称作实例标识符，在 *systemctl* 调用模板单元时，会将其当作一个参数传给模板单元，模板单元会使用这个传入的参数代替模板中的 `%I` 指示符。在实例化之前，*systemd* 会先检查 `name@string.suffix` 文件是否存在。如果存在，就直接使用这个文件，而不是模板实例化。大多数情况下，包含 `@` 标记都意味着这个文件是模板。如果一个模板单元没有实例化就调用，该调用通常返回失败，除非是某些例如 `cat` 之类的命令。

&emsp;&emsp;可参考 [systemd.service(5)](https://man.archlinux.org/man/systemd.service.5) 或其他类型的单元文件对应的手册编写单元文件，或选择下面的方案修改软件包提供的单元文件：

0. 在`/etc/systemd/system/`创建同名的单元文件以覆盖`/usr/lib/systemd/system`中的单元文件并手动重新加载单元文件。可通过`systemctl edit --full unit`快速完成。mask 就是在`/etc/systemd/system/`创建同名的单元文件的指向`/dev/null`的软链接。
1. 先创建名为 `/etc/systemd/system/<单元名>.d/` 的目录，然后放入 `*.conf` 文件， conf 文件中的内容会追加到单元文件中，然后手动重新加载单元文件。可通过`systemctl edit unit`快速完成。

### 目标

&emsp;&emsp;Systemd 使用目标实现 SystemV 运行级别的效果，其大概的对应关系如下：

| SysV 运行级别 |                     Systemd 目标                      |                          注释                           |
| :-----------: | :---------------------------------------------------: | :-----------------------------------------------------: |
|       0       |           runlevel0.target, poweroff.target           |                    中断系统（halt）                     |
| 1, s, single  |            runlevel1.target, rescue.target            |                       单用户模式                        |
|     2, 4      | runlevel2.target, runlevel4.target, multi-user.target |          用户自定义运行级别，通常识别为级别3。          |
|       3       |          runlevel3.target, multi-user.target          |    多用户，无图形界面。用户可以通过终端或网络登录。     |
|       5       |          runlevel5.target, graphical.target           | 多用户，图形界面。继承级别3的服务，并启动图形界面服务。 |
|       6       |            runlevel6.target, reboot.target            |                          重启                           |
|   emergency   |                   emergency.target                    |               急救模式（Emergency shell）               |

&emsp;&emsp;使用`systemctl isolate target`在不同目标之间切换

## SSH

> &emsp;&emsp;Secure Shell (SSH) is a cryptographic network protocol for operating network services securely over an unsecured network. Typical applications include remote command-line login and remote command execution, but any network service can be secured with SSH.

&emsp;&emsp;对于 Linux 运维，SSH 一般用于远程登录。

&emsp;&emsp;SSH 协议有多种实现，这里介绍常用的 OpenSSH 。

> 关于 OpenSSH 和 SSH Keys ，可参考ArchWiki相关页面 [OpenSSH](https://wiki.archlinux.org/title/OpenSSH) 和 [SSH Keys](https://wiki.archlinux.org/title/SSH_keys) 。

### 客户端

&emsp;&emsp;客户端登录较为简单，这里介绍使用`ssh`命令登录，当然你可以使用其他工具进行登录，如 putty 等。

```bash
ssh [username]@hostname[:port]
# ssh 默认使用 22 端口
```

&emsp;&emsp;客户端的配置文件为`~/.ssh/config`，在这里，可以配置常用的服务器和全局信息，如：

```bash
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

```bash
ssh-keygen -t rsa -b 4096 -C "$(whoami)@$(uname -n)-$(date -I)"
# 默认为2048位的rsa
```

&emsp;&emsp;生成密钥对后，将私钥（默认为`~/.ssh/id_rsa`）放到自己的电脑上相同位置或使用客户端配置文件为要连接的服务器指定私钥；将公钥（默认为`~/.ssh/id_rsa.pub`追加到服务器要登录的用户的`~/.ssh/authorized_keys`文件中并确保其权限大于等于600：

```bash
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

