### 常用命令 ###

`man appname` //应用手册  
`cat /proc/version linux` 内核版本  
`uname -r` 内核版本/架构  
`cat /etc/redhat-release` 发行版(redhat)  
`cat /etc/issue` ubuntu版本号  
`lsb_release -a` ubuntu版本号（详细）  
`su` //切换到root用户 使用exit或Ctrl+D返回普通用户(`sudo su`可以无密码切换到`root`) `su -`切换到root并使用root的环境  
`df` //磁盘状态  
`set/export key=value` 设置环境变量，分别对当前shell和当前会话有效  
`vi /etc/profile` 或 `vi /root/.bashr` 加入 `export PATH=VALUE` 在重启会每次会话都有效  
在`/etc/profile.d/`下创建`xxx.sh`文件，在里面写`export path=value`  
`tee file std` 从标准输入(std)中读取并同时写入到标准输出和指定的文件(file)上  
`ps | grep $$ | awk '{print $4}'` 显示正在使用的shell  
`which redis-cli` 查看是否安装了某程序  
`sudo init 6` 重启  
`sudo init 0` 关机  
`date` 查看时间  
`tzselect` 设置时区，按照提示一步步来，还要修改/etc/profile(root用户) --这个基本来说不管用  
`timedatectl` CentOS系，查看时间详情  
`timedatectl set-time yyyy-mm-dd` 设置日期  
`timedatectl set-time hh:mm:ss` 设置时间  
`timedatectl set-ntp yes/no` 开关NTP服务器 需安装ntp包，并开启ntpd服务  
`timedatectl list-timezones` 查看所有的时区  
`timedatectl set-timezone Asia/Shanghai` 设置时区  
修改时区*Ubuntu好用*  `sudo cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime`  
`awk '{print $4}' file` 处理文本通用工具，`'{命令 模式}'`是通用的参数形式  
`last -x|grep reboot` 自从上次重启后运行时间  
`env` 查看环境变量，其中`PATH`的值就是系统可执行文件的目录，一般最常见的可执行文件的安装目录为`/usr/sbin`  
`sort` 执行排序，一般用于管道的后端，常用参数`-r`表示逆序，`-n`表示作为数字排序 如`du | sort -nr` 把目录按由大到小排序  
`grep str` 执行过滤，默认为包含。`-v str`为排除。一般用于管道符后面  
`tail -n 50  /var/spool/mail/root` 查看一些系统通知之类的  
`tail -f /var/log/xxx.log` 持续输出log的变化，使用`Ctrl+C`结束并返回 `-F` 等价于 `-f --retry`  
`head` 与`tail`相反用来现实文件的头部，默认前10行  
`journalctl -f` 实时输出日志，在ubuntu中等价于`tail -f /var/log/syslog.log`  
`source` 执行刚安装的脚本使其立刻生效而不用等重启后  
`eval` 一个用法：`eval command -options $var1 $var2` 先对后面跟的命令语句执行变量解析替换，然后再运行后面的命令  
`pv` 显示命令进度（Pipe Viewer），常用于命令管道中（需要安装，如`sudo apt-get install pv`） eg:`pv /media/xxx.mkv > ~/yyy.mkv` 显示拷贝文件进度  
`( command )` 括号可以强制其中的命令在子Shell中执行。  

> 子Shell环境拥有与 父Shell 相同的环境变量、标准输入、输出、错误。比如我们在登陆shell中执行一个xxx.sh脚本，则这个脚本就是在一个子shell环境中执行。脚本对子shell的改动不会影响父shell。
   题外话：如果需要以父shell执行sh脚本，使用如下语法：`. ./test.sh`，注意两个点之间有个空格
   举个子环境的例子：`(umask 077; touch xxx.config)` 这条命令以特定权限创建一个文件，而且由于在子shell执行，对权限掩码的修改不影响父shell。

### 安全相关 ###

