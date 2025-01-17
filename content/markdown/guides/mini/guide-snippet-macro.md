title: Guide to the Snippet Macro
author: Wendy Smoak
date: 2014-08-20

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
<!--  @todo dennisl: We should drop this document and refer to the macros page in the Doxia site -->
## Guide to the Snippet Macro


 When generating your project website with Maven, you have the option of dynamically including _snippet_s of source code in your pages.


 A _snippet_ is a section of a source code file that is surrounded by specially formatted comments.


 This functionality is inspired by the [Confluence](http://www.atlassian.com/software/confluence/) snippet macro, and is provided by the Maven Doxia project by way of the Maven Site Plugin.


 To include snippets of source code in your documentation, first add comments in the source document surrounding the lines you want to include, and then refer to the snippet by its id in the documentation file. Each snippet must be assigned an id, and the id must be unique within the source document. The id parameter is not required if you want to include the entire file.


 Following are examples of snippets in various source documents, as well as the corresponding macros in the APT documentation format.


 See the Doxia [Macros Guide](/doxia/macros/index.html#Snippet_Macro) for more information and examples.


### Snippets in Sources


#### Java



```
    // START SNIPPET: snip-id
    public static void main( String[] args )
    {
        System.out.println( "Hello World!" );
    }
    // END SNIPPET: snip-id
```


#### XML



```
  <!-- START SNIPPET: snip-id -->
  <navigation-rule>
    <from-view-id>/logon.jsp</from-view-id>
    <navigation-case>
      <from-outcome>success</from-outcome>
      <to-view-id>/mainMenu.jsp</to-view-id>
    </navigation-case>
  </navigation-rule>
  <!-- END SNIPPET: snip-id -->
```


#### JSP



```
  <%-- START SNIPPET: snip-id --%>
  <ul>
      <li><a href="newPerson!input.action">Create</a> a new person</li>
      <li><a href="listPeople.action">List</a> all people</li>
  </ul>
  <%-- END SNIPPET: snip-id --%>
```



### Snippets in Documentation


#### APT



```
 %{snippet|id=snip-id|url=http://svn.example.com/path/to/Sample.java}

 %{snippet|id=snip-id|url=file:///path/to/Sample.java}
```

 As of doxia-core version 1.0-alpha-9, a 'file' parameter is also available. If a full path is not specified, the location is assumed to be relative to `${basedir}`.



```
 ~~ Since doxia-core 1.0-alpha-9
 %{snippet|id=abc|file=src/main/webapp/index.jsp}
```


 - Macros in apt **must not** be indented.

 - Exactly one of `url` or `file` **must** be specified.




