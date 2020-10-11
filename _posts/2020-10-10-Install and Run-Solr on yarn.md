---
published: false
---
1.Download the Solr distributions archive file(tgz)
Once downloaded the move the tgx file to the /package/files/solr.tgz<br/>
2.Create the metainfo.xml file with the below content and make sure yoe edit the tgz file name according to the one which is placed in /package/files/solr.tgz:<br/>
```html
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
