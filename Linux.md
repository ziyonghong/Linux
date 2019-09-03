## 2.基本命令

### 2.1查看帮助

man

![](F:\Desktop\Typora\Linux\Linux\man命令.png)

### 2.2系统工作

1.echo [字符串 | $变量]  ：在终端输出

2.reboot  重启

3.poweroff  关闭系统

4.top 于动态地监视进程活动与系统负载等信息

### 2.3系统状态检查

1.ifconfig  查看ip，MAC地址

2.uname 查看系统内核与系统版本等信息，格式为“uname [-a]”

3.uptime查看系统的负载信息

4.free显示当前系统中**内存**的使用量信息

### 2.4工作目录切换

1.pwd 显示用户当前所处的工作目录

2.cd 切换目录

 “cd -”命令返回到上一次所处的目录， 

使用“cd..”命令进入上级目录，以及使用“cd ~”命令切换到当前用户的家目录，

“cd ~username”切换到其他用户的家目录。

3.ls 显示目录中的文件信息

### 2.5文本文件编辑

1.cat 命令用于查看纯文本文件（内容较少的），内容多的用more

2.head/tail

3.tr 替换文本文件中的字符，格式为“tr [原始字符] [目标字符]”。

4.cut 命令用于按“列”提取文本字符，格式为“cut [参数] 文本”。

【要使用-f 参数来设置需要看的列数，还需要使用-d 参数来设置间隔符号。】

5.diff 比较多个文本文件的差异，格式为“diff [参数] 文件”。

【--brief 参数来确认两个文件是否不同，-c 参数来详细比较出多个文件的差异之处】

### 2.6文件目录管理

1.touch创建空白文件或设置文件的时间

2.mkdir 创建空白的目录  【合-p 参数来递归创建出具有嵌套叠层关系的文件目录】

例如：mkdir -p a/b/c/d/e

3.mv  剪切文件或将文件重命名

4.dd  按照指定大小和个数的数据块来复制文件或转换文件

例如：dd if=/dev/zero of=560_file count=1 bs=560M  从/dev/zero 设备文件中取出一个大小为 560MB 的数据块，然后保存成名为 560_file 的文件。

还可以把把光驱设备中的光盘制作成 iso 格式的镜像文件

例如：dd if=/dev/cdrom of=RHEL-server-7.0-x86_64-LinuxProbe.Com.iso

### 2.7打包与搜索

1.tar

![](F:\Desktop\Typora\Linux\Linux\tar命令.png)

例：tar -czvf etc.tar.gz /etc   使用 tar 命令把/etc 目录通过 gzip 格式进行打包压缩，并把文件命名为 etc.tar.gz

2.grep 在文本中执行关键词搜索，并显示匹配的结果

3.find 按照指定条件来查找文件  

例：find / -name file1 从 '/' 开始进入根文件系统搜索文件和目录

## 6.存储结构与磁盘划分

### 6.1一切从“/”开始

![](Linux\文件存储结构.png)



![](Linux\常见目录名称.png)



### 6.2物理设备

![](Linux\设备文件名称.png)



硬盘设备是由大量的扇区组成的，每个扇区的容量为 512 字节。其中
第一个扇区最重要，它里面保存着主引导记录与分区表信息。

![](Linux\第一个扇区.png)



![](Linux\磁盘分区.png)

扩张分区并不是一个实际的分区，它只是一个指向下一个分区的指针。这种指针结构讲形成一个单向链表。



### 6.3文件系统与数据资料

用户在硬件存储设备中执行的文件建立、写入、读取、修改、转存与控制等操作都是依靠文件系统来完成的。文件系统的作用是合理规划硬盘。



Linux 把每个文件的权限与属性记录在inode 中，而且每个文件占用一个独立的 inode 表格，而文件的实际内容则保存在block块中。

一个 inode 的默认大小仅为 128B（Ext3），记录一个 block 则消耗 4B。当文件的 inode 被写满后，Linux 系统会自动分配出一个 block 块，专门用于像 inode 那样记录其他 block 块的信息，这样把各个block 块的内容串到一起，就能够让用户读到完整的文件内容了.

