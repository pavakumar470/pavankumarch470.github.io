---
published: true
---
We can access the hadfs file system from the non-hadoop machine which will make the HDFS vulnerable due to this we should have the auth of the cluster(kerberos).<br/>

we can follow the below procedure for accessing the HDFS:<br/>
1.copy the client libraries and binaries from the hadoop cluster machines to other linux machine.<br/>
2.If it is HDP cluster copy the following libraries from your Hadoop nodes to the linux Server. <br/>
```java
/usr/hdp/<version>/hadoop/
/usr/hdp/<version>/hadoop-hdfs/
/usr/hdp/<version>/hadoop-yarn/
/usr/hdp/<version>/hadoop-mapreduce/
```
3.Then copy the configuration files for the cluster and unzip to the directory. use the below api for downloading the config files:<br>
```java
http://<hdfs host name>:50070/conf
```
4.set the below environment variables for all the users(including the hdfs users and hadoop users):
```java
export HADOOP_CONF_DIR=/tmp/newconf
export PATH=$PATH:/usr/hdp/<version>/hadoop/bin
export HADOOP_HOME=/usr/hdp/<version>/hadoop/
```
5.add the "hdfs" user in server machine and switch to the hdfs user and access hdfs files using the below command:<br/>
```java
hdfs dfs -ls /
```
6.But if the cluster is kerberos enabled then while accessing the hdfs files complains as follow:
```java
WARN ipc.Client: Exception encountered while connecting to the server : org.apache.hadoop.security.AccessControlException: Client cannot authenticate via:[TOKEN, KERBEROS]
```
7.if the cluster is kerberos enabled copy the "krb5.conf" file to /etc and copy the keytab file for the users and run the kinit on keytab file for accessing the hdfs files.
