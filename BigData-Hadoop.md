
### HDFS ###

`./bin/hdfs dfs -mkdir -p /user/hadoop` 创建主目录
`./bin/hdfs dfs -mkdir test` 创建目录
`hadoop fs -chmod` 修改目录权限
`hadoop fs -rmdir dir` 删除一个目录
`./bin/hdfs dfs -put localfile [localfile2] dstfile` 导入文件到hdfs
`./bin/hdfs dfs -copyFromLocal /home/hadoop/test test` 类似于上述命令，但不同的`-put`可以一次导入多个文件且可以接受`stdin`的输入。而这个参数只能导入一个文件且不接受`stdin`输入
`hdfs dfs -get /user/hadoop/file localfile` 将HDFS中的文件(左)复制到本地(右)
`hdfs dfs -getmerge max-temp max-temp-local` 合并Reduce输出到一个文件
`hadoop fs -expunge` 清空HDFS回收站中（超过最小保存时限）的文件
`hadoop dfsadmin -safemode get` 查看namenode是否处于安全模式
`hadoop dfsadmin -safemode wait` 等待退出安全模式
`hadoop dfsadmin -safemode enter` 进入安全模式
`hadoop dfsadmin -safemode leave` 退出安全模式
`hadoop dfsadmin -metasave` 转储HDFS元数据（包含已连接的datanode和正在被复制或删除的块）
`hadoop fsck <path>` 检查HDFS健康状况（path可以是`/`或具体文件夹`/path`）
  `-files` 显示第一行信息，包括文件名称、大小、快数量、健康状态
  `-blocks` 文件所在的各个块，每个块一行
  `-racks` 块所在的datanode及机架位置
`hadoop dfsadmin -refreshNodes` 将include文件中一系列datanode更新到namenode，如果exclude文件不为空，也会由namenode解除/删除（具体看include是否有相同节点）
`hadoop mradmin -refreshNodes` 将include文件中一系列tasktracker更新至jobtracker，如果exclude文件不为空，也会由jobtracker解除/删除（具体看include是否有相同节点）
`hadoop dfsadmin -upgradeProgress` 查看升级进度
`hadoop dfsadmin -finalizeUpgrade` 定妥升级（删除旧版本备份）


`hdfs oiv` 检测fsimage文件
`hdfs oev` 检测edits文件

`start-dfs.sh -upgrade` 升级（更新namenode元数据）
`start-dfs.sh -rollback` 回滚到旧版HDFS
`start-balancer.sh` 开始执行集群均衡


命令区别：
`./bin/hdfs dfs`等价于已经被废弃的`./bin/hadoop dfs`
`./bin/hadoop fs`除了可以用于HDFS，还可以用于本地磁盘或S3，参数与`./bin/hdfs dfs`基本一致

`./bin/hdfs fsck / -files -blocks` 现实块信息。这里的参数是现实hdfs中的文件由哪些块构成

`./sbin/start-dfs.sh` 启动HDFS
`./sbin/stop-dfs.sh` 停止HDFS
`./bin/hdfs namenode -format` 格式化HDFS

### 文件相关 ###

`hadoop fs --text file.seq` 以文本形式显示顺序文件，会自动检测文件格式及是否压缩
`hadoop MapFileFixer mapfile.seq` 为MapFile的data部分(重新)建立索引部分(index)

### MapReduce ###

`hadoop jar /local_dir/xxx.jar mainClass arg1 arg2` 在MapReduce上运行分布式任务
  arg1,arg2一般是代表输入数据与输出的位置，这个位置一般都表示HDFS路径
  mainClass表示包含Main函数的Hadoop执行类，在命令中输入这个类名时大小写不敏感，Hadoop最自动在jar包中查找匹配的类。

简化的方法，添加环境变量指定包含hadoop的jar包的位置（本地卷位置，非HDFS路径）：
`export HADOOP_CLASSPATH="~/test/hadoop/"`
然后上面的命令可以简化为（省略指定jar包）：

    $HADOOP_INSTALL/bin/hadoop mainClass input/  output/

**选项**

`-conf conf-file-path` 以指定的配置文件运行任务
`-D property=value` 覆盖配置文件配置，包括使用`-conf`指定文件中的配置，注意`-D`后空格可有可无
`-fs uri` 等价于`-D fs.default.name=uri` 指定所使用文件系统的uri
`-jt host:port` 等价于`-D mapred.job.tracker=host:port` 设置jobtracker的地址和端口
`-libjars jar1,jar2..` 从本地文件系统中将jar包复制到jobtracker使用的文件系统（一般是HDFS，可由`-fs`指定），并加入MapReduce任务类路径
`-files file1,file2..` 从本地文件系统中将文件复制到jobtracker使用的文件系统供MapReduce使用，一般都是任务输入文件
`-archives arch1,arch2..` 从本地文件系统中将文件复制到jobtracker使用的文件系统并解压存档文件保证MapReduce任务可以使用，一般都是任务输入文件

**通过HadoopStreaming运行MR任务**
`hadoop jar $HADOOP_INSTALL/share/hadoop/tools/lib/hadoop-streaming-2.6.5.jar -param1 xxx `

> 版本号可以使用`*`代替，如上面命令文件部分可写为：hadoop-streaming-*.jar

通过HadoopStreaming运行以bash脚本编写的任务（示例）：

MapReduce任务相关的文件在计算时要处于计算节点中，通过提交任务时指定`-file`或`-files`参数来实现自动将相关文件提交到计算节点。`-file`选项默认文件处于当前HDFS用户的主目录，如果在其中找不到会报错，也可以通过在`-files`中给出完整HDFS路径，且`-files`参数可以一次接收多个文件，文件名/路径之间逗号分隔。
`-files`属于*一般*选项，而`-file`属于*streaming*选项，在一条命令中一般选项要放在streaming选项之前。（下面的例子给出的两段命令是等价的，第二段目前有问题）
**注意**如果提交任务时，文件还不在HDFS，要首先将任务相关文件放入HDFS中。（一般是放入HDFS用户的主目录）

> 下面是只有Map无Reduce的示例

    $HADOOP_INSTALL/bin/hadoop jar $HADOOP_INSTALL/share/hadoop/tools/lib/hadoop-streaming-2.6.5.jar \
      -D mapreduce.job.reduces=0 \
      -D mapreduce.map.speculative.execution=false \
      -D mapreduce.task.timeout=12000000 \
      -input ncdc_files.txt \
      -inputformat org.apache.hadoop.mapred.lib.NLineInputFormat \
      -output output \
      -mapper load_ncdc_map.sh \
      -file ncdc_files.txt \
      -file load_ncdc_map.sh

    $HADOOP_INSTALL/bin/hadoop jar $HADOOP_INSTALL/share/hadoop/tools/lib/hadoop-streaming-2.6.5.jar \
      -D mapreduce.job.reduces=0 \
      -D mapreduce.map.speculative.execution=false \
      -D mapreduce.task.timeout=12000000 \
      -files hdfs://localhost:9000/user/hadoop/ncdc_files.txt,hdfs://localhost:9000/user/hadoop/load_ncdc_map.sh \
      -input ncdc_files.txt \
      -inputformat org.apache.hadoop.mapred.lib.NLineInputFormat \
      -output output \
      -mapper load_ncdc_map.sh

`hadoop job -history /job-output` 查看作业的历史，作业输出目录下/_logs/history文件夹存放作业历史的备份，此目录位置`hadoop.job.history.location`来控制

### YARN ###

查看运行的任务：

    yarn application -list

停止运行的任务：

    yarn application -kill $ApplicationId
