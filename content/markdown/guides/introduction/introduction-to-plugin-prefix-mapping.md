title: Introduction to Plugin Prefix Resolution
author: John Casey
date: 2009-08-01

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
## Introduction to Plugin Prefix Resolution


 When you execute Maven using a standard lifecycle phase, resolving the plugins that participate in that lifecycle is a relatively simple process. However, when you directly invoke a mojo from the command line, as in the case of **clean**, Maven must have some way of reliably resolving the **clean** plugin prefix to the **maven-clean-plugin**. This provides brevity for command-line invocations, while preserving the descriptiveness of the plugin's real artifactId.


 To complicate matters even more, not all plugins should be forced to have the same groupId in the repository. Since groupIds are presumed to be controlled by one project, and multiple projects may release plugins for Maven, it follows that plugin-prefix mappings must also accommodate multiple plugin groupIds.


 To address these concerns, Maven provides a new piece of repository-level metadata (not associated with any single artifact) for plugin groups, along with a plugin mapping manager to organize multiple plugin groups and provide search functionality.


### Specifying a Plugin's Prefix


 In order to give users a convenient prefix with which to reference your plugin a prefix must be associated with your plugin when it is built. By default, Maven will make a guess at the plugin-prefix to be used, by removing any instances of "maven" or "plugin" surrounded by hyphens in the plugin's artifact ID. The conventional artifact ID formats to use are:



 - `maven-${prefix}-plugin` - for official plugins maintained by the Apache Maven team itself (you **must not** use this naming pattern for your plugin, see [this note for more informations](../plugin/guide-java-plugin-development.html#Plugin_Naming_Convention_and_Apache_Maven_Trademark))

 - `${prefix}-maven-plugin` - for plugins from other sources


 If your plugin's artifactId fits this pattern, Maven will automatically map your plugin to the correct prefix in the metadata stored within your plugin's groupId path on the repository. However, if you want to customize the prefix used to reference your plugin, you can specify the prefix directly through a configuration parameter on the `maven-plugin-plugin` in your plugin's POM:



```
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <artifactId>maven-plugin-plugin</artifactId>
        <version>2.3</version>
        <configuration>
          ...
          <goalPrefix>somePrefix</goalPrefix>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

 The above configuration will allow users to refer to your plugin by the prefix **somePrefix**, as in the following example:



```
mvn somePrefix:goal
```


### Mapping Prefixes to Plugins


 For each groupId configured for searching, Maven will:



 1 Download `maven-metadata.xml` from each remote repository into the local repository, and name it `maven-metadata-${repoId}.xml` within the path of `${groupId}`.

 1 Load these metadata files, along with `maven-metadata-local.xml` (if it exists), within the path of `${groupId}`. Merge them.

 1 Lookup the plugin prefix in the merged metadata. If it's mapped, it should refer to a concrete groupId-artifactId pair. Otherwise, go on to #1 for the next groupId in the user's plugin-groups.


 These metadata files consist of the **groupId** it represents (for clarity when the file is opened outside the context of the directory), and a group of **plugin** elements. Each **plugin** in this list contains a **prefix** element denoting the plugin's command-line prefix, and an **artifactId** element, which provides the other side of the prefix mapping and provides enough information to lookup and use the plugin. When a plugin is installed or deployed, the appropriate metadata file is located - and if the prefix mapping is missing - modified to include the plugin-prefix mapping.



### Configuring Maven to Search for Plugins


 By default, Maven will search the groupId **org.apache.maven.plugins** for prefix-to-artifactId mappings for the plugins it needs to perform a given build. However, as previously mentioned, the user may have a need for third-party plugins. Since the Maven project is assumed to have control over the default plugin groupId, this means configuring Maven to search other groupId locations for plugin-prefix mappings.


 As it turns out, this is simple. In the Maven settings file (per-user: `${user.home}/.m2/settings.xml`; global: `${maven.home}/conf/settings.xml`), you can provide a custom **pluginGroups** section, listing the plugin groupIds you want to search (each groupId goes in its own **pluginGroup** sub-element). For example, if my project uses a Modello model file, I might have the following in my settings:



```
<pluginGroups>
  <pluginGroup>org.codehaus.modello</pluginGroup>
</pluginGroups>
```

 This allows me to execute the following, which will generate Java classes from the model:



```
mvn -Dversion=4.0.0 -Dmodel=maven.mdo modello:java
```

 Maven will always search the following groupId's **after** searching any plugin groups specified in the user's settings:



 - org.apache.maven.plugins

 - org.codehaus.mojo


 **NOTE:** When specifying plugin groups to be used in searching for a prefix mapping, order is critical! By specifying a pluginGroup of `com.myco.plugins` with a prefix of `clean`, I can override the usage of the `maven-clean-plugin` when `clean:clean` is invoked.


 **NOTE2:** For more information on `settings.xml`, see \[[1](a1)\].



### Resources


 1 [Guide to Configuring Maven](../mini/guide-configuring-maven.html)



