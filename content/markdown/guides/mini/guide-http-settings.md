title: Guide to Advanced HTTP Transport Configuration
author: John Casey
date: 2011-12-12

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

## Advanced Configuration of the Maven Resolver Transport



 - [Advanced Configuration of the Maven Resolver Transport](Advanced_Configuration_of_the_Maven_Resolver_Transport)

  - [Advanced configuration to Transports](Advanced_configuration_to_Transports)

   - [HTTP Headers](HTTP_Headers)

   - [Connection Timeouts](Connection_Timeouts)



  - [Advanced Configuration of the HttpClient HTTP Wagon](Advanced_Configuration_of_the_HttpClient_HTTP_Wagon)

   - [Introduction](Introduction)

   - [The Basics](The_Basics)

   - [Configuring GET, HEAD, PUT, or All of the Above](Configuring_GET.2C_HEAD.2C_PUT.2C_or_All_of_the_Above)

   - [Taking Control of Your HTTP Headers](Taking_Control_of_Your_HTTP_Headers)

   - [Fine-Tuning HttpClient Parameters](Fine-Tuning_HttpClient_Parameters)

    - [Non-String Parameter Values](Non-String_Parameter_Values)

    - [Example: Using Preemptive Authentication](Example:_Using_Preemptive_Authentication)

    - [Example: Lifting auth scope restriction for external authentication systems](Example:_Lifting_auth_scope_restriction_for_external_authentication_systems)

    - [Ignoring Cookies](Ignoring_Cookies)



   - [Support for General-Wagon Configuration Standards](Support_for_General-Wagon_Configuration_Standards)

    - [HTTP Headers](HTTP_Headers_1)

    - [Connection Timeouts](Connection_Timeouts_1)

    - [Read time out](Read_time_out)



   - [Resources](Resources)






 You can use the default transport for a given protocol, or you can select the transport you want by using the configuration. For more information about existing Resolver transports see the [ Resolver](https://maven.apache.org/resolver/) site. The default transport in Maven 3.x is Transport-Wagon, the Wagon layer having been introduced in Maven 2.x. Since then, more modern transports were introduced as well, even supporting overlapping protocols. The default transport in Maven 4.x changed to the more modern ["native" HTTP](https://maven.apache.org/resolver/maven-resolver-transport-http/index.html) transport.


 Ultimate reference for resolver transport configuration can be found on [ this page](https://maven.apache.org/resolver/configuration.html). While one can easily define simple typed values on command line using `-D...` switch, some more complex values, like HTTP headers, cannot.


### Advanced configuration to Transports


 Using your `settings.xml` you can customize the transport configurations in several ways.


#### HTTP Headers


 In all HTTP transports, you can add your custom HTTP headers like this:



```
<settings>
  <servers>
    <server>
      <id>my-server</id>
      <configuration>
        <httpHeaders>
          <property>
            <name>Foo</name>
            <value>Bar</value>
          </property>
        </httpHeaders>
      </configuration>
    </server>
  </servers>
</settings>
```

 It is important to understand that the above approach does not allow you to turn off all of the default HTTP headers; nor does it allow you to specify headers on a per-method basis. However, this configuration remains available in all transports that support headers, like HTTP transports are.



#### Connection Timeouts


 All transport implementations that perform some network access allow the configuration of a several timeouts, for example to allow the user to tell Maven how long to wait before giving up on a connection that has not responded.



```
<settings>
  <servers>
    <server>
      <id>my-server</id>
      <configuration>
        <connectTimeout>10000</connectTimeout> <!-- milliseconds -->
        <requestTimeout>1800000</requestTimeout> <!-- milliseconds -->
      </configuration>
    </server>
  </servers>
</settings>
```

 These above define per-server timeout configuration, and show default values.


 These are the standard ways to configure transport, regarding custom headers, and various timeouts. Each transport MAY introduce it's own specific configuration, like we can see below for Wagon.




### Advanced Configuration of the HttpClient HTTP Wagon


 You can use the default wagon implementation for a given protocol, or you can select an alternative wagon `provider` on a per-protocol basis. For more information, see the [Guide to Wagon Providers](./guide-wagon-providers.html) \[3\]. The default wagon http(s) is the HttpClient based on [Apache Http Client 4.5](https://hc.apache.org/httpcomponents-client-4.5.x/). HTTP connection pooling prevents reopening new connections to the same server for each request. This pool feature is configurable with some parameters \[4\]. The default wagon comes with some default configuration:



 - http(s) connection pool: default to 20.

 - readTimeout: default to 1,800,000ms (\~30 minutes) (see section `Read time out` below)

 - default Preemptive Authentication only with PUT (GET doesn't use anymore default Preemptive Authentication)


#### Introduction


 The HttpClient-based HTTP wagon offers more control over the configuration used to access HTTP-based Maven repositories. For starters, you have fine-grained control over what HTTP headers are used when resolving artifacts. In addition, you can also configure a wide range of parameters to control the behavior of HttpClient itself. Best of all, you have the ability to control these headers and parameters for all requests, or individual request types (GET, HEAD, and PUT).



#### The Basics


 Without any special configuration, Maven's HTTP wagon uses some default HTTP headers and client parameters when managing artifacts. The default headers are:



```
Cache-control: no-cache
Cache-store: no-store
Pragma: no-cache
Expires: 0
Accept-Encoding: gzip
```

 In addition, PUT requests made with the HTTP wagon use the following HttpClient parameter:



```
http.protocol.expect-continue=true
```

 From the HttpClient documentation\[2\], this parameter provides the following functionality:



```
Activates 'Expect: 100-Continue' handshake for the entity enclosing methods. 
The 'Expect: 100-Continue' handshake allows a client that is sending a request 
message with a request body to determine if the origin server is willing to 
accept the request (based on the request headers) before the client sends the 
request body.

The use of the 'Expect: 100-continue' handshake can result in noticeable performance 
improvement for entity enclosing requests (such as POST and PUT) that require 
the target server's authentication.

'Expect: 100-continue' handshake should be used with caution, as it may cause 
problems with HTTP servers and proxies that do not support HTTP/1.1 protocol.
```

 Without this setting, PUT requests that require authentication transfer their entire payload to the server before that server issues an authentication challenge. In order to complete the PUT request, the client must then re-send the payload with the proper credentials specified in the HTTP headers. This results in twice the bandwidth usage, and twice the time to transfer each artifact.


 Another option to avoid this double transfer is what's known as preemptive authentication, which involves sending the authentication headers along with the original PUT request. However, there are a few potential issues with this approach. For one thing, in the event you have an unused `<server>` entry that specifies an invalid username/password combination, some servers may respond with a `401 Unauthorized` even if the server doesn't actually require any authentication for the request. In addition, blindly sending authentication credentials with every request regardless of whether the server has made a challenge can result in a security hole, since the server may not make provisions to secure credentials for paths that don't require authentication.


 We'll discuss preemptive authentication in another example, below.



#### Configuring GET, HEAD, PUT, or All of the Above


 In all of the examples below, it's important to understand that you can configure the HTTP settings for all requests made to a given server, or for only one method. To configure all methods for a server, use the following section of the `settings.xml` file:



```
<settings>
  [...]
  <servers>
    <server>
      <id>the-server</id>
      <configuration>
        <httpConfiguration>
          <all>
            [ Your configuration here. ]
          </all>
        </httpConfiguration>
      </configuration>
    </server>
  </servers>
</settings>
```

 On the other hand, if you can live with the default configuration for most requests - say, HEAD and GET requests, which are used to check for the existence of a file and retrieve a file respectively - maybe you only need to configure the PUT method:



```
<settings>
  [...]
  <servers>
    <server>
      <id>the-server</id>
      <configuration>
        <httpConfiguration>
          <put>
            [ Your configuration here. ]
          </put>
        </httpConfiguration>
      </configuration>
    </server>
  </servers>
</settings>
```

 For clarity, the other two sections are `<get>` for GET requests, and `<head>` for HEAD requests. I know that's going to be hard to remember...



#### Taking Control of Your HTTP Headers


 As you may have noticed above, the default HTTP headers do have the potential to cause problems. For instance, some websites set the encoding for downloading GZipped files as `gzip`, in spite of the fact that the HTTP request itself isn't being sent using GZip compression. If the client is using the `Accept-Encoding: gzip` header, this can result in the client itself decompressing the GZipped file _during the transfer_ and writing the decompressed file to the local disk with the original filename. This can be misleading to say the least, and can use up an inordinate amount of disk space on the local computer.


 To turn off this default behavior, simply disable the default headers. Then, respecify the other headers that you are still interested in, like this:



```
<settings>
  [...]
  <servers>
    <server>
      <id>openssl</id>
      <configuration>
        <httpConfiguration>
          <put>
            <useDefaultHeaders>false</useDefaultHeaders>
            <headers>
              <property>
                <name>Cache-control</name>
                <value>no-cache</value>
              </property>
              <property>
                <name>Cache-store</name>
                <value>no-store</value>
              </property>
              <property>
                <name>Pragma</name>
                <value>no-cache</value>
              </property>
              <property>
                <name>Expires</name>
                <value>0</value>
              </property>
              <property>
                <name>Accept-Encoding</name>
                <value>*</value>
              </property>
            </headers>
          </put>
        </httpConfiguration>
      </configuration>
    </server>
    [...]
  </servers>
  [...]
</settings>
```


#### Fine-Tuning HttpClient Parameters


 Going beyond the power of HTTP request parameters, HttpClient provides a host of other configuration options. In most cases, you won't need to customize these. But in case you do, Maven provides access to specify your own fine-grained configuration for HttpClient. Again, you can specify these parameter customizations per-method (HEAD, GET, or PUT), or for all methods of interacting with a given server. For a complete list of supported parameters, see the link\[2\] in Resources section below.


##### Non-String Parameter Values


 Many of the configuration parameters for HttpClient have simple string values; however, there are important exceptions to this. In some cases, you may need to specify boolean, integer, or long values. In others, you may even need to specify a collection of string values. You can specify these using a simple formatting syntax, as follows:



 1 **booleans:** `%b,<value>`

 1 **integer:** `%i,<value>`

 1 **long:** `%l,<value>` (yes, that's an 'L', not a '1')

 1 **double:** `%d,<value>`

 1 **collection of strings:** `%c,<value1>,<value2>,<value3>,...`, which could also be specified as:

```
%c,
<value1>,
<value2>,
<value3>,
...
```



 As you may have noticed, this syntax is similar to the format-and-data strategy used by functions like `sprintf()` in many languages. The syntax has been chosen with this similarity in mind, to make it a little more intuitive to use.



##### Example: Using Preemptive Authentication


 Using the above syntax, you can configure preemptive authentication for PUT requests using the boolean HttpClient parameter `http.authentication.preemptive`, like this:



```
<settings>
  <servers>
    <server>
      <id>my-server</id>
      <configuration>
        <httpConfiguration>
          <put>
            <params>
              <property>
                <name>http.authentication.preemptive</name>
                <value>%b,true</value>
              </property>
            </params>
          </put>
        </httpConfiguration>
      </configuration>
    </server>
  </servers>
</settings>
```

 Another option is to make write it like this:



```
<settings>
  <servers>
    <server>
      <id>my-server</id>
      <configuration>
        <httpConfiguration>
          <put>
            <usePreemptive>true</usePreemptive>
          </put>
        </httpConfiguration>
      </configuration>
    </server>
  </servers>
</settings>
```


##### Example: Lifting auth scope restriction for external authentication systems


 Maven Wagon by default limits supplied credentials to the host:port combination scope, ignoring any other target servers. When the target server delegates authentication to an external system, you need to deliberately lift that scope limitation. Configure your server element to pass authentication to all target servers which challenge the client. +---+ _settings_ _servers_ _server_ _id_my-server_/id_ _configuration_ _basicAuthScope_ _host_ANY_/host_ _port_ANY_/port_ _!-- or even 443 to force the use of TLS --_ _/basicAuthScope_ _httpConfiguration_ _all_ _params_ _property_ _name_http.protocol.cookie-policy_/name_ _value_standard_/value_ _/property_ _/params_ _/all_ _/httpConfiguration_ _/configuration_ _/server_ _/servers_ _/settings_ +---+



##### Ignoring Cookies


 Like the example above, telling the HttpClient to ignore cookies for all methods of request is a simple matter of configuring the `http.protocol.cookie-policy` parameter (it uses a regular string value, so no special syntax is required):



```
<settings>
  <servers>
    <server>
      <id>my-server</id>
      <configuration>
        <httpConfiguration>
          <all>
            <params>
              <param>
                <name>http.protocol.cookie-policy</name>
                <value>ignore</value>
              </param>
            </params>
          </all>
        </httpConfiguration>
      </configuration>
    </server>
  </servers>
</settings>
```

 The configuration above can be useful in cases where the repository is using cookies - like the session cookies that are often mistakenly turned on or left on in appservers - alongside HTTP redirection. In these cases, it becomes far more likely that the cookie issued by the appserver uses a `Path` that is inconsistent with the one used by the client to access the server. If you have this problem, and know that you don't need to use this session cookie, you can ignore cookies from this server with the above configuration.




#### Support for General-Wagon Configuration Standards


 It should be noted that configuration options previously available in the HttpClient-driven HTTP wagon are still supported in addition to this new, fine-grained approach. These include the configuration of HTTP headers and connection timeouts. Let's examine each of these briefly:


##### HTTP Headers


 In all HTTP Wagon implementations, you can add your own HTTP headers like this:



```
<settings>
  <servers>
    <server>
      <id>my-server</id>
      <configuration>
        <httpHeaders>
          <property>
            <name>Foo</name>
            <value>Bar</value>
          </property>
        </httpHeaders>
      </configuration>
    </server>
  </servers>
</settings>
```

 It's important to understand that the above approach doesn't allow you to turn off all of the default HTTP headers; nor does it allow you to specify headers on a per-method basis. However, this configuration remains available in both the lightweight and httpclient-based Wagon implementations.



##### Connection Timeouts


 All wagon implementations that extend the `AbstractWagon` class, including those for SCP, HTTP, FTP, and more, allow the configuration of a connection timeout, to allow the user to tell Maven how long to wait before giving up on a connection that has not responded. This option is preserved in the HttpClient-based wagon, but this wagon also provides a fine-grained alternative configuration that can allow you to specify timeouts per-method for a given server. The old configuration option - which is still supported - looks like this:



```
<settings>
  <servers>
    <server>
      <id>my-server</id>
      <configuration>
        <timeout>6000</timeout> <!-- milliseconds -->
      </configuration>
    </server>
  </servers>
</settings>
```

 ...while the new configuration option looks more like this:



```
<settings>
  <servers>
    <server>
      <id>my-server</id>
      <configuration>
        <httpConfiguration>
          <put>
            <connectionTimeout>10000</connectionTimeout> <!-- milliseconds -->
          </put>
        </httpConfiguration>
      </configuration>
    </server>
  </servers>
</settings>
```

 If all you need is a per-server timeout configuration, you still have the option to use the old `<timeout>` parameter. If you need to separate timeout preferences according to HTTP method, you can use one more like that specified directly above.



##### Read time out


 With Wagon 2.0 and Apache Maven 3.0.4, a default timeout of 30 minutes comes by default. If you want to change this value, you can add the following setup in your settings:



```
<settings>
  <servers>
    <server>
      <id>my-server</id>
      <configuration>
        <httpConfiguration>
          <put>
            <readTimeout>120000</readTimeout> <!-- milliseconds -->
          </put>
        </httpConfiguration>
      </configuration>
    </server>
  </servers>
</settings>
```



#### Resources



 1 [HttpClient website](https://hc.apache.org/httpcomponents-client-4.5.x/)

 1 [HttpClient preference architecture and configuration guide](https://hc.apache.org/httpclient-3.x/preference-api.html)

 1 [Guide to Wagon Providers](./guide-wagon-providers.html)

 1 [Wagon Http](/wagon/wagon-providers/wagon-http/)