权限三部分：owner group others  
`chmod (-R) ugo+/-rwx filename` 修改权限  
`chgrp -R root filename` 更改文件所属组  
`chown (-R -V) user filename` 更改文件所有者  
`sudo passwd username` 修改密码  
`id (-a) username` 查看账号信息  
`cat /etc/group` 文件包含所有组  
`cat /etc/shadow` 和 `/etc/passwd` 系统存在的所有用户名  
`groups` 当前用户所在组  
`groups username` 查看username所在组及组内成员  
`whoami` 当前用户  
`who` 当前登录用户详细  
`useradd -g groupname username` 和 `groupadd groupname` 用来删除用户或用户组 `-d` 指定主目录 `-m` 自动创建主目录 `-s` 指定使用的bash  
`userdel` 和 `groupdel` 用来删除用户或用户组  
`sudo adduser <username> sudo` 将用户添加到sudo组 （其它方式：`sudo usermod -a -G sudo <username>`）**注意** Ubuntu中组名为`sudo`，CentOS中组名为`wheel`
`cat /etc/sudoers` 查看sudo用户
`sudo visudo` 修改`/etc/sudoers`文件，如实现无密码sudo：`user1 ALL=(ALL) NOPASSWD: ALL` 添加此行到文件最后（如果只是sudo个别命令时不需要输密码，将命令的路径添加在`NOPASSWD:`后）  
`usermod -d /usr/newfolder -u uid LOGIN` 指定用户主目录 uid通过`id`命令来获取  
`passwd username` 设置用户密码  
`sudo chsh -s /bin/bash hadoop` 修改用户的bash，需要sudo，或者直接编辑`/etc/passwd`，最后一列表示登陆的shell  
`umask` 权限掩码，如使用`umask 077`修改后，则当前shell创建的文件的权限都是`700`   

### 文件相关 ###

