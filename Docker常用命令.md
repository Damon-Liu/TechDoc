## docker 命令 ##  

### 系统 ###  

`docker info` 显示docker的信息  

### 镜像相关 ###  

`docker iamges` 列出所有镜像 (-a 显示全部镜像，包含一些中间镜像)  
`docker rmi imagename` 删除镜像，如果这个镜像有多个tag，只有多个tag都被删除后镜像本身才被删除  
`docker pull microsoft/dotnet` 拉取预建的镜像  
`docker pull microsoft/dotnet:1.0.0-core` 拉取指定版本（:tag）  
`docker history imagename` 查看镜像构建过程    
`docker build path` 编译镜像  
 `--rm`移除中间镜像  
 `-t|--tag` 指定标签(imagen-name:tag)  
 `-f` 指定Dockerfile的路径及文件名  
 `--no-cache` 构建镜像时不适用缓存
`docker search imagename` 在Docker Hub中查找镜像

### 容器相关 ###  

`docker ps` 列出运行中的container  
`docker ps -a` 所有container 包括没有运行的
`docker container ls [-a]` 作用同上    
`docker start cname` 运行container  
`docker stop cname` 停止container  
`docker rm cname` 移除container `-f` 强制删除，包括正在运行的  
`docker run --name container-name image-name command-to-run` 将镜像作为容器运行（run=create+start）  
 - `-p`要与EXPOSE结合使用才能真正使暴露的端口可用，如`-p 8080:6666`中8080是外部端口，6666是内部端口  
 - `-P`自动将EXPOSE的端口发出出来  
 - `--rm`容器退出后自动删除相关文件，开发调试时很方便  
 - `--link` 链接到容器，可以指定别名 `--link other-container:alias` other-container及alias可以在当前容器中作为hostname使用  
 - `-d` 在后台运行container，即detach启动container的shell，与下面的`-it`相对  
 - `-it` `-i`与`-t`的结合，这两个项常一起用，`-t`表示分配一个tty终端，`-i`保持container接收STDIN状态。结合在一起即以交互式方式运行container  
 - ` -v /home/orgname/projname:/publish:ro microsoft/dotnet:1.0.3-runtime /bin/bash` 挂载本地目录（左）到container（右），`:ro`表示只读挂载（挂在会自动创建卷）  
 - `--restart=always image-name` 指定运行策略，退出后是否重启等，所有选项：`no(default) | on-failure | unless-stopped | always`  
 - `--privileged` 以特权模式运行，container可以访问主机的设备，在这种模式下，container中可以嵌套运行container  
 - `-h/--hostname` 指定container的主机名  
 - `-e "key:value"` 配置environment

`docker create --name imagename containername:1.0` 基于镜像创建容器  
`docker inspect --format='{{.State.Status}}' web31` 查看容器状态  
`docker pause container` 暂停容器  
`docker unpause container` 恢复容器  
`docker rename cont1 cont2` #重命名容器  
`docker top cont1` 显示容器运行的进程  
`docker logs [-f] containername` 显示容器的日志  
`docker kill cont1` 强制结束容器  
`docker cp /home/user/publish/Dockerfile cname:/webapp` #从 host 拷贝文件到 container 里面，也可以反向copy  
`docker import cnamezip cimage -m "imported on 0916"` 从压缩文件创建一个镜像  
`docker exec -it containerid/name /bin/bash`   exec用于在容器中执行一个命令，如上命令的作用就是进入容器并打开bash（`-it`等效`-i -t`）  
`docker attach containerid/name` 附加到一个运行中的container，和上面的命令差不多，只是上面的命令是新打开一个bash，使用`Ctrl+p`与`Ctrl+q`退出  
`docker update --restart=always containerid/name` 更新容器的运行策略  

### 批量操作 ###  

`docker ps | awk  '{print $1}' | xargs docker stop` 关闭所有正在运行容器  
`docker ps -a | awk  '{print $1}' | xargs docker rm` 删除所有容器应用  
`docker rm $(docker ps -a -q)` 与上面的等效  

### 导出&还原 ###  

`docker export -o path/container-bak-name containername/containerid` 将一个**container**打包为一个压缩文件  
`docker import path/container-bak-name repoName:tag` 还原压缩文件为image，可以指定image的repository和tag  
`docker save -o path/image-bak-name image-name` 备份**image**到一个文件  
`docker load -i path/image-bak-name` 导入**image**备份  
`docker save image-name | bzip2 | pv | ssh user@host 'bunzip2 | docker load'` 将image备份、压缩、传输到远程电脑并还原  

### 注册中心 ###  

`docker login ip/hostname` 登录到注册中心  
`docker pull hostname/library/image:tag` 由注册中心拉取指定镜像  
`docker tag source:tag target:tag` 关联源image与目标image，push前所需要的操作  
`docker push hostname/library/image:tag` 推送到注册中心  

### Docker Machine ###  

`docker-machine ls` 列出docker虚拟机  

## Dockerfile 语法 ##  

**FROM**：指定进行的基础镜像（**必须**是第一条指令）`FROM ubuntu`  
**Add**：将本地文件或者网络上文件存储到容器中的指定目录  `ADD [source directory or URL] [destination directory]` 如 `ADD /my_app_folder /my_app_folder`  
**USER**：设置该镜像的容器的主进程所使用的用户，以及后续**RUN**, **CMD** 和 **ENTRYPOINT** 指令运行所使用的用户 `USER 751`(UID)  
注意：如果未设置将使用基础镜像的USER设置  
**COPY**：与ADD区别是不支持Url下载，压缩文件存储到容器时不会被解压  
**CMD**：在容器被创建后执行的命令  
格式：  

 - CMD ["executable","param1","param2"] (如同exec的效果，首选)  
 - CMD ["param1","param2"] (作为 ENTRYPOINT 的参数)  
 - CMD command param1 param2 (作为 shell 运行)  

注意：一个Dockerfile里**只能有一个**CMD，如果有多个，只有最后一个生效  
**WORKDIR**：设置**CMD**所指定命令的执行目录 `WORKDIR /path`  
**RUN**：在构造容器时候所执行的命令，结果会生成镜像中的一个新层 `RUN aptitude install -y ntp`  
**ENTRYPOINT**：设置默认应用，会保证每次容器被创建后该应用都会被执行  
注意：一个Dockerfile中**CMD** 和**ENTRYPOINT** 指令至少指定一个，同时存在以**ENTRYPOINT**为准  
**ENV**：设置环境变量，对容器内环境有效 `ENV abc=1`  
**EXPOSE**：向容器外暴露一个端口 `EXPOSE 8080`  
**MAINTAINER**：设置镜像的作者 `MAINTAINER authors_name` (可以在任意地方使用)  
**VOLUME**：允许容器访问host上某个目录 `VOLUME ["/dir_1", "/dir_2" ..]`  
**HEALTHCHECK**：容器健康检查(docker1.12) 语法为 `HEALTHCHECK [OPTIONS] CMD command`  
示例：  
`HEALTHCHECK --interval=10s --timeout=2s CMD curl -f http://localhost:8888/ || exit 1`  
注意要保证HEALTHCHECK中的CMD在容器中可用  
**ARG**：定义参数 `ARG argname=val` 使用参数 `$argname`  

### 网络 ###  

`docker network create`  

### 存储 ###  

`docker volume create` 创建卷
`docker volume ls` 创建卷
