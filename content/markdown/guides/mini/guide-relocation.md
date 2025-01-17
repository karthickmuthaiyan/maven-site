title: Guide to relocation
author: Dennis Lundberg
date: 2006-07-08

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
## Guide to relocation


 Sometimes it is necessary to relocate artifacts in the repository. One example of that is when a project moves from one groupId to a different groupId.


 Making changes to the repository can have far reaching consequences. So it is best to get it right the first time, hence this guide.


 **2020 rework in progress**, see [discussion on dev mailing list](https://lists.apache.org/thread.html/r5e940260cfe5234f540c20fdb7bb7dacbb63b911a4b902c75f4f0cd2%40%3Cdev.maven.apache.org%3E), still need analysis of issues, definition of improvements, and of course implementation...


### How to relocate a Maven 2 artifact to a different groupId


 The goal of the example below is for the `foo` project to relocate its groupId from `bar` to `org.bar`.


#### Working on past versions



 1 Copy all `foo`-related files from `/bar/foo/` in your Maven 2 repository to a temporary location.

 1 Change the groupId to `org.bar` in all `foo`-related POM files in the temporary location.

 1 Copy all files from the temporary location to `/org/bar/foo/` in your Maven 2 repository.

 1 Create a minimal Maven 2 POM file for every old release of `foo` in your Maven 2 repository. The POM files only need to include `groupId`, `artifactId`, `version` and the relocation section.

   **Note:** Before you replace your old POM files in `/bar/foo/` with these minimal POM files, make sure you have made backups!



   The minimal POM file might look like this for version 1.0 of `foo`:



```
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>bar</groupId>
  <artifactId>foo</artifactId>
  <version>1.0</version>
  <distributionManagement>
    <relocation>
      <groupId>org.bar</groupId>
    </relocation>
  </distributionManagement>
</project>
```


   In this case we are relocating because the groupId has changed. We only need to add the element that has changed to the `relocation` element. For information on which elements are allowed in the `relocation` element, see [the POM reference](/ref/current/maven-model/maven.html#class_relocation).



 1 If your project uses MD5 or SHA1 checksums you must now create new checksums for the pom files in `/bar/foo/` in your Maven 2 repository. If the POM file needs to be signed, do that as well.

 1 If your project syncs with Central, you should now initiate that sync. This might happen automatically depending on your projects sync policy.


 Your `foo`-artifacts are now available to Maven users with both the old and the new groupId. Projects using the old groupId will automatically be redirected to the new groupId and a warning telling the user to update their dependencies will be issued.



#### Releasing the next version


 When the next release of `foo` is made, you should publish two Maven 2 POM files: first you should publish `foo`'s POM with the new groupId `org.bar`.


 Because data in the repository is not supposed to change, Maven doesn't download POM files that it has already downloaded. Therefore you will also need to publish a relocation POM file with the old groupId `bar` for the new version: this should be a minimal relocation POM (as described in step 4 above), but for the new version of `foo`.


 For the release after that, you only need to publish a Maven POM with a groupId of `org.bar`, since users of the previous version have been informed of the changed groupId.




### Examples


#### Apache Ant



 1 has published its releases until 1.6.5 to Maven 1-compliant `ant:ant` coordinates (see [repository content](https://repo.maven.apache.org/maven2/ant/ant/)),

 1 starting with 1.7.0, moved to reverse-DNS compliant Maven 2+ `org.apache.ant:ant` coordinates, (see [repository content](https://repo.maven.apache.org/maven2/org/apache/ant/ant/)),

 1 published one `ant:ant:1.7.0` relocation POM in old groupId to advertise about the move (see [repository content](https://repo.maven.apache.org/maven2/ant/ant/1.7.0/)).


 Notice that past `ant:ant` versions POMs (until 1.6.5) have not been modified to advertise about the move: Central POM content is not expected to be changed once published (because initial content has been downloaded many times and is not expected to be re-loaded later).



#### Apache POI



 1 has published its releases until 3.0-alpha-3 to Maven 1-compliant `poi:poi` coordinates (see [repository content](https://repo.maven.apache.org/maven2/poi/poi/)),

 1 starting with 3.0-FINAL, moved to reverse-DNS compliant Maven 2+ `org.apache.poi:poi` coordinates, (see [repository content](https://repo.maven.apache.org/maven2/org/apache/poi/poi/)),

 1 published `poi:poi:3.0-FINAL` relocation POM **with jars** in old groupId to advertise about the move (see [repository content](https://repo.maven.apache.org/maven2/poi/poi/3.0-FINAL/)).

 1 published `poi:poi` relocation POMs for 3.0.1-FINAL, 3.0.2-beta1/beta2/FINAL and 3.1-beta1/beta2/FINAL in old groupId to advertise about the move (see [repository content](https://repo.maven.apache.org/maven2/poi/poi/)).