![](Linux\VFS.png)



### 6.4挂载硬件设备

在拿到一块全新的硬盘存储设备后要先**分区**，然后**格式化**，最后才能**挂载**并正常使用。

当用户需要使用硬盘设备或分区中的数据时，需要先将其与一个已存在的目录文件进行关联，而这个关联动作就是“挂载”。

#### 6.4.1 mount命令

```shell
 mount /dev/sdb2 /backup #把设备/dev/sdb2 挂载到/backup 目录
```

这种方式系统在重启后挂载就会失效！

要让硬件设备和目录永久地进行自动关联，就必须把挂载信息按照指定的填写“设备文件 挂载目录 格式类型 权限选项 是否备份 是否自检” 写入到/etc/fstab 文件中。

![](Linux\挂载.png)

#### 

#### 6.4.2 umount命令

撤销已经挂载的设备文件。

```shell
umount /dev/sdb2
```



### 6.5增加硬盘设备

在虚拟机管理主界面增加硬盘。

#### 6.5.1 fdisk 命令

```shell
root@ziyonghong:~# fdisk /dev/vda

Welcome to fdisk (util-linux 2.27.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): n
To create more partitions, first replace a primary with an extended partition.

Command (m for help): p
Disk /dev/vda: 40 GiB, 42949672960 bytes, 83886080 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x77ba45a4

Device     Boot Start      End  Sectors Size Id Type
/dev/vda1  *     2048 83884031 83881984  40G 83 Linux
```

```shell
[root@linuxprobe ]# file /dev/sdb1
/dev/sdb1: cannot open (No such file or directory)
[root@linuxprobe ]# partprobe
[root@linuxprobe ]# partprobe
[root@linuxprobe ]# file /dev/sdb1
/dev/sdb1: block special
```

用于格式化操作的命令 **mkfs**

**df -h** 命令来查看挂载状态和硬盘使用量信息。



#### 6.5.2 du 命令

用来查看一个或多个文件占用了多大的硬盘空间。

