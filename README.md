****
内容参考`/docs/AbutionGraph-2.8.0-安装文档.docx`中`AbutionGraph-完整版安装`的内容
****
# AbutionGraph下载
modelscope下载地址:[AbutionGraph时序知识图谱数据库](https://modelscope.cn/models/AbutionGraph/abution_graph_db_install_package/summary)
SKD下载方式：
```
#模型下载  
from modelscope import snapshot_download  
model_dir = snapshot_download('AbutionGraph/abution_graph_db_install_package',cache_dir='/home/modelscope') #修改了默认下载地址
```
Git下载方式：
```
git clone https://www.modelscope.cn/AbutionGraph/abution_graph_db_install_package.git
```
GitHub地址:[AbutionGraph](https://github.com/ThutmoseAI/AbutionGraph)

AbutionGraph-2.8 集成软件参考版本信息：
# 软硬件要求
## 软件
清空缓存命令：echo 1 > /proc/sys/vm/drop_caches
基础组件:【】内标注是解压后的组件相对路径

Jdk-1.8.0_211 (Abution-jshell使用自带jdk11)【/abution_graph_db_install_package/jdk1.8.0_211 】

Hadoop-3.3.5/Hadoop-2.7.7【/abution_graph_db_install_package/hadoop-3.3.5】

Zookeeper-3.4.13（默认包含在Abution安装包中）【./abution_graph_db_install_package/abution-graph-db-2.8.1/zk】

大数据生态组件（推荐版本）:

Python-3.8.5

Scala-2.12.8

Spark-3.3.2 （AI算法计算引擎；AbutionGCS-图计算定制版；Mlep可融合Python）

MySQL-8.0

Flink-1.12.1（实时计算引擎）

Hive-3.1.3（数据仓库API，推荐配置运行在Spark上）

RocketMQ-4.5.3

Kafka-2.2.1

Jcache

**安装目录结构（JDK、Hadoop、AbutionDB），为了少改动系统环境变量，建议都装在/thutmose/app/（**mkdir -p /thutmose/app**）下**
## 硬件版本
最好是使用CentOS7或者Ubuntu18以上系统，不满足的话请升级系统内核gcc版本至8以上版本。由于我们实际上同时在整个群集中运行两个或三个系统：HDFS，AbutionDB和MapReduce，因此硬件通常由4至8个内核和8至32 GB RAM组成。这样一来，每个正在运行的进程至少可以具有一个内核，每个内核可以具有2-4 GB，提高吞吐和速度只需配置进程使用更多的资源。

一个运行HDFS的内核通常可以使2到4个磁盘繁忙，因此每台计算机通常可能只有2 x 300GB磁盘，最多有4 x 1TB或2TB磁盘。

AbutionDB的最低配置：如使用2H4GB的1u服务器，建议每台计算机最多运行两个进程-即DataNode和AbutionServer或DataNode和MR Worker，不建议同时启动3个服务。这个约束是为机器上的所有进程提供足够的可用堆空间。

**服务器设置**

1. 关闭防火墙；

2. （已安装则跳过）安装SSH无密通信：sudo apt-get install openssh-server，并执行：ssh-keygen -t rsa，一路回车即可，然后复制.ssh/id_rsa.pub内容到.ssh/authorized_keys中，最后启动服务：service ssh start；

3. （可选）在群集内运行NTP协议同步时间，时间不一致可能会导致节点间的数据同步延缓；

4. 如果是阿里等云服务器，请把端口号9090、50070、9990-9999 加入到安全组，否则web无法连通。

# 环境配置
## **规范化软件目录**
1. 执行命令新建安装目录：`
   ```
	mkdir -p /thutmose/app/abution_graph_db_install_package/
	```

2. 上传所有安装文件到：abution_graph_db_install_package/下

3. 依次解压所有安装文件到文件夹abution_graph_db_install_package/下：tar -xvf xxx.tar.xz
```
# 进入abution_graph_db_install_package/
tar -xvf xxx.tar.xz -C abution_graph_db_install_package/
```

4. 对所有软件目录创建软连接到/thutmose/app/（如上图目录结构）：

cd /thutmose/app/

ln -s ./abution_graph_db_install_package/jdk1.8.0_211 ./jdk

ln -s ./abution_graph_db_install_package/scala-2.12.8 ./scala【无，需自安】

ln -s ./abution_graph_db_install_package/hadoop-3.3.5 ./hadoop

ln -s ./abution_graph_db_install_package/abution-graph-db-2.8.1 ./abution

ln -s ./abution_graph_db_install_package/hive-3.1.3 ./hive【无，需自安】

ln -s ./abution_graph_db_install_package/spark-3.3.2 ./spark【无，需自安】
## 配置系统环境变量
如为root用户，追加以下内容到~/.profile
```
## Hadoop-3.x

export HADOOP_HOME=/thutmose/app/hadoop

export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop

export CLASSPATH=".:$JAVA_HOME/lib:$CLASSPATH"

export HADOOP_COMMON_HOME=$HADOOP_HOME

export HADOOP_HDFS_HOME=$HADOOP_HOME

export HADOOP_MAPRED_HOME=$HADOOP_HOME

export HADOOP_YARN_HOME=$HADOOP_HOME

export HADOOP_INSTALL=$HADOOP_HOME

export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native

export HADOOP_LIBEXEC_DIR=$HADOOP_HOME/libexec

export JAVA_LIBRARY_PATH=$HADOOP_HOME/lib/native:$JAVA_LIBRARY_PATH

export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin

## 遇到权限问题解开注释

export HDFS_DATANODE_USER=root

export HDFS_DATANODE_SECURE_USER=root

export HDFS_SECONDARYNAMENODE_USER=root

export HDFS_NAMENODE_USER=root

export HADOOP_SHELL_EXECNAME=root

export YARN_RESOURCEMANAGER_USER=root

export YARN_NODEMANAGER_USER=root

## AbutionDB

export ABUTION_HOME=/thutmose/app/abution

export ABUTION_CONF_DIR=$ABUTION_HOME/conf

export PATH=$ABUTION_HOME/bin:$PATH

## Zppkeeper

export ZOOKEEPER_HOME=$ABUTION_HOME/zk

export PATH=${ZOOKEEPER_HOME}/bin:$PATH

## java8

export JAVA_HOME=/thutmose/app/jdk

export JRE_HOME=${JAVA_HOME}/jre

export CLASSPATH=.:${JAVA_HOME}/lib/dt.jar:${JAVA_HOME}/lib/tools.jar

export PATH=${JAVA_HOME}/bin:${JRE_HOME}/bin:$PATH

## scala-2.12.8【无可忽略】

export SCALA_HOME=/thutmose/app/scala

export PATH=${SCALA_HOME}/bin:$PATH

export TERM=xterm-color

## Hive【无可忽略】

export HIVE_HOME=/thutmose/app/hive

export PATH=${HIVE_HOME}/bin:$PATH

## spark【无可忽略】

export SPARK_HOME=/thutmose/app/spark

export PATH=${SPARK_HOME}/bin:$PATH

#export PYSPARK_PYTHONPATH=${SPARK_HOME}/bin:${SPARK_HOME}/python:${SPARK_HOME}/python/lib/py4j-0.10.7-src.zip:$PATH
```
# 安装AbutionGraph
注：我们假定HDFS已经正常运行（单机版可不用，伪分布式可启用），具体在abution.properties中配置，。

1. 进入目录：cd /thutmose/app/abution/conf

2. 更改以下5个文件中localhost为主机名（可选,单机建议无需改动）

	：gc、masters、monitor、slaves、tracers；高可用和多计算节点可依据服务器数量配置为多行的不同主机名。

3. 更改数据库配置文件abution.properties

	1）instance.volumes：更改localhost为主机名（可选,单机建议无需改动）
	
	2）配置数据库为本地单机模式（如下）或分布式模式（改为使用hdfs目录，推荐单机模式也使用hdfs持久化数据）

## 设置数据持久化HDFS默认路径
```

#instance.volumes=hdfs://localhost:9000/abution

instance.volumes=file:///thutmose/app/abution/data
#这里因为data内已经有了数据，在此路径初始化会报错，故改为test1文件夹
```

3）tserver.memory.maps.native.enabled：

	建议设置true，可以解决JavaGC暂停的难题，提高AbutionDB性能，同时修改lib/libabution.so文件为对应服务器版本。
	
	其中关联参数tserver.memory.maps.max和table.compaction.minor.logs.threshold用于调节吞吐量。

4. 启动Zookeeper：
```
cd /thutmose/app/abution/zk/bin
#在环境配置成功的情况下是不用cd进zk/bin的，但如何配置失败，zkServer.sh是在zk/bin下的，可以手动启动
bash zkServer.sh start

```

5. 初始化Abution：
```
Source /etc/profile

abution init --instance-name [AbutionGraph可修改] --user root --password [abutiongraph]
```


6. 启动Abution：abution-db-start.sh
```
#这里在看app/abution/bin/下没有abution-db-start.sh,只有abution-local-start.sh故
abution-local-start.sh
#使用abution-loccal-start.sh启动，与abution-cluster start功能相同
```

jps查看是否启动成功，正常启动会有>5个Main服务，否则检查abution/logs排除异常。如以下：
```
ZooKeeper JMX enabled by default
Using config: /thutmose/app/abution/zk/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
ps : monitor already running (1937318)
Starting tablet servers .... done
ps : tserver already running (1937406)
ps : master already running (1937681)
ps : gc already running (1937756)
ps : tracer already running (1937809)
```
zookeeper启动，monitor、tserver、master、gc、tracer均已启动成功
AbutionDB监控页面：ip:9995

AbutionGraph-UI页面：ip:9995/graph

Abution起停命令：
```
abution-cluster stop
abution-cluster start #与abution-local-start.sh功能相同
abution-cluster restart
```

停止zk：`zkServer.sh stop` （启动Abution前必须先启动zk）
# AbutionGraph命令
停止AbutionGraph集群：
1. abution-rest-stop.sh（若几秒后还存在Bootstrap进程，请kill掉）
2. abution-db-stop.sh
3. zkServer.sh stop（可选）
启动AbutionGraph集群：
1. zkServer.sh start （已启动请忽略）
2. abution-db-start.sh
3. abution-rest-start.sh

HDFS启动/停止：start-dfs.sh / stop-dfs.sh
添加AbutionDB节点（在某个节点服务器中执行）：start-here.sh （需$ABUTION_HOME/conf/slaves中已具有该节点）
停止AbutionDB节点（在某个节点服务器中执行）：stop-here.sh 

清空缓存命令：echo 1 > /proc/sys/vm/drop_caches

更多运维命令略。。。
