---
published: true
---
Here in this blog we will talk about the installation of the HDP cluster using the Blue prints and local repos<br/>
<br/>

Follow the below steps for the same:
1.create the local repo for the cluster installers on the server machine using the httpd/simplehttp service<br/>
2.create the ambari.repo file under /etc/yum.repos.d and edit the base url as below:<br/>
```java 
http://<hostanme>:<port>/ambari
```
3.create the hdputils.repo and edit the base urls for the HDP and HDP-Utils:<br/>
```java 
http://<hostname>:<port>/HDP
http://<hostname>:<port>:HDP-UTILS
```
4.install the ambari-server with the below:<br/>
```java
yum install ambari-server
```
5.then setup the ambari server using the below(the below is the silint installation where the java home can be overwritten):<br/>
```java
sudo ambari-server setup -j /home/Informatica/10.4.0/java --silent
```
6.install the ambari agent on all the cluster machines using:<br/>
``java
yum install ambari-agent
```
7.edit the "/etc/ambari-agent/conf/ambari-agent.ini" file and add the ambari-server hostname on all the ambari-agent machines and then start the ambari server and agents.<br/>
8.create the json file which conatins the complete blueprint. below is the example for the blueprint with single node:<br/>
```java
{
  "host_groups" : [
    {
      "name" : "blueprint1",
      "components" : [
                {"name":"ZOOKEEPER_SERVER"},{"name":"ZOOKEEPER_CLIENT"},{"name":"NAMENODE"},{"name":"HDFS_CLIENT"},{"name":"DATANODE"},{"name":"SECONDARY_NAMENODE"},{"name":"MAPREDUCE2_CLIENT"},{"name":"HISTORYSERVER"},{"name":"METRICS_MONITOR"},{"name":"METRICS_COLLECTOR"},{"name":"APP_TIMELINE_SERVER"},{"name":"RESOURCEMANAGER"},{"name":"NODEMANAGER"},{"name":"YARN_CLIENT"},{"name":"ZOOKEEPER_CLIENT"},{"name":"HDFS_CLIENT"},{"name":"DATANODE"},{"name":"MAPREDUCE2_CLIENT"},{"name":"METRICS_MONITOR"},{"name":"NODEMANAGER"},{"name":"YARN_CLIENT"}
      ],
      "cardinality" : "1"
    }
  ],
  "Blueprints" : {
    "blueprint_name" : "blueprint",
    "stack_name" : "HDP",
    "stack_version" : "3.1"
  }
}
```
9.register the blueprint using the api calls below is the curl command for registering the blueprint:<br/>
```java
curl -H "X-Requested-By: ambari" -X POST -u admin:admin http://<AMBARI SERVER HOST>:8080/api/v1/blueprints/<BLUEPRINT NAME> -d @<blueprint filename>.json
```
8.if you using the local repositores for the installing the components regiester the HDP repo and HDP-utils repo as well. create the HDP.json file with the below as content:<br/>
```java
{
"Repositories":{
"repo_name":"HDP-3.1.0.0",
"base_url":"http://<hostname>:<port>/HDP",
"verify_base_url":true
}
}
```
9.create the hdp-utils.json file with the below as a content:
```java
{
"Repositories":{
"repo_name":"HDP-3.1.0.0",
"base_url":"http://ink02860073/HDP-UTILS",
"verify_base_url":true
}
}
```
10.register the HDP repo below is the crl command for registering the same:<br/>
```java
curl -H "X-Requested-By: ambari" -X PUT -u admin:admin http://<hostname>:8080/api/v1/stacks/HDP/versions/3.1/operating_systems/redhat7/repositories/HDP-3.1 -d @HDP.json
```
11.register the HDP Utils repo using the below curl command:<br/>
```java
curl -H "X-Requested-By: ambari" -X PUT -u admin:admin http://<hostname>:8080/api/v1/stacks/HDP/versions/3.1/operating_systems/redhat7/repositories/HDP-UTILS-1.1.0.22 -d @hdp-utils.json
``
12.create the hostname.json file which conatins the blueprints mapping for all the hosts below is the ebample:<br/>
```java
{
"Clusters": {
        "cluster_name": "pavan"
    },
 "blueprint" : "pavan_kumar",
 "default_password" : "admin",
 "host_groups" :[
{
 "name" : "blueprint1",
 "hosts" : [
 {
 "fqdn" : "ink02860073.informatica.com"
 }
 ]
 }
 ]
}
```
13.trigger the instaltion of the components and the start of the services using the below curl commands:<br/>
```java
curl -H "X-Requested-By: ambari" -X POST -u admin:admin http://<ambari-host>:8080/api/v1/clusters/<new-cluster-name>; -d @hostmapping.json
```
