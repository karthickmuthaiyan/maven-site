title: Releasing A Parent POM
author: Hervé Boutemy, Dennis Lundberg
date: 2013-12-07

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

## Releasing A Parent POM


 Releasing a Parent POM is much the same as any other Maven project. The following guide walks through most of the steps:



 - [ Maven Project Common Release procedure](./maven-project-release-procedure.html)


 Note that Parent POMs have particular conventions for managing and deploying the documentation.


### Rationale


 To be able to publish a documentation for the parent POM without affecting released `pom.xml` and `site.xml`, parent POM projects have a specific structure, with the addition of `site-pom.xml` and `src/site-docs` to provide `mvn -f site-pom.xml site` with useful documentation content:



```
|-- pom.xml
|-- site-pom.xml
`-- src
    |-- site
    |   `-- site.xml
    `-- site-docs
        |-- apt
        |   `-- index.apt
        `-- site.xml
```

 And the `index.apt` page not only contains instructions about the content of the parent POM, but it maintains a history of POM releases links and diffs.


 Each specific step is done to maintain `site-pom.xml` and `index.apt` in sync with the release being released.



### Stage the release


 Before staging the release with usual procedure, you need to update `site-pom.xml` and `index.apt` to take the future release into account:



 1 update `site-pom.xml` parent POM version to match the version being released,

 1 update `src/site-docs/index.apt.vm`: add a line in the history of `pom.xml` for the version being released, referring to the future git release tag and date.


 Once these modifications are done, you can follow [standard component documentation staging steps](../website/deploy-component-reference-documentation.html), taking care to use the `site-pom.xml` POM, with `mvn -f site-pom.xml ...` command, each time the parent POM's site is generated or published.


 Then the only difference is with commands to stage the site:



```
cd target/checkout
mvn -f site-pom.xml site
mvn -f site-pom.xml scm-publish:publish-scm
```


### Call the vote


 In the vote, instead of providing links to JIRA, the parent POMs should include a link to the Git changes since the last release:



```
...
Hi,

Changes since the last release:
for Maven Apache Parent POM:
https://github.com/apache/maven-apache-parent/compare/apache-<VERSION-OF-PREVIOUS-RELEASE>...apache-<VERSION-OF-CURRENT-RELEASE>

or for Maven Projects Parent POM:
https://github.com/apache/maven-parent/compare/maven-parent-<VERSION-OF-PREVIOUS-RELEASE>...maven-parent-<VERSION-OF-CURRENT-RELEASE>

Staging repo:
...
```


