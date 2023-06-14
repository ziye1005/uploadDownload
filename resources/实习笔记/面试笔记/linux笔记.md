# Linux概述

unix是当前所有操作系统的思想来源，C语言编写，高效稳定，但不是开源免费。

Linus开发了Linux和git，linux是基于Minix重写的免费开源的Unix，高速轻量。

GNU/Linux：GNU is not Unix，开发自由软件，符合GPL协议的源码都是公开的和自由的，所有人都可以修改打包发布，但是打包发布的软件必须也遵循GPL。

狭义的linux：linux内核；广义的linux：GNU/Linux，当时richard的GNU里面只差内核是没有自由公开的，linus公开了自己写的1w+行源码。

linux发行版：redhat（性能好，界面不够好） centOS（稳定） ubuntu（桌面好看，不够稳定）suse（桌面最华丽）

## linux VS windows

|          | windows                                | linux                        |
| -------- | -------------------------------------- | ---------------------------- |
| 费用     | 收费且很贵                             | 免费                         |
| 软件支持 | 微软官方                               | 全球linux开发者或软件社区    |
| 安全性   | 有保障，微软官方打补丁                 | 不够安全                     |
| GUI      | 用户交互性很好                         | 命令行，新手困难，但效率很高 |
| 可定制性 | 黑盒，不能改                           | 开源，模块化，可做内核裁剪   |
|          | 微软官方维护，提供安全保证，新人易上手 | 短小精悍，开源免费，高效轻量 |

## 文件系统

linux中，**一切皆文件**。多用户系统。linux下只有一棵树，一个根目录。用的正斜杠/，windows是反斜杠\。文件系统有ext4和xfs（更适合管理大文件）。

| 文件目录 | 含义                                   |
| -------- | -------------------------------------- |
| bin      | binary,最经常使用的指令，cd等          |
| lib      | 库文件                                 |
| usr      | 用户所有应用程序，类似于program files  |
| etc      | 配置文件，安装软件的配置文件在这里配置 |
| home     | 每个用户都会在home下有一个文件夹       |
| boot     | 启动所需文件，不能写用户文件           |
| dev      | device，管理设备                       |
| opt      | optional，给第三方软件包划分的目录     |



# 命令

## vim——编辑器之神