`ls -l dest_dir` //列目录+详细信息 `-lsa` 显示隐藏文件  
`ll` 列目录  
`tree` 显示目录树  
`pwd` 当前目录  
`basename` 取路径中的文件名（就是最后一个`/`之后的部分），常用法 basenam \`pwd\`  
`cd ..` 上一级 ~用户主目录 /系统根目录  
`wget` 文件名 url 下载文件并重命名  
`tar xzf file.tar.gz` 压缩解压 `-x/c/t` 解压/压缩/列出文件 `-z` 压缩文件同时有gzip压缩(文件名如.tar.gz) `-v` 显示正在处理的文件 `-f` 后面接档案名(要创建或要解压的名字)，一般作为最后一个命令选项  
`mv oldname newname` 移动文件/重命名  
`whereis mysql` 定位文件  
`./file-name` 执行一个文件  
`rm -r dir` 递归删除目录  
 find / -name phpmyadmin -type d (-exec rm -r {} \;) //在根目录下查找指定名称指定类型的文件，并执行其他操作如删除 可以使用如`*`等通配符（查找的文件名需要引号）  
`find / -name nginx 2>/dev/null` 屏蔽所有错误，如permission denied等 **注意** `>`前后不要有空格  
`ln -s src dest` //创建软连接，后面的目录链接到前面的目录，如果不加`-s`则为硬链接  
`cp src-file dest-file` //在当前目录下进行重命名复制 `-r` 复制目录时实现递归  
`mkdir -p /dir1/dir2/dir3` //创建目录，在有多级目录不存在的情况下p参数可以一次性创建  
`touch filename` //在无参数时创建一个文件，包含参数时多位修改文件时间戳  
`df -h` //磁盘信息  
`wc` 统计文件 `- c` 统计字节数 `- l` 统计行数 `- w` 统计字数    
`du` 统计目录大小（默认为当前目录下所有目录的大小）  `du dir` 可以查看指定目录 常与`sort`连用，如按大小排序 `du | sort -nr`  
`du -sh` 当前目录大小  
`scp file hadoop@192.168.0.1:/home/test` 远程文件传输，将`file`拷贝到远程  

> username@host:/dir1/dir2 这样的格式即Linux中表示远程目录的方式（注意那个冒号不要少）
> 将Windows10的文件拷贝到Linux，可以在包含待拷贝的目录下，右键选择在此处打开命令窗口，然后输入`bash`进入`Linux`子系统，并使用`scp`传输文件。

`rsync` 远程同步，一种功能更强大的`scp`，需要以软件包方式安装`apt-get install rsync` 用法 `rsync -params src dst` src与dst可以是远程路径 常用参数`-avlH`  
   `-a`  --archive 归档模式，表示以递归方式传输文件，并保持所有文件属性  
   `-v`  --verbose 详细模式输出  
   `-l`   --links 保留软链接  
   `-H`  --hard-links 保留硬链接  

### 重定向 ###

输出重定向 `>`(新建) 或 `>>`(追加) 错误重定向 `2>` 或 `2>>`  
输入重定向 `<`或 `<<`  
`n >& m`  将输出文件 m 和 n 合并  
`n <& m`  将输入文件 m 和 n 合并  
`<< tag xxx tag`     将开始标记 tag 和结束标记 tag 之间的内容(即xxx)作为输入  
标准输入文件 - stdin，文件描述符为0（默认输入）  
标准输出文件 - stdout，文件描述符为1（默认输出）  
标准错误文件 - stderr，文件描述符为2（默认错误输出）  
如：`$command > file 2>&1` 将 stdout 和 stderr 合并后重定向到 file 等价于`&>file` 或者`>&file`（不推荐）
重定向到`/dev/null`的输出会被丢弃，如屏蔽错误输出$ command 2 > /dev/null  

## 文件编辑 ##

`sed` 以行为单位对文本进行新增、替换、删除或选取等处理  
    选项 `-i` 就地修改读取的档案  
    命令 `s` 替换文本中内容 `sed 's/要替换的字符串/新的字符串/g'`  
`tr` 简化版的sed，主要用于文本替换 如`tr "[a-z]" "[A-Z]" ` 表示小写转大写  
**vi相关**  
`ese` 退出编辑模式  
`:q!` 不保存退出  
`:wq` 保存退出  
`i` 进入编辑模式  
`dd` 删除当前一整行  
`u` 撤销最近一次操作  
:set fileencoding=utf-8 / :set bomb / :set nobomb / :set bomb? 转为UTF8编码/设置BOM/删除BOM/查询BOM  

> `grep -r -I -l $'^\xEF\xBB\xBF' /path` 可以检测path目录下所有的BOM的UTF8文件  
> `grep -r -I -l $'^\xEF\xBB\xBF' /path | xargs sed -i 's/^\xEF\xBB\xBF//g'`

其它编辑器  
`nano` 控制Ctrl+(Centos等)  

## 安装/卸载程序 ##

### apt-get 系 ###

`apt-get update` 更新软件列表  
`apt-get upgrade` 更新包，遇到依赖项不再匹配时，停止更新  
`apt-get dist-upgrade` 更新包，并自动更新依赖项，相对于`upgrade`有风险  
`apt-get install/remove/autoremove lxde` 安装/删除  
`apt-get reinstall xxxpackage` 重新安装  
`apt-get purge xxxpackage` 清除包相关所有文件  
`apt-get autoremove` 自动移除不再需要的自动安装的依赖包，可以在purge后执行来清理下系统  
`apt-file find name` 可以实现类似`yum whatprovides`一样的功能，使用前需要安装`apt-file`并执行`apt-file update`  
`apt-cache search xxx| grep dev` 根据关键字查找可用的包  
`dpkg -S file` 对于已安装的包的文件，查找包可使用（文件归属的包）  
`dpkg -s package` 已安装的包的详细信息  
`dpkg -l` 列出所有的包  
`dpkg -listfiles package` 列出包所包含的文件  
`apt` 与`apt-get`有细微不同  
基于apt-get的tasksel（用于一系列apt-get安装）  
`tasksel install lamp-server` #使用tasksel安装LAMP套件  
`tasksel remove lamp-server` #使用tasksel卸载LAMP套件  
`tasksel --list-tasks` #列出将要显示的软件集并退出  
`tasksel --task-packages lamp-server` 查看lamp-server软件集里包含哪些软件  

### rpm/yum 系 ###

> 所有命令需要sudo执行

`rpm -ivh` 安装软件，常用参数  
`yum whatprovides ifconfig` 或 `yum provides ifconfig` 查看哪个包提供了ifconfig  
`yum install net-tools` #安装  
`-y` 所有提示，一律是  
`yum info package` 查看包信息  
`yum list (package)` 列出所有包，或指定包的安装情况  
`yum -y install /path/to/package.rpm` 安装指定的rpm包  
`yum -y localinstall /path/to/package.rpm` 与上面命令等价  
`yum repolist enabled` 列出启用的repository  
**使用yum-config-manager管理yum repository**  
`yum install yum-config-manager` 安装yum-config-manager  
`sudo yum-config-manager --disable/-enable repo-name` 启用或禁用包源  

## 网络相关 ##

`host` DNS查询，类似Win上的`nslookup`  
`traceroute` 路由跳跃点，需要安装同名的包
`sudo netstat -nlp |grep 1723/LISTEN` 查看端口号(root权限)  
`netstat -rn` 核心路由表  
netstat 替代品 ss  
`ss -l` 列出所有监听端口  
`ss -a` 列出所有端口  
`ss -t` 列出tcp端口 `-lt` 所有监听重的tcp接口 `-u` udp端口 `-p` 使用端口的进程(`-ltp`监听tcp端口的进程)  

### 防火墙 ###

**iptables系**  
`sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT` //放开80端口  
`sudo iptables -L` //列出当前放火墙规则 `-n` 显示端口号  
`sudo iptables-restore < /etc/iptables.up.rules` 导入规则到iptables  
`sudo iptables-save > /etc/iptables.up.rules` iptables规则到文件  
`sudo iptables -F` 临时禁用所有防火墙规则（临时放开所有）/清除预设表filter中的所有规则链的规则  
`sudo iptables -X` 清除预设表filter中使用者自定链中的规则  
`sudo iptables -P INPUT DROP` 禁止一切传入  
`sudo iptables -P OUTPUT ACCEPT` 允许一切传出  
`sudo iptables -P FORWARD DROP` 禁止一切转发  
**ufw(Ubuntu)**
`sudo ufw enable/disable` 启用/禁用防火墙  
`sudo ufw reload` 重新载入规则  
**firewalld系**
`systemctl status firewalld` 或 `firewall-cmd --state`  
`systemctl restart firewalld` 重启服务  
`firewall-cmd --zone=public --add-port=80/tcp --permanent` 开放80端口，重启服务即时生效，且效果持久，不用改文件  
`firewall-cmd --reload` 上面的命令不重启实时生效  
`firewall-cmd --get-active-zones` 查看活动区域  

#### net-tools工具集 ####
`ifconfig |egrep "HWaddr|inet addr"` 所有网卡的摘要  

#### ip工具集 ####
`ip addr` #ifconfig 在RHEL7以后不默认安装了  
`ip (-s) link` #网络接口(-s统计信息)  

#### net-manager工具集 ####
`nmcli (con mod eth0 ipv4.dns "114.114.114.114 8.8.8.8")` 修改dns  
`nmcli con up eth0`  
`nmtui` 图形界面的配置工具，完成后需重启网络服务  
`systemctl restart network.service` 需重启网络服务--rhel7  

`route |grep default` 默认路由  

`sudo nano /etc/network/interfaces`  
 //sudo vi /etc/resolv.conf  
`sudo service networking restart`  
`sudo /etc/init.d/networking restart`  
`sudo ifconfig eth0 down` 重启网卡  
`sudo ifconfig eth0 up`  

### 主机名 ###

`sudo nano /etc/hostname` 修改包含主机名的文件(Ubuntu)  
`sudo nano /etc/hosts` 修改hosts文件，映射最新的主机名(Ubuntu)  
`sudo nano /etc/sysconfig/network` 修改包含主机名的文件（其中的HOSTNAME一行）(CentOS)  
`sudo nano /etc/hosts` 修改hosts文件，映射最新的主机名(CentOS)  

### SSH ###  
`apt-get install openssh-server`  
由pem格式文件导出pub格式文件  
`chmod 600 xxx.pem` 修改其权限为600（不然第三步会报错）  
`ssh-keygen -p -f xxx.pem` 改写密钥格式为OpenSSH，（如果询问passphrase可以直接回车）  
`ssh-keygen -e -f xxx.pem > xxx.pem.pub` 生成公钥.pub文件  
`ssh ipaddress/host` ssh客户端登陆  
 `-p port`指定服务器端口  
 `-l user` 指定用户，效果等同`ssh user@host`  
 `-t` 虚拟一个远程服务器的终端(打开一个tty终端)  
 `-i` 使用私钥登录
`ssh user@host "command" cmdParam1 cmdParam1` 可以直接运行远程命令，一般都需要预先配置无密码(publickey验证)登陆远程服务器  

> command如果是脚本，要写完整的绝对路径

`sshd` 启动一个ssh服务以接受客户端的连接  
  `-D` 不以daemon模式运行sshd，即不从控制台detach sshd进程，可以更好的查看`sshd`的工作情况  


## 进程/服务 ##

`ps -ef | grep lxdm` 查看进程，常用参数：  
   `-ef` 查看所有进程及命令行 `-e`等价于`-A` 显示所有进程 `-f` 显示程序关系（即命令行） 常与`grep`连用查找某进程 `ps -ef | grep lxdm`  
   `-aux` 显示所有运行进程（包括服务） `-a` 显示所有用户的进程 `-u`显示进程所属用户 `-x`显示没有附加到终端的进程（开机运行的程序-即服务）  
扩展： ps -e -o 'pid,comm,args,pcpu,rsz,vsz,stime,user,uid' | grep redis|  sort -nrk5 查看内存占用
`kill pid` 参数`-9`表示强制结束  
`kill  $(ps -ef | grep processName | grep -v grep | awk '{print $2}')` 查找进程并结束  
`cp ${installdir}/ctlscript.sh /etc/init.d/bitnami-drupal` //bitnami自动启动  
`update-rc.d -f bitnami-drupal start 80 2 3 4 5 . stop 30 0 1 6 .`  
`sudo /etc/init.d/servicename restart` //Ubuntu服务控制  
`service xxx start/stop/restart` //开始/停止/重启(Ubuntu)  
`service --status-all` 所有服务状态(Ubuntu)  
`systemctl restart/status/enable/is-enabled` 重启服务/服务状态/开机自启动/是否自启动(CentOS)  
`nohup exe  &` 程序在后台保持运行1  
`setsid exe` 程序在后台保持运行2  
`top` 进程管理器 `q`键退出 `M`键按内存排序(注意大写，如果事小写模式，需要按`shift+M`)  `P`按进程占用 `-p pid` - 指定线程信息 `-u user` - 指定用户的进程  
`pmap` 查看进程的内存映像信息 如：`pmap -d pid`  
`sudo chkconfig mysql on` 开机自动运行(旧版Ubuntu)  
`upstart` 开机自动运行(新版Ubuntu) 需要安装 `sudo apt-get install upstart`  
`sleep` 休眠等待，参数单位为s  

## 定时任务 ##

`yum install crontabs` 安装crontab定时任务管理程序  
`crontab -e` 编辑定时任务，计划任务被写入`/var/spool/cron`中（这个文件无法手动编辑）  
`crontab -l` 列出定时任务，日志可见于`/var/log/cron`  
`min hour day month week username cmd` cron计划顺序，`*` 表示全部 `*/min` 表示每分钟 `min1-min2` 时间区段 `min1,min2`第1和2分钟  
`/etc/init.d/cron restart/reload/start/stop` 重启/重新加载配置/启动/停止  

## 开发 ##

`usr/local` 应用程序安装目录  
`home/wwwroot` web发布路径  

## 应用 ##

`curl http://xxx`  
 `-o filename.ext` 使用指定名称保存  
 `-O` 使用默认名称保存
 `-C` 使用断点续传，方便下载大文件
Nginx  
`sudo apt-get install nginx` 安装 &启动  
`sudo /etc/init.d/nginx start`  

Screen 保存bash会话环境  
`sudo apt-get install screen` 安装  
`screen -S sessionName` 创建一个Screen会话，离开时按`Ctrl+A+D`保存会话  
`screen -ls` 列出所有会话  
`screen -r sessionName` 恢复Screen会话  
`screen -x sessionName` 以只读方式连接到一个会话

> 输入`exit`退出scream会话，会提示[screen is terminating]

### 差异 ###

ubuntu nginx配置文件 位置 /etc/nginx/sites-enabled/default  
