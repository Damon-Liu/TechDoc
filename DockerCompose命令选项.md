### docker compose yml配置结点说明 ###  



| 结点| 值类型 | 值 | 作用  | | 等效docker选项 |  
| -------  |----- | ------ | ------ | ----- |  --- |
| version | Scalar |  | docker compose版本(1/2/2.1/3)，不写默认为1 |  |  
| services | Mapping | *service* obj | docker compose版本，不写默认为1 |  |  
| services/*service* (name) | Mapping |  | | |  
| services/*service*/image | Scalar |  | 用这个image启动一个容器来作为当前service | 与下一个，两个是**必选**之一 |  
| services/*service*/build | Mapping |  | 指定Dockerfile生成一个image并用来启动容器作为当前service | 与image**二选一** |  
| services/*service*/build/context | Scalar | . 一般用`.`表示当前目录 | 包含Dockerfile的目录 |  |  
| services/*service*/build/dockerfile | Scalar |  | Dockerfile名字，如果没有改过，可以省略这项 |  |  
| services/*service*/build/args | Mapping 或 Sequence |  | 对象将作为Dockerfile编译过程中的参数被提供 |  |  
| services/*service*/container_name | Scalar |  | 指定服务的容器的名称 |  | docker run --name |  
| services/*service*/ports | Sequence |  | 开放端口(对外暴露端口:内部暴露端口) |  | docker run -p |  
| services/*service*/expose | Sequence |  |  | 容器间访问的端口，不提供外部访问 | docker run --expose |  
| services/*service*/links | Sequence | 服务名(:别名，可选) | 用于连接到其它服务 |  | docker run --link |  
| services/*service*/volumes | Sequence | 主机目录(:容器目录:ro/rw) | 挂载主机目录到容器 |  | docker run -v |  
| services/*service*/volumes_from | Sequence | 服务名:ro/rw 或 container:容器名:ro/rw  | 将服务/容器作为挂载 |  |  
| services/*service*/environment | Mapping 或 Sequence |  | 添加环境变量 |  | docker run -e |  
| services/*service*/env_file | Sequence |  | 环境变量文件，作用同environment |  |  |  
| services/*service*/depends_on | Sequence |  | 指定服务（如redis等）依赖 | 类似links，但优先级高，先于其它服务启动 |  |  
| services/*service*/external_links | Sequence | 类似links | 外部依赖，当前docker compose之外，但同一个子网内 |  |  
| services/*service*/extra_hosts | Sequence | “hostname:ip” | 添加主机名映射 | 添加到`/etc/hosts` |  |  
| services/*servcie*/deploy |  |  | version3新增 |  |  
| services/*servcie*/deploy/restart_policy |  |  | 配置重启策略 |  |  
| services/*servcie*/deploy/restart_policy/condition | Scalar |  | 指定什么情况下重启 | docker run --restart *xxx* |  

> `services/*service*/image`与`services/*service*/build`同时出现时，会将image结点的值作为name给build生成的image。  

### 命令 ###  

`docker-compose build` 编译docker compose文件中的镜像  
`docker-compose up` 编译image，设置网络，并启动docker compose定义的服务 `-d` 后台运行  
`docker-compose down` 停止docker compose中一系列服务，删除容器并删除网络  
`docker-compose start` 启动build的镜像（即启动服务），参数可以指定具体启动的服务名  
`docker-compose stop` 与`start`相对，这个命令用来停止服务  
`docker-compose run SERVICE_NAME` 运行服务 `-e`指定环境变量  
`docker-compose config` 检查docker compose通过yml得到的配置，及通过env得到的环境变量  

选项：  
`-f` 显式定义docker-compose.yml文件的位置及名称，可以定义多次，配置文件将叠加。  
