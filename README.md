# Instalation Hadoop 

## Download Java JDK 1.8
http://planetone.online/downloads/java/jdk/jdk-8u221-linux-x64.tar.gz

## Create a hadoop user and configure passwordless ssh
sudo addgroup hadoop
sudo adduser --ingroup hadoop hduser
sudo visudo
hduser ALL=(ALL:ALL) ALL
su - hduser 


## Install Java 8 Ubuntu 18.04 
sudo wget <jdk-link>
sudo tar -xzvf jdk-8u221-linux-x64.tar.gz
sudo mv jdk1.8.0_221/ java 
sudo vim ~/.bashrc 

export JAVA_HOME=/usr/local/java
export PATH=:$PATH:/usr/local/java/bin

sudo update-alternatives --install "/usr/bin/java" "java" "/usr/local/java/bin/java" 1
sudo update-alternatives --install "/usr/bin/javac" "javac" "/usr/local/java/bin/javac" 1
sudo update-alternatives --install "/usr/bin/javaws" "javaws" "/usr/local/java/bin/javaws" 1
 
sudo update-alternatives --set java /usr/local/java/bin/java
sudo update-alternatives --set javac /usr/local/java/bin/javac
sudo update-alternatives --set javaws /usr/local/java/bin/javaws

## Install SSH 
sudo apt-get update
sudo apt-get install openssh-server openssh-client
sudo ufw allow 22

## Download and Install Hadoop
 
cd /usr/local 
sudo wget http://www-eu.apache.org/dist/hadoop/common/hadoop-3.1.2/hadoop-3.1.2.tar.gz
sudo tar xzvf hadoop-3.1.2.tar.gz
sudo mv hadoop-3.1.2 hadoop
 
sudo chown -R hduser:hadoop /usr/local/hadoop
sudo chmod -R 777 /usr/local/hadoop
 
sudo vim /etc/sysctl.conf
 
net.ipv6.conf.all.disable_ipv6=1
net.ipv6.conf.default.disable_ipv6=1
net.ipv6.conf.lo.disable_ipv6=1
 
sudo vim ~/.bashrc 
 
export HADOOP_HOME=/usr/local/hadoop
export HADOOP_CONF_DIR=/usr/local/hadoop/etc/hadoop
export HADOOP_MAPRED_HOME=/usr/local/hadoop
export HADOOP_COMMON_HOME=/usr/local/hadoop
export HADOOP_HDFS_HOME=/usr/local/hadoop
export YARN_HOME=/usr/local/hadoop
export PATH=$PATH:/usr/local/hadoop/bin
export PATH=$PATH:/usr/local/hadoop/sbin
 
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib"

## Setup hadoop configuration 


sudo vim yarn-site.xml
 
<property>
<name>yarn.nodemanager.aux-services</name>
<value>mapreduce_shuffle</value>
</property>
<property>
<name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
<value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>
 
sudo vim hdfs-site.xml
 
<property>
<name>dfs.replication</name>
<value>1</value>
</property>
<property>
<name>dfs.namenode.name.dir</name>
<value>file:/usr/local/hadoop/yarn_data/hdfs/namenode</value>
</property>
<property>
<name>dfs.datanode.data.dir</name>
<value>file:/usr/local/hadoop/yarn_data/hdfs/datanode</value>
</property>
 
sudo vim core-site.xml
 
<property>
<name>hadoop.tmp.dir</name>
<value>/app/hadoop/tmp</value>
</property>
<property>
<name>fs.default.name</name>
<value>hdfs://localhost:9000</value>
</property>
 
 
sudo vim mapred-site.xml
 
<property>
<name>mapred.framework.name</name>
<value>yarn</value>
</property>
<property>
<name>mapreduce.jobhistory.address</name>
<value>localhost:10020</value>
</property>
 
sudo mkdir -p /app/hadoop/tmp
sudo chown -R hduser:hadoop /app/hadoop/tmp
sudo chmod -R 777 /app/hadoop/tmp
 
sudo mkdir -p /usr/local/hadoop/yarn_data/hdfs/namenode

sudo mkdir -p /usr/local/hadoop/yarn_data/hdfs/datanode

sudo chmod -R 777 /usr/local/hadoop/yarn_data/hdfs/namenode

sudo chmod -R 777 /usr/local/hadoop/yarn_data/hdfs/datanode

sudo chown -R hduser:hadoop /usr/local/hadoop/yarn_data/hdfs/namenode

sudo chown -R hduser:hadoop /usr/local/hadoop/yarn_data/hdfs/datanode

 
source ~/.bashrc
 
hdfs namenode -format
 
start-dfs.sh 
start-yarn.sh
 
jps
 
namenode 9870
http://localhost:9870/
 
cluster info
http://localhost:8042/
 
hadoop nodes 9864
http://localhost:9864/
 
 
bin/hdfs dfs -mkdir /user
bin/hdfs dfs -mkdir /user/hadoop
