title: Guide to generating Sources
author: Jason van Zyl, Karl Heinz Marbaise
date: 2005-10-12, 2016-06-11

<!--
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.
-->

## Guide to generating sources


 Let's run though a short example to try and help. To generate sources you must first have a plugin that participates in the `generate-sources` phase like the [ANTLR4 Maven Plugin](http://www.antlr.org/api/maven-plugin/latest/).


 So this is all fine and dandy, we have a plugin that wants to generate some sources from a Antlr4 grammar but how do we use it. You need to specify that you want to use it in your POM:



```

<project>
  ...
  <build>
    <plugins>
      <plugin>
        <groupId>org.antlr</groupId>
        <artifactId>antlr4-maven-plugin</artifactId>
        <version>4.5.3</version>
        <executions>
          <execution>
            <id>antlr</id>
            <goals>
              <goal>antlr4</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
  ...
</project>

```

 If you then type `mvn compile` Maven will walk through the [lifecycle](../introduction/introduction-to-the-lifecycle.html) and will eventually hit the `generate-sources` phase and see you have a plugin configured that wants to participate in that phase and the ANTLR4 Maven Plugin is executed with your given configuration. Furthermore during the compile you can observe that all the generated code (from your grammar files) will automatically being compiled without supplemental configuration.


