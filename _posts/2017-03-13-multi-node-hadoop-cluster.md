---
layout: post
title:  "Easily Setup Multi-Node Hadoop Cluster in YARN Mode on CentOS/Ubuntu"
---
First, let’s understand what is Hadoop YARN and how it’s different from Hadoop 1.x. YARN is one the key features in next gen Hadoop 2 version. YARN is now characterized as a large-scale, distributed operating system for big data applications.
Image Courtesy: Google

YARN(Yet Another Resource Negotiator) is a cluster management technology where resource management and processing is done using different processing models. When compared to Hadoop 1.X where scaling was limited to 4000 nodes per cluster, in 2.x we can scale up to 10,000 nodes per cluster. Hadoop 2.x allows to work in MapReduce as well as other distributed computing models like Spark, MP and Hbase coprocessor. It works on the concept of containers. Here we can have multiple namenodes and hence there is no single point of failure.
Hadoop 2.x architeture

The architecture consists of 2 major components i.e. HDFS and YARN. As the name suggests HDFS is used for Storage and YARN is used for Processing. In the above architecture, the NameNode and ResourceManager acts as Master and DataNode and NodeManager acts as slave.

Let’s begin with creating a multinode hadoop yarn cluster. Here I will show, how you can create your own 3 Nodes(1 Master and 2 Slaves nodes cluster)
1. Install CentOS:

I will be using 3 CentOS 6.5 minimal machines on my virtual box and lets name them as nn1(consisting of namenode and resourcemanager), dn1(datanode1 and nodemanager1), dn2(datanode2 and nodemanager2). You can also use ubuntu here instead of CentOS

You can make your network adapter as Bridge or Host-Only depending upon your requirement. Here are my 3 machines

    nn1–192.168.1.200 HDFS — Namenode and YARN — Resourcemanager

    dn1–192.168.1.201 HDFS — Datanode and YARN — Nodemanager

    dn2–192.168.1.202 HDFS — Datanode and YARN — Nodemanager

Virtual Box
2. Edit /etc/hosts :

Now lets ssh into nn1, dn1, dn2 and change the /etc/hosts file, so that we can use hostname instead of IP everytime we wish to use or ping any of these machines
vi /etc/hosts
3. Download and Install Java:

    [root@nn1 /]yum install wget

    [root@nn1 /]wget https://mirror.its.sfu.ca/mirror/CentOS-Third-Party/NSG/common/x86_64/jdk-7u45-linux-x64.rpm

    [root@nn1 /]# rpm -ivh /opt/jdk-7u45-linux-x64.rpm

java -version
4. Install Hadoop 2.7.3

    [root@nn1 ~]# wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz

    [root@nn1 ~]# tar -xzvf /root/hadoop-2.7.3.tar.gz -C /home/hadoop/

5. Set Environment variables in ~/.bash_profile(centos) or .bashrc(ubuntu)

    export JAVA_HOME=/usr/java/jdk1.7.0_45

    PATH=$PATH:$JAVA_HOME/bin:/home/hadoop/hadoop-2.7.3/bin:$PATH

    export PATH

    # Set Hadoop-related environment variables

    export HADOOP_HOME=/home/hadoop/hadoop-2.7.3

    export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop

    export HADOOP_MAPRED_HOME=$HADOOP_HOME

    export HADOOP_COMMON_HOME=$HADOOP_HOME

    export HADOOP_HDFS_HOME=$HADOOP_HOME

    export YARN_HOME=$HADOOP_HOME

    # Add Hadoop bin/ directory to PATH

    export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin

vi ~/.bash_profile

source it to reflect changes
6. Modify core-site.xml
xmls to be modified

    <configuration>

    <property>

    <name>fs.default.name</name>

    <value>hdfs://192.168.1.200:9000</value>

    </property>

    </configuration>

core-site.xml
7. Modify hdfs-site.xml

    <configuration>

    <property>

    <name>dfs.namenode.name.dir</name>

    <value>file:/home/hadoop/hadoop-2.7.3/hadoop_store/hdfs/namenode2</value>

    </property>

    <property>

    <name>dfs.datanode.data.dir</name>

    <value>file:/home/hadoop/hadoop-2.7.3/hadoop_store/hdfs/datanode2

    </value>

    </property>

    </configuration>

hdfs-site.xml
8. Modify mapred-site.xml

cp mapred-site-template.xml mapred-site.xml

    <configuration>

    <property>

    <name>mapreduce.framework.name</name>

    <value>yarn</value>

    </property>

    </configuration>

mapred-site.xml
9. Modify yarn-site.xml

    <configuration>

    <! — Site specific YARN configuration properties — >

    <property>

    <name>yarn.nodemanager.aux-services</name>

    <value>mapreduce_shuffle</value>

    </property>

    <property>

    <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>

    <value>org.apache.hadoop.mapred.ShuffleHandler</value>

    </property>

    <property>

    <name>yarn.resourcemanager.resource-tracker.address</name>

    <value>192.168.1.200:8025</value>

    </property>

    <property>

    <name>yarn.resourcemanager.scheduler.address</name>

    <value>192.168.1.200:8030</value>

    </property>

    <property>

    <name>yarn.resourcemanager.address</name>

    <value>192.168.1.200:8050</value>

    </property>

    </configuration>

yarn-site.xml
10. Create Namenode directory in nn1

    mkdir -p /home/hadoop/hadoop-2.7.3/hadoop_store/hdfs/namenode2

11. Modify slaves files and add both datanodes ip’s
vi slaves
12. Copy all modified files to both the datanodes dn1 and dn2
Copy all files to dn1
Copy all files to dn2
13. Make datanode directory on both dn1 and dn2 and give permissions
14. Disable Firewall

    service iptables stop

    service ip6tables stop

15. Format Namenode on nn1
16. Start Namenode, Datanode(start-dfs.sh) and Resourcemanager, Nodemanager(start-yarn.sh)
17. jps on nn1
18. jps on dn1 and dn2
19. Check if we get 2 live data-nodes

    hdfs dfsadmin -report

20. Check ResourceManager on browser

If you have any issues installing vm, network related issues or any commands, please comment below.
