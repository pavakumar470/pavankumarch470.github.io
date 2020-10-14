---
published: false
---
1.Download the Solr distributions archive file(tgz)
Once downloaded the move the tgx file to the /package/files/solr.tgz<br/>
2.Create the metainfo.xml file with the below content and make sure yoe edit the tgz file name according to the one which is placed in /package/files/solr.tgz:<br/>
```xml
<?xml version="1.0"?>
<!--
   Licensed to the Apache Software Foundation (ASF) under one or more
   contributor license agreements.  See the NOTICE file distributed with
   this work for additional information regarding copyright ownership.
   The ASF licenses this file to You under the Apache License, Version 2.0
   (the "License"); you may not use this file except in compliance with
   the License.  You may obtain a copy of the License at
       http://www.apache.org/licenses/LICENSE-2.0
   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.
-->

<metainfo>
  <schemaVersion>2.0</schemaVersion>
  <application>
    <name>Solr</name>
    <comment>Solr is an app that can run on YARN.</comment>
    <version>1.0.0</version>
    <exportedConfigs>None</exportedConfigs>

    <exportGroups>
      <exportGroup>
        <name>Servers</name>
        <exports>
          <export>
            <name>host_port</name>
            <value>http://${SOLR_HOST}:${site.global.listen_port}/</value>
          </export>
          <export>
            <name>zkhost</name>
            <value>${site.global.zk_host}</value>
          </export>
        </exports>
      </exportGroup>
    </exportGroups>

    <components>
      <component>
        <name>SOLR</name>
        <category>SLAVE</category>
        <compExports>Servers-host_port,Servers-zkhost</compExports>
        <commandScript>
          <script>scripts/solr_node.py</script>
          <scriptType>PYTHON</scriptType>
        </commandScript>
      </component>
    </components>

    <osSpecifics>
      <osSpecific>
        <osType>any</osType>
        <packages>
          <package>
            <type>tarball</type>
            <name>files/solr.tgz</name>
          </package>
        </packages>
      </osSpecific>
    </osSpecifics>

  </application>
</metainfo>
```

3.once both metainfo.xml and solr binaries are formed zip them usin the below:
```java
zip -r solr-on-yarn.zip metainfo.xml package/
```
5.create the slor and slider directories in hdfs and then create the "appConfig.json" file with the below properties:<br/>
```java
{
  "schema": "http://example.org/specification/v2.0.0",
  "metadata": {
  },
  "global": {
    "site.global.solr_hdfs_home": "hdfs://hostname:8020/solr"
    "application.def": ".slider/package/solr/solr-on-yarn.zip",
    "java_home": "",
    "site.global.app_root": "${AGENT_WORK_ROOT}/app/install/solr-5.2.0-SNAPSHOT",
    "site.global.zk_host": "localhost:2181",
    "site.global.solr_host": "${SOLR_HOST}",
    "site.global.listen_port": "${SOLR.ALLOCATED_PORT}",
    "site.global.xmx_val": "1g",
    "site.global.xms_val": "1g",
    "site.global.gc_tune": "-XX:NewRatio=3 -XX:SurvivorRatio=4 -XX:TargetSurvivorRatio=90 -XX:MaxTenuringThreshold=8 -XX:+UseConcMarkSweepGC -XX:+UseParNewGC -XX:ConcGCThreads=4 -XX:ParallelGCThreads=4 -XX:+CMSScavengeBeforeRemark -XX:PretenureSizeThreshold=64m -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=50 -XX:CMSMaxAbortablePrecleanTime=6000 -XX:+CMSParallelRemarkEnabled -XX:+ParallelRefProcEnabled -verbose:gc -XX:+PrintHeapAtGC -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:+PrintTenuringDistribution -XX:+PrintGCApplicationStoppedTime",
    "site.global.zk_timeout": "15000",
    "site.global.server_module": "--module=http",
    "site.global.stop_key": "solrrocks",
    "site.global.solr_opts": ""
  },
  "components": {
    "slider-appmaster": {
      "jvm.heapsize": "512M"
    },
    "SOLR": {
    }
  }
}
```
6.create the resource.json file with the below details and edit the memory and other details:<br/>
```java
{
  "schema": "http://example.org/specification/v2.0.0",
  "metadata": {},
  "global": {},
  "components": {
    "SOLR_SHARD1_REPLICA1": {
      "yarn.vcores": "1",
      "yarn.role.priority": "1",
      "yarn.component.instances": "1",
      "yarn.memory": "2560",
      "yarn.component.placement.policy": "2"
    },
    "slider-appmaster": {
      "yarn.vcores": "1",
      "yarn.component.instances": "1",
      "yarn.memory": "512"
    }
  }
}
```
7.create the solr home z-node in the zookeeper and then start the solr application using the slider with the below script:<br/>
```java
$SLIDER_HOME/bin/slider create  <NAME OF THE APPLICATION TO BE DEPLOYED>  --basepath hdfs://hostname:8020/slider --template /path/to/appConfig.json --resources /path/to/resources.json  -D slider.yarn.queue=default -D hadoop.registry.zk.quorum=hostname:2181 --manager localhost:8032
```
