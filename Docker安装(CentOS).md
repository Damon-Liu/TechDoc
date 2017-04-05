## Docker ##  

### 首先删除集成的Docker版本 ###  

默认yum源中的docker版本太旧  
集成的包名为`docker`而Docker官方包名已改为`docker-engine`  

    sudo yum -y remove docker docker-common container-selinux  

下面会与`docker-engine`冲突的包也要删除  

    sudo yum -y remove docker-selinux  

### 添加docker的yum源 ###  

#### 方法1 推荐方法 ####  

安装`yum-utils`以便可以使用其中的`yum-config-manager`工具  

    sudo yum install -y yum-utils  

添加**稳定**库  

```bash  
sudo yum-config-manager \  
    --add-repo \  
    https://download.docker.com/linux/centos/docker-ce.repo  
```  

> ce表示Community Edition  

### 安装docker并启动 ###  

首先更新包  

    sudo yum makecache fast  

然后安装并启动Docker  

    sudo yum -y install docker-ce  
    sudo systemctl start docker  

> 如果需要安装指定版本，使用下面的命令，首先列出所有版本，然后安装指定版本  
  `yum list docker-engine.x86_64  --showduplicates |sort -r`  
  `sudo yum -y install docker-engine-<VERSION_STRING>`  

### 检查是否安装成功 ###  

运行下面的命令，检查docker的安装  

    sudo docker info  
    sudo docker run hello-world //官方入门image测试  

### 其它 ###  

重启docker  

    sudo systemctl daemon-reload  
    sudo systemctl restart docker  

### 升级 ###  

在[这里](https://yum.dockerproject.org/repo/main/centos/)下载比当前安装版本高的Docker的rpm包。  
然后执行下面的命令安装：  

    yum -y install /path/to/package.rpm  

惯例，安装后启动测试  

    sudo systemctl restart docker  
    sudo docker run hello-world  

### 卸载 ###  

    sudo yum -y remove docker-engine  
    sudo rm -rf /var/lib/docker //手动删除所有的image，container  

注意，手动编译过的配置文件需要手动删除  

## Docker Compose ##  

### 使用curl安装Docker Compose ###  

查看Docker Compose的[发布页面](https://docs.docker.com/compose/install/)确定版本，使用下面的命令安装（将其中版本换成最新版本）。  

    curl -L https://github.com/docker/compose/releases/download/1.11.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose  

> 命令中的`uname -s`及`uname -m`在CentOS7下分别返回`Linux`和`x86_64`，可以用于确定需要下载的Docker Compose的版本  

> 如果不是root账户，可能需要`sudo`提升权限，建议使用`sudo -i`模拟root账户  

赋予执行权限  

    chmod +x /usr/local/bin/docker-compose  

测试安装  

    docker-compose --version  

> **注意** CentOS7的root账号$PATH中没有`/usr/local/bin`，为了让root可以使用`docker-compose`，可以将其copy到`/usr/bin`中一份  

### 安装Docker Compose - Command-line completion ###  

首先安装依赖包  

    yum install bash-completion  

通过curl下载安装  

    curl -L https://raw.githubusercontent.com/docker/compose/$(docker-compose version --short)/contrib/completion/bash/docker-compose -o /etc/bash_completion.d/docker-compose  

### 附：卸载方法 ###  

    rm /usr/local/bin/docker-compose  
