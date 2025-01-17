title: Building For Different Environments
author: Trygve Laugstøl
date: 2006-01-01

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

## Building For Different Environments


 Building the same artifact for different environments has always been an annoyance. You have multiple environments, for instance test and production servers or, maybe a set of servers that run the same application with different configurations. In this guide I'll explain how you can use profiles to build and package artifacts configured for specific environments. See [Introduction to Build Profiles](../introduction/introduction-to-profiles.html) for a more in-depth explanation of the profile concept.


 Note:



 - This guide assume that you have basic Maven knowledge.

 - It will show a way to configure Maven to solve simple configuration set-ups only. By simple configuration set-up I mean cases where you only have a single file or a small set of files that vary for each environment. There are other and better ways to handle two and many-dimensional configuration issues.

   This example assume the use of the [Standard Directory Layout](../introduction/introduction-to-the-standard-directory-layout.html).



```
pom.xml
src/
  main/
    java/
    resources/
  test/
    java/
```



 Under `src/main/resources` there are three files:



 - `environment.properties` - This is the default configuration and will be packaged in the artifact by default.

 - `environment.test.properties` - This is the variant for the test environment.

 - `environment.prod.properties` - This is basically the same as the test variant and will be used in the production environment.

   In the project descriptor, you need to configure the different profiles. Only the test profile is showed here.



```
 <profiles>
   <profile>
     <id>test</id>
     <build>
       <plugins>
         <plugin>
           <artifactId>maven-antrun-plugin</artifactId>
           <executions>
             <execution>
               <phase>test</phase>
               <goals>
                 <goal>run</goal>
               </goals>
               <configuration>
                 <tasks>
                   <delete file="${project.build.outputDirectory}/environment.properties"/>
                   <copy file="src/main/resources/environment.test.properties"
                         tofile="${project.build.outputDirectory}/environment.properties"/>
                 </tasks>
               </configuration>
             </execution>
           </executions>
         </plugin>
         <plugin>
           <artifactId>maven-surefire-plugin</artifactId>
           <configuration>
             <skip>true</skip>
           </configuration>
         </plugin>
         <plugin>
           <artifactId>maven-jar-plugin</artifactId>
           <executions>
             <execution>
               <phase>package</phase>
               <goals>
                 <goal>jar</goal>
               </goals>
               <configuration>
                 <classifier>test</classifier>
               </configuration>
             </execution>
           </executions>
         </plugin>
       </plugins>
     </build>
   </profile>

   .. Other profiles go here ..

 </profiles>
```


   Three things are configured in this snippet:



  1 It configures the antrun plugin to execute the run goal in the test phase where it will copy the `environment.test.properties` file to `environment.properties`.

  1 It will configure the test plugin to skip all tests when building the test and production artifacts. This is useful as you probably don't want to run tests against the production system

  1 It configures the JAR plugin to create an "attached" JAR with the "test" classifier.

    To activate this profile execute `mvn -Ptest install` and Maven will execute the steps in the profile in addition to the normal steps. From this build you will get two artifacts, "foo-1.0.jar" and "foo-1.0-test.jar". These two jars will identical. 







## Caveats



 - Currently Maven doesn't allow a project build to only produce attached artifacts. (i.e. it has to produce a "main" artifact as well) This results in two equal JARs being packaged and installed. The JAR plugin probably should also get improved support for this use case to that two different output directories will be used as the basis for building the JAR.

 - The usage of the delete task might seem a bit odd but is required to make sure that the copy task actually will copy the file. The copy task will look at the timestamps of the source and destination files, only when copying the files it won't know that the actually source file might be different than the last time it was executed.

 - After the build the test configuration will be in target/classes and won't be overridden because the resources plugin uses the same timestamp checking, so you should always do a clean after executing Maven with a profile.

 - For the reasons given above it's imperative that you only build an artifact for a single environment in a single execution at a time and that you execute "mvn clean" whenever you change the profile switches. If not, you might get artifacts with a mixed set of configuration files.



## Resources



 1 [Introduction to Build Profiles](../introduction/introduction-to-profiles.html)

 1 [Standard Directory Layout](../introduction/introduction-to-the-standard-directory-layout.html)