最核心的是一般模式，输入**:或者/**进入底层命令模式，输入i a o 进入编辑文本模式，其他两个模式按esc退出。

![image-20230521195655834](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230521195655834.png)

普通/一般模式

| 语法         | 功能                                                     |
| ------------ | -------------------------------------------------------- |
| yy 5yy       | 复制一行 从当前行复制8行                                 |
| y$           | 先敲一个y，再敲$符号，复制当前光标位置到该行结尾         |
| y^           | 先敲一个y，再敲shift+6 ^符号，复制该行开始到当前光标位置 |
|              |                                                          |
| p 5p         | 粘贴一遍  将yy或者dd的内容粘贴5遍                        |
| dd 10dd      | 删除一行 删除10行                                        |
| u            | 撤销上一步，可以疯狂按u                                  |
| 移动光标操作 |                                                          |
| shift+6 ^    | 该行头部                                                 |
| shift+4 $    | 该行尾部                                                 |
| shift+g      | 移动到页尾                                               |
| 数字+shift+g | 移动到目标行                                             |
| 1+shift+g    | 移动到页头部，先敲1，再按住shift敲g                      |
| w            | 下一个词的词头                                           |
| e            | 下一个词的词尾                                           |
| b            | 上一个词的词头                                           |

插入模式

| 语法 | 描述                   |
| ---- | ---------------------- |
| i    | 当前光标前             |
| a    | 当前光标后，相当于i+➡  |
| o    | 换行插入，相当于i+换行 |

底层命令模式：

| 语法            | 描述                                         |
| --------------- | -------------------------------------------- |
| :wq :q :wq! :q! | 保存退出，退出，强制保存退出，强制退出不保存 |
| :set nu         | 显示行号                                     |
| :set nonu       | 不显示行号                                   |
| /find           | 查找find的值，按n向下查找，按shift+n向上查找 |
| :%s/old/new/g   | 全局替换所有的old变为new                     |
| :s/old/new      | 将当前行第一个old替换为new                   |
| :s/old/new/g    | 将当前行所有的替换成new                      |

## 网络配置 ssh免密登录 服务启动停止

```shell
vim /etc/sysconfig/network-scripts/ifcfg-ens33 # 配置IP地址，网关，DNS
service network restart # 重启网络
vi /etc/hostname # 修改主机名
vim /etc/hosts # 修改IP到主机名的映射

ssh-keygen -t rsa # 在home/atguigu下，敲3个回车，生成私钥id_rsa和公钥id_rsa.pub
ssh-copy-id hadoop102 # 在.ssh下，拷贝公钥到目标IP上，这个hadoop102在/etc/hosts里面映射过

systemctl status crond # 查看crond是否打开，用来开启定时任务
systemctl stop firewalld # 停止防火墙服务
systemctl start firewalld # 开启防火墙服务
systemctl restart firewalld # 重启防火墙
systemctl enable firewalld.service # 开启开机自启动
systemctl disable firewalld.service # 关闭开机自启动

shutdown [3/now]# 默认是1分钟后关机，可以设置3分钟后关机/立刻关机
shutdown -c # 取消关机
halt # 停机不断电
reboot / shutdown -r now # 立刻重启
```

| known_hosts     | 记录ssh访问过计算机的公钥（public key）               |
| --------------- | ----------------------------------------------------- |
| id_rsa          | 生成的私钥                                            |
| id_rsa.pub      | 生成的公钥                                            |
| authorized_keys | 存放授权过的无密登录服务器公钥，里面应该有102,103,104 |

## 基本常用快捷键

| 快捷键/命令  | 描述                                                         |
| ------------ | ------------------------------------------------------------ |
| ctrl+alt+F2  | 到大黑屏，shell编程                                          |
| ctrl+alt+F1  | 回到GUI                                                      |
| ctrl+c       | 停止进程                                                     |
| ctrl+l       | 清屏，或者clear                                              |
| 输入中文     | 应用程序——系统工具——设置——region and language——选择拼音上去，用徽标键+空格 切换 |
| 帮助命令man  | man ls 用空格向下翻页，着重看描述<br />man -f cd<br />man man 查看man自身 |
| 帮助命令help | 更简洁，只针对内置命令cd   help cd                           |
| 命令 --help  | 更简洁，只针对外部命令ls，ls --help                          |
| type         | 查看cd是shell内嵌命令还是外部命令                            |

# 常用基础命令

## 文件目录类

| 命令                                | 示例                                                        | 描述                                                         |
| ----------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------ |
| pwd                                 |                                                             | print working directory 打印工作目录                         |
| cd                                  | ../回退一级，./当前目录下                                   | change directory 切换路径                                    |
| ls [-a/-l/-al/**-h**]               | ls -l 等价于ll                                              | 列出目录内容，-a包括.开头的隐藏文件，-l详细信息，-h文件大小KB MB |
| mkdir /opt/module                   |                                                             | 创建目录                                                     |
| rm[-r/-f/-v]                        | rm -rf慎用                                                  | -r递归删除文件夹；-f不跳过确认；-v展示详细过程               |
| cp [-r] source target               |                                                             | 拷贝文件/文件夹-r                                            |
| mv oldNameFile newNameFile          | mv oldNameFile newNameFile                                  | 移动或者重命名                                               |
| history                             |                                                             | 查看已经执行过历史命令                                       |
| ==查看文件cat more less head tail== |                                                             |                                                              |
| cat changeTest.cfg                  |                                                             | 小文件，一屏幕可以展示完全的                                 |
| **more** changeTest.cfg             |                                                             | 分页展示，空格翻页                                           |
| **less** changeTest.cfg             |                                                             | 适合大文件，分页加载<br />空格向下翻页，b向上翻页<br />shift+g到末尾，g到开头<br />/芭比 查找“芭比”，n向下找，shift+n向上找 |
| **head** -n 10 changeTest.cfg       |                                                             | 默认查看头10行                                               |
| **tail** -n 10 changeTest.cfg       |                                                             | 默认查看尾10行                                               |
|                                     |                                                             |                                                              |
| ==重定向==                          |                                                             |                                                              |
| >                                   | ll -a > /opt/sxq/info                                       | 将ll -a的结果**覆盖写入**info文件内                          |
| >>                                  | ls >> /opt/sxq/info                                         | 将ls的结果追加到info尾部                                     |
| echo -e                             | echo -e "hello\n      linux" >> /opt/sxq/info               | 将hello换行linux追加到info                                   |
| 挂载到服务器                        | nohup tools/train.py > output.log 2>&1 &                    | 将train.py的运行挂载在服务器上，结果输出到output.log文件，2>&1 &将错误信息转化为标准化输出 |
|                                     |                                                             |                                                              |
| ==软链接==                          |                                                             |                                                              |
| ln                                  | ln -s /opt/sxq/info myInfo<br />ln -s myfolder1/ lnmyfolder | link的简写，软链接（符号链接），类似于快捷方式<br />为/opt/sxq/info创建软链接myInfo<br />为文件夹myfolder1创建软链接lnmyfolder |
| 删除软链接                          | rm -rf lnmyfolder/                                          | 有这个斜杠，会把源数据myfolder1下面的**原文件清空**<br />没有最后的/，仅删除快捷方式lnmyfolder |
| ==历史记录==                        |                                                             |                                                              |
| history                             | history -c<br />history 10                                  | 删除历史命令<br />查看最近10条                               |



## 日期时间

| 命令    | 示例                                                         | 说明                                                         |
| ------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| date    | date +%Y                                           2023<br />**date +%Y-%m-%d**                          2023-05-22<br />date "+%Y-%m-%d %H:%M:%S"   2023-05-22 11:36:12<br />date +%H:%M:%S                            11:38:03 | 当前时间的操作                                               |
| date -d | date -d '3 month ago'<br />date -d '3 month ago' +"%Y-%m-%d %H:%M:%S" | 获取3月前的日期<br />获取该日期的时分秒                      |
| date -s | date -s "2027-05-22 12:41:25"                                | 将当前系统时间设置为2027--5-22                               |
| ntpdate | ntpdate cn.pool.ntp.org                                      | 同步更新时间，中国开源免费的NTP服务器是这个cn.pool.ntp.org   |
| cal     |                                                              | 获取当前月份的日历                                           |
|         | cal -3m                                                      | 获取当前月份前后共3个月的日历，并将monday放在第一个显示（默认是sunday第一列） |
|         | cal 2023                                                     | 获取2023全年日历                                             |



## 用户/用户组管理

| 用户管理命令 | 示例                                                         | 描述                                                         |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| useradd      | useradd tony                                增加一个tony用户，在home下面有一个tony文件夹<br />useradd -d /home/dave david   增加一个david  用户，在home下面有一个dave文件夹属于david | 增加linux用户                                                |
| userdel      | userdel -r david<br />userdel tony                           | 删除用户david，连同home下的子文件夹dave<br />删除用户tony，保留子目录/home/tony |
| id           | id root<br />less /etc/passwd                                | 查看某个用户是否存在<br />查看所有用户，里面很多都是没见过的，系统服务相关，bin，daemon |
| su           | su root/david/atguigu                                        | switch user，切换用户                                        |
|              | whoami                                                       | 显示当前会话的用户是谁，自身用户名称                         |
|              | who am i                                                     | 显示登录用户的用户名以及登陆时间                             |
| sudo         | 在/etc/sudoers里面添加一行<br />tony    ALL=(ALL)       NOPASSWD:ALL<br />即可在tony用户下用sudo指令执行超级管理员的指令 | 给一个普通用户临时赋予root权限                               |

![image-20230522140503698](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230522140503698.png)



| 用户组管理命令 | 示例                      | 描述                         |
| -------------- | ------------------------- | ---------------------------- |
| /etc/group     | tail -10 /etc/group       | 用户组在这里面               |
| usermod        | usermod -g tony meifa     | 修改用户所属组               |
| groupadd       | groupadd bigdata          | 增加组                       |
| groupdel       | groupdel testing          | 删除组                       |
| groupmod       | groupmod -n haircut meifa | 修改组名，从meifa改为haircut |



## 文件权限

![image-20230522145144167](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230522145144167.png)

第0位：d表示目录，l表示软链接，-表示普通文件
1-3位：属主（User）权限；4-6位：属组（group）权限；7-9位：其他用户权限（Other）。
一般都是逐级权限下降的

想要删除文件，需要所在目录的写（w）权限。

| 文件权限命令                    | 示例                                                         | 描述                                                         |
| ------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| chmod [{ugoa}{+-=}{rwx}] 文件名 | chmod g+wx initial.cfg #增加了用户组的写、执行<br />chmod a=rw babiByZijin.txt  #所有用户ugo的权限改为读、写 | u——user; g——group;o——other<br />+——增加权限；-表示减少权限；=表示设置权限<br />rwx分别表示读写执行（执行变绿） |
| chmod [-R] 777 文件名           | r（2^2=4）w（2）x（1）<br />第一个7表示user，第二个表示group，第三个表示other<br />-R 修改整个文件夹下所有文件的权限 |                                                              |
| chown [-R]                      | chown atguigu:atguigu hello.txt<br />chown atguigu hello.txt | 修改所属用户 && 所属组<br />只修改所属用户                   |
| chgrp                           | chgrp tony hello.txt                                         | 只修改所属组                                                 |



## 搜索查找

### find

根据文件名，进行名称大小的查找。

从指定目录，向下递归地遍历其各个子目录，将满足条件的文件显示在终端。

-name（按照名称）-user（查找所属某个用户的文件） -size（按照文件大小）

```shell
find [搜索范围] [选项]
find /opt/sxq/ -name "*.txt"  # 在/opt/sxq/目录下寻找所有txt文件
find /opt/sxq/ -user atguigu  # 在/opt/sxq下寻找atguigu用户的所有文件
find ./ -size +200M           # 在当前目录下寻找>200M的文件
```

### locate

根据文件名，利用事先建立的locate 数据库实现快速查找。须定期更新 locate才能保证查询准确性，默认一天更新一次。

```shell
updatedb     # 先更新db
locate hello # 查找所有包含hello的文件名，速度很快
```

### grep

==根据文件内容==，筛选过滤得到文件名；|管道符号，表示把前一个命令的结果传给后面的命令过滤

```shell
grep [-n/-i] 查找内容 源文件 -n显示行号,-i忽略大小写
grep -n boot initial.cfg  # 在initial.cfg查找boot，显示行号和内容
ls | grep .cfg            # 筛选处当前目录下的cfg文件
ls /usr/lib/systemd/system | grep d.service # 筛选system文件夹下的守护进程，即包含d.service的文件
ls | wc                   # 浏览当前目录，显示ls结果的行数、字数，以及字节数，用-l只显示行数
ls -l | grep "^-" | wc -l # 统计当前目录下文件个数(不包括目录)
ls -lR| grep "^-" | wc -l # 统计当前目录下文件的个数（包括子目录）
```

## 压缩解压缩

```shell
-c:打包
-x:解压
-v:显示详细信息
-z:打包同时压缩
-f:指定打包之后的文件名
-C:指定解压后的路径
-zcvf:打包命令
tar -zcvf temp.tar.gz hadoop-3.1.3 hello.txt # 将hadoop-3.1.3文件夹，hello.txt一起打包，放在temp.tar.gz中
-zxvf:解压缩  -C指定解压目录
tar -zxvf hadoop-3.1.3.tar.gz -C /opt/software/tarzxvf/ # 将hadoop-3.1.3.tar.gz解压到tarzxvf文件夹
```

## 磁盘分区

| 命令                          | 示例                                                         | 描述                                   |
| ----------------------------- | ------------------------------------------------------------ | -------------------------------------- |
| du [--max-depth=n] [-h/-a/-s] | du --max-depth=1 -ah /opt/software/ 只显示一级的目录统计，如果n=2，显示的结果会更多<br />-h  用KB MB显示；-a  不仅查看子目录，还包括文件；  -s  总和，相当于max-depth=0； | disk usage<br />查看目录的磁盘占用情况 |
| df                            | df -h                                                        | disk free查看空余磁盘                  |
| lsblk                         |                                                              |                                        |
| mount/umount                  | mount -t iso9660 /dev/cdrom /mnt/cdrom/  # 挂载镜像文件<br />umount /mnt/cdrom  # 卸载镜像文件 | 挂载/卸载设备                          |
| fdisk                         | fdisk -l                                                     | 查看磁盘分区列表                       |

## 进程管理

| 进程管理命令                | 示例                                                         | 描述                                                         |
| --------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ps aux \| less              |                                                              | 分页查看系统中所有进程（带终端的和不带终端的）不加-的BSD风格 |
| ps -ef \| less              |                                                              | 查看系统所有进程，加-的UNIX风格                              |
| kill                        | kill -9 进程号                                               | 通过进程号杀死进程，-9表示强制进程立刻停止                   |
| killall                     |                                                              | 通过进程名称杀死进程，范围杀死，谨慎使用                     |
| pstree -pu \| less          |                                                              | -p显示PID，-u显示所属用户，less做分页显示                    |
| top                         | top -d 5  每5s刷新一次，默认3秒<br />top -p 1201 指定PID监控<br />shift+p按照CPU排序，shift+m按照内存排序 | 实时监控系统进程状态                                         |
| netstat -anp \| grep 进程号 | -a 显示所有正在监听的；-n拒绝别名；-p显示调用的进程<br />    | 显示网络路由信息、端口占用信息                               |
| netstat -nlp \| less        | -l仅列出在监听的服务状态                                     | hostname:port 端口号0-65535，mysql3306，表示哪一个房子（hostname）的哪一个门（port）。 |
| ifconfig                    |                                                              | 查看所有的网络信息                                           |
| crontab -l/-e/-r            | -l查看所有定时任务，-e编写定时任务，-r清理所有定时任务<br />*/1 * * * * echo "hello,world" >> /root/hello  每隔一分钟写入一条 | 系统定时任务                                                 |