**du -sh /*** 查看在 Linux 系统根目录下所有一级目录分别占用的空间大小。



### 6.6增加交换分区

SWAP（交换）分区是在硬盘中预先划分一定的空间，然后将把内存中暂时不常用的数据临时存放到硬盘中，腾出物理内存空间让更活跃的程序服务来使用，其设计目的是为了解决真实物理内存不足的问题。

使用 SWAP 分区格式化命令 **mkswap**

**swapon** 命令把准备好的 SWAP 分区设备正式挂载到系统中。

**free -m** 命令查看交换分区的大小变化



### 6.7磁盘容量配额

**quota** 命令进行磁盘容量配额管理，从而限制用户的硬盘可用容量或所能创建的最大文件个数。quota 命令还有**软限制**和**硬限制**的功能。

**edquota** 命令用于编辑用户的 quota 配额限制。



### 6.8软硬方式链接

**硬链接：**可以理解为 一个“**指向原始文件** inode 的指针”，系统不为它分配独立的 inode 和文件。硬链接文件与原始文件其实**是同一个文件**，只是名字不同。因此即便原始文件被删除，依然可以通过硬链接文件来访问。

**软连接（符号链接）：**仅仅包含所链接文件的**路径名**，可以跨越文件系统进行链接。但当原始文件被删除后，链接文件也将失效。

##### ln 命令

```shell

root@ziyonghong:~# echo "welcome! " >readme.txt
root@ziyonghong:~# ln -s readme.txt readit.txt #没有-s是硬链接
root@ziyonghong:~# cat readme.txt
welcome!
root@ziyonghong:~# cat readit.txt
welcome!
root@ziyonghong:~# ls -l readme.txt
-rw-r--r-- 1 root root 10 Mar 30 15:58 readme.txt
root@ziyonghong:~# rm -f readme.txt
root@ziyonghong:~# cat readit.txt
cat: readit.txt: No such file or directory
root@ziyonghong:~#
```



## 7.RAID与LVM磁盘阵列技术

#### 7.1 RAID （独立冗余磁盘阵列）

​    把多个硬盘设备组合成一个容量更大、安全性更好的磁盘阵列，并把数据切割成多个区段后分别存放在各个不同的物理硬盘设备上，然后利用分散读写技术来提升磁盘阵列整体的性能，同时把多个重要数据的副本同步到不同的物理硬盘设备上，从而起到了非常好的数据冗余备份效果。

##### 7.1.1 RAID 0

RAID 0 技术提升了硬盘设备的读写速度，但是它是将数据依次写入到各个物理硬盘中，也就是说，它的数据是分开存放的，其中任何一块硬盘发生故障都会损坏整个系统的数据。

![](Linux\RAID0.png)

##### 7.1.2 RAID 1

将数据同时写入到多块硬盘设备上（可以将其视为数据的镜像或备份）。当其中某一块硬盘发生故障后，一般会立即自动以热交换的方式来恢复数据的正常使用。 但这样做无疑是牺牲了可用率的。

![](Linux\RAID1.png)

##### 7.1.3  RAID 5

RAID 5 技术实际上没有备份硬盘中的真实数据信息，而是当硬盘设备出现问题后通过奇偶校验信息来尝试重建损坏的数据。

parity 部分存放的就是数据的奇偶校验信息。

![](Linux\RAID5.png)

##### 7.1.4 RAID 10

先分别两两制作成 RAID 1 磁盘阵列，以保证数据的安全性；

然后再对两个 RAID 1 磁盘阵列实施 RAID 0 技术，进一步提高硬盘设备的读写速度。

![](Linux\RAID10.png)

##### 7.1.5 部署磁盘阵列

在虚拟机中添加 4 块硬盘设备来制作一个 RAID 10 磁盘阵列:

**mdadm**  命令在 Linux 系统中创建和管理软件 RAID 磁盘阵列

```shell
[root@linuxprobe ~]# mdadm -Cv /dev/md0 -a yes -n 4 -l 10 /dev/sdb /dev/sdc
/dev/sdd /dev/sde
mdadm: layout defaults to n2
mdadm: layout defaults to n2
mdadm: chunk size defaults to 512K
mdadm: size set to 20954624K
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md0 started.
```

​	-C 参数代表创建一个 RAID 阵列卡；-v 参数显示创建的过程，同时在后面追加一个设备名称/dev/md0，这样/dev/md0就是创建后的RAID磁盘阵列的名称；-a yes 参数代表自动创建设备文件；-n 4 参数代表使用 4 块硬盘来部署这个RAID 磁盘阵列；而-l 10 参数则代表 RAID 10 方案；最后再加上 4 块硬盘设备的名称就搞定了。



##### 7.1.6损坏磁盘阵列及修复

有一块物理硬盘设备出现损坏而不能继续正常使用后，应该使用 **mdadm** 命令将其移除.

```shell
[root@linuxprobe ~]# mdadm /dev/md0 -f /dev/sdb #移除
mdadm: set /dev/sdb faulty in /dev/md0
[root@linuxprobe ~]# mdadm -D /dev/md0 #查看 RAID 磁盘阵列的状态
```

##### 

##### 7.1.7 磁盘阵列+备份盘

准备一块足够大的硬盘，这块硬盘平时处于闲置状态，一旦 RAID磁盘阵列中有硬盘出现故障后则会马上自动顶替上去。

#### 

#### 7.2 LVM（逻辑卷管理器）

LVM 可以允许用户对硬盘资源进行动态调整。

##### ![](Linux\LVM.png)

**物理卷**处于 LVM 中的最底层，可以将其理解为物理硬盘、硬盘分区或者 RAID 磁盘阵列，这都可以。

**卷组**建立在物理卷之上，一个卷组可以包含多个物理卷，而且在卷组创建之后
也可以继续向其中添加新的物理卷。

**逻辑卷**是用卷组中空闲的资源建立的，并且逻辑卷在建立后可以动态地扩展或缩小空间。



![](Linux\LVM部署命令.png)



##### 7.2.1逻辑卷快照

LVM 的快照卷功能有两个特点：
➢ 快照卷的容量必须等同于逻辑卷的容量；
➢ 快照卷仅一次有效，一旦执行还原操作后则会被立即自动删除。

使用-s 参数生成一个快照卷，使用-L 参数指定切割的大小。另外，还需要在命
令后面写上是针对哪个逻辑卷执行的快照操作。

```
lvcreate -L 120M -s -n SNAP /dev/storage/vo
```



### 8.iptables 与 firewalld 防火墙

iptables服务会把配置好的防火墙策略交由内核层面的 netfilter 网络过滤器来处理，而 firewalld 服务则是把配置好的防火墙策略交由内核层面的 nftables 包过滤框架来处理。



防火墙策略规则中 DROP 和 REJECT 的不同：

DROP 的动作是丢包，不响应；REJECT 是拒绝请求，同时向发送方回送拒绝信息。



SNAT 是一种为了解决 IP 地址匮乏而设计的技术，它可以使得多个内网中的用户通过同一个外网 IP 接入 Internet。

![](Linux\无SNAT.png)

![](Linux\SNAT.png)



#### 8.1 服务的访问控制列表

```
vim /etc/hosts.deny
vim /etc/hosts.allow
```



## 9. ssh服务远程管理主机

#### 9.1配置网络服务

##### 9.1.1 配置网络参数

**nmtui** 命令

##### 

##### 9.1.2 创建网络会话

```shell
nmcli connection show 
nmcli con show  [NAME]
nmcli connection add con-name company ifname eno16777736  autoconnect no type ethernet ip4 192.168.10.10/24 gw4 192.168.10.1
nmcli connection add con-name house type [] ifname  eno16777736
nmcli connection up house 
```

 con-name 参数指定公司所使用的网络会话名称 company， ifname 参 数指定本机的网卡名称（千万要以实际环境为准，不要照抄书上的 eno16777736），用 autoconnect no 参数设置该网络会话默认不被自动激活，以及用 ip4 及 gw4 参数手动指定 网络的 IP 地址。





## 13. BIND 提供域名解析服务 

#### 13.1 DNS 域名解析服务

将域名解析为 IP 地址（正向解析），或将 IP 地址解析为域名（反向解析）

主服务器是用于管理域名和 IP 地址对应关系的真正服务器。

从服务器帮助主 服务器“打下手”，分散部署在各个国家、省市或地区，以便让用户就近查询域名，从而减轻 主服务器的负载压力。

缓存服务器不太常用，一般部署在企业内网的网关位置，用于加速用 户的域名查询请求。

![](Linux\域名解析.png)



#### 13.2  安装 bind 服务程序 

BIND服务是最广泛、最安全可靠且高效的域名解析服务程序。

```shell
root@ziyonghong:/# apt install bind
Reading package lists... Done
Building dependency tree
Reading state information... Done
Package bind is not available, but is referred to by another package.
This may mean that the package is missing, has been obsoleted, or
is only available from another source
However the following packages replace it:
  dnsutils dnsutils:i386 bind9:i386 bind9 manpages

E: Package 'bind' has no installation candidate
#不知为啥报错了？
```

```shell
vim /etc/named.conf 
 11 listen-on port 53 { any; }; 
 17 allow-query { any; }; 
 #把第 11 行和第 17 行的地址均修改为 any，分别表示服务器上的所有 IP 地址均可提供 DNS 域名解析服务
```

主配置文件（/etc/named.conf）：这些参数用来定义 bind 服务程序的运行。

 区域配置文件（/etc/named.rfc1912.zones）：用来保存域名和 IP 地址对应关系的所在位置。类似于图书的目录，对应着每个域和相应 IP 地址所在的具体位置，当需要查看 或修改时，可根据这个位置找到相关文件。 

数据配置文件目录（/var/named）：该目录用来保存域名和 IP 地址真实对应关系的数据配置文件。





## 14.DHCP 动态管理主机地址 

解动态主机配置协议（DHCP）用于 自动管理局域网内主机的 IP 地址、子网掩码、网关地址及 DNS 地址等参数，可以有效地提 升 IP 地址的利用率，提高配置效率，并降低管理与维护成本。 