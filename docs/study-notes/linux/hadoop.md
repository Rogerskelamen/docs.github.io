下载相应的文件：

- 下载Hadoop

  我们去官网下载：http://hadoop.apache.org/

  输入`wget`下载`Hadoop`；

  ```bash
  wget http://mirrors.tuna.tsinghua.edu.cn/apache/hadoop/common/hadoop-2.7.7/hadoop-2.7.7.tar.gz
  ```

- 下载jdk

  前往`O\fracle`的官网下载`JDK`： 


  http://www.o\fracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html

  ![](https://gitee.com/rogerskelamen/mdpic/raw/master/img/image-20200911174836331.png)

将jdk的**压缩包放在系统的**`/opt`**目录下**

> 解压

首先在右侧命令行中创建一个`/app`文件夹，我们之后的软件都将安装在该目录下。 命令：`mkdir /app`

然后，切换到`/opt`目录下，来查看一下提供的压缩包。

![](https://gitee.com/rogerskelamen/mdpic/raw/master/img/image-20200911175036316.png)

#### 配置环境变量

解压好`JDK`之后还需要在环境变量中配置`JDK`，才可以使用，接下来就来配置`JDK`。 输入命令：`vim /etc/profile` 编辑配置文件；

在文件末尾输入如下代码：

```bash
JAVA_HOME=/app/jdk1.8.0_171
CLASSPATH=.:$JAVA_HOME/lib/tools.jar
PATH=$JAVA_HOME/bin:$PATH

export JAVA_HOME CLASSPATH PATH
```

最后：`source /etc/profile`使刚刚的配置生效。

#### 测试

最后我们可以测试一下环境变量是否配置成功。 输入：`java -version` 出现如下界面代表配置成功。

![](https://gitee.com/rogerskelamen/mdpic/raw/master/img/image-20200911175515927.png)



---



> 接下来我们开始正式整Hadoop：
> 首先是解压

将解压好的文件移动到`/app`目录下。

我们来切换到`app`目录下修改一下`hadoop`文件夹的名字。
通过`mv`指令来改名字。

![](https://gitee.com/rogerskelamen/mdpic/raw/master/img/image-20200911175905236.png)

## 配置Hadoop环境（这是一大头）

##### 设置SSH免密登录

在之后操作集群的时候我们需要经常登录主机和从机，所以设置`SSH`免密登录时有必要的。

输入如下代码：

```bash
 ssh-keygen -t rsa -P ''
```

生成无密码密钥对，询问保存路径直接输入回车，生成密钥对：`id_rsa和id_rsa.pub`，默认存储在`~/.ssh`目录下。  接下来：把`id_rsa.pub`追加到授权的`key`里面去。

```bash
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

然后修改权限：==很多时候我们会忘记搞这个==

```bash
chmod 600 ~/.ssh/authorized_keys
```

接着需要启用`RSA`认证，启动公钥私钥配对认证方式：
`vim /etc/ssh/sshd_config`  ； 修改`ssh`配置：

```bash
RSAAuthentication yes # 启用 RSA 认证
PubkeyAuthentication yes # 启用公钥私钥配对认证方式
AuthorizedKeysFile %h/.ssh/authorized_keys # 公钥文件路径
```

然后重启ssh

```bash
service ssh restart
```

好了准备工作已经做完了，我们要开始修改`Hadoop`的配置文件了，总共需要修改`6`个文件。分别是：*(实际上并不需要这么多)*

- hadoop-env.sh；
- yarn-env.sh ；
- core-site.xml；
- hdfs-site.xml；
- mapred-site.xml；
- yarn-site.xml。

##### hadoop-env.sh 配置

首先我们切换到`hadoop`目录下

`cd /app/hadoop3.1/etc/hadoop/`

编辑  `hadoop-env.sh`在文件中插入如下代码： 

```bash
# The java implementation to use.  
#export JAVA_HOME=${JAVA_HOME}  
export JAVA_HOME=/app/jdk1.8.0_171
```

##### yarn-env.sh 配置

编辑`yarn-env.sh` 插入如下代码：

```bash
export JAVA_HOME=/app/jdk1.8.0_171
```

##### core-site.xml配置

这个是核心配置文件我们需要在该文件中加入`HDFS`的`URI`和`NameNode`的临时文件夹位置，这个临时文件夹在下文中会创建。

```xml
<configuration>  
 <property>  
    <name>fs.default.name</name>  
    <value>hdfs://localhost:9000</value>  
    <description>HDFS的URI，文件系统://namenode标识:端口号</description>  
</property>  
<property>  
    <name>hadoop.tmp.dir</name>  
    <value>/usr/hadoop/tmp</value>  
    <description>namenode上本地的hadoop临时文件夹</description>  
</property>  
</configuration>  
```

##### hdfs-site.xml文件配置

`replication`指的是副本数量，我们现在是单节点，所以是`1`。

```xml
<configuration>  
<property>  
    <name>dfs.name.dir</name>  
    <value>/usr/hadoop/hdfs/name</value>  
    <description>namenode上存储hdfs名字空间元数据 </description>   
</property>  
<property>  
    <name>dfs.data.dir</name>  
    <value>/usr/hadoop/hdfs/data</value>  
    <description>datanode上数据块的物理存储位置</description>  
</property>  
<property>  
    <name>dfs.replication</name>  
    <value>1</value>  
</property>  
</configuration>  
```

##### mapred-site.xml文件配置

```xml
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
```

##### yarn-site.xml配置

```xml
<configuration>  
<property>  
        <name>yarn.nodemanager.aux-services</name>  
        <value>mapreduce_shuffle</value>  
</property>  
<property>  
        <name>yarn.resourcemanager.webapp.address</name>  
        <value>192.168.2.10:8099</value>  
        <description>这个地址是mr管理界面的</description>  
</property>  
</configuration>
```

##### 创建文件夹

我们在配置文件中配置了一些文件夹路径，现在我们来创建他们，在`/usr/hadoop/`目录下使用**hadoop**用户操作，建立`tmp`、`hdfs/name`、`hdfs/data`目录，执行如下命令：

`mkdir -p /usr/hadoop/tmp `

 ``mkdir /usr/hadoop/hdfs `

`mkdir /usr/hadoop/hdfs/data `

`mkdir /usr/hadoop/hdfs/name`

##### 将Hadoop添加到环境变量中

```bash
export HADOOP_HOME=/app/hadoop3.1
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```

最后使修改生效：`source /etc/profile`

##### 格式化

在使用`Hadoop`之前我们需要格式化一些`hadoop`的基本信息。 使用如下命令：

```bash
hadoop namenode -format
```

#### 设置root权限启动`hadoop`

在`/hadoop3.1/sbin`路径下： `cd /app/hadoop3.1/sbin`。 将`start-dfs.sh`，`stop-dfs.sh`两个文件顶部添加以下参数

```bash
#!/usr/bin/env bash
HDFS_DATANODE_USER=root
HADOOP_SECURE_DN_USER=hdfs
HDFS_NAMENODE_USER=root
HDFS_SECONDARYNAMENODE_USER=root
```

同时，还有，`start-yarn.sh`，`stop-yarn.sh`顶部也需添加以下：

```bash
#!/usr/bin/env bash
YARN_RESOURCEMANAGER_USER=root
HADOOP_SECURE_DN_USER=yarn
YARN_NODEMANAGER_USER=root
```



##### 启动Hadoop

接下来我们启动`Hadoop`：

```bash
start-dfs.sh
```

最后输入命令 `jps` 验证,出现如下界面代表启动成功：

![](https://gitee.com/rogerskelamen/mdpic/raw/master/img/image-20200911181634437.png)

之后如果你是图形化界面，可以在你虚拟机的图形化界面中打开火狐浏览器输入：http://localhost:9870/   或者在`windows`机器上输入http://虚拟机ip地址:9870/  也可以访问`hadoop`的管理页面。

![](https://gitee.com/rogerskelamen/mdpic/raw/master/img/image-20200911181723238.png)