![image-20230522192448953](https://gitee.com/ziye1005/test-typora/raw/master/imgTypora/image-20230522192448953.png)

crontab 有5个位置，分别表示   {分0-59}{时0-23}{一个月的第几天1-31}{一年的第几个月1-12}{一周的星期几0-7,0和7都代表周天}

第三个位置和第五个位置不要同时有东西，因为每月的第几天和星期几可能互斥

java的@sheduled里面的cron是类似的写法，但前面加了秒数，最后有一个?表示互斥

"*"表示任意，","表示不连续时间，"-"表示连续时间，" * /10"表示每隔10，"0/10"表示从0开始每隔10

| 特定时间指令    | 含义                    |      |
| --------------- | ----------------------- | ---- |
| 45 22 * * *     | 每天的22:45             |      |
| 0 5 1,15,23 * * | 每月的1号15号23号的5:00 |      |
| 40 4 * * 1-5    | 每周一至周五的4:40      |      |
| */10 4 * * *    | 每天凌晨4点，每隔10分钟 |      |



## 其他

```shell
# 1.卸载虚拟机自带的JDK
rpm -qa | grep -i java | xargs -n1 rpm -e --nodeps
rpm -qa：查询所安装的所有rpm软件包；grep -i：忽略大小写；xargs -n1：表示每次只传递一个参数；rpm -e –nodeps：强制卸载软件不检查依赖
# 2.添加虚拟路径
export PATH=export PATH=\$PATH:\$JAVA_HOME/binJAVA_HOME/bin
source /etc/profile # 使得环境变量生效
# 3.scp实现server到server的拷贝，第一次的时候使用
scp  -r     $pdir/$fname       $user@$host:$pdir/$fname
# 在102上，拷贝文件夹jdk到103的指定目录
scp -r /opt/module/jdk1.8.0_212/ atguigu@hadoop103:/opt/module/
# 在103上，找102的hadoop文件夹拉取过来
scp -r atguigu@hadoop102:/opt/module/hadoop-3.1.3 ./

# 4.rsync实现远程同步，不全部拷贝，相当于更新，速度快。在102上做更改，同步到103
rsync -av hadoop-3.1.3/ atguigu@hadoop103:/opt/module/hadoop-3.1.3/
# -a:归档拷贝    -v:显示复制过程

# 5.编写xsync集群分发脚本

# 6.查看进程占用内存
jmap -heap 2611 
```

# 软件包管理

## RPM

Ubuntu软件包管理：apt，红帽系centOS是RPM，会有依赖关系，很多情况下不能一键安装

```shell
rpm -qa | grep -i firefox # -qa 查询所有rpm软件包；过滤出火狐相关的
rpm -qi firefox           # 查询firefox相关的详细信息
rpm -e --nodeps firefox            # -e卸载firefox；不考虑依赖关系--nodeeps
rpm -ivh firefox-68.10.0-1.el7.centos.x86_64.rpm        
# -i表示安装，-v显示详细信息，-h显示进度条，到/run/media/atguigu/CentOS 7 x86_64/Packages下，这是镜像文件所在处
```

## Yum

类似于maven，yum直接从指定的服务器自动下载，将相关的依赖一键下载安装。

```shell
yum -y install/update/remove firefox  # 对所有的交互都表示yes
yum list  # 显示所有软件包信息
yum -y install firefox # 安装火狐浏览器，不用向rpm一样先找到安装包，这是自动从服务器上下载的
yum remove firefox # 删除
```