---
title: Disable DynAdmin authentication of Oracle Commerce
date: 2016-04-01
tags: ['atg', 'configuration', 'oraclecommerce', 'hack']
author: Buddha
description: Couple of handy dandy tricks for bypassing the authentication mechanism of Dyn Admin for a quick opening of it.
---
We use Dyn/Admin for many of the development related tasks of Oracle Commerce(ATG). However, the log-in ticket expires too frequently forcing us to enter credentials repeatedly. Some times we will also have to give both application server(weblogic/jboss) credentials and dynamo administration credentials. As we are not in a production critical environment where multiple people may access our DynAdmin and create issues, it is not necessary to have this security check every time we try to open DynAdmin of our development environment. I have found following solutions to be free of this issue. This can come in handy when we are doing active development and have to frequently access dynamo administration.

## Solution 1 - Enable lazyAthentication
This approach works by enabling Lazy Athentication which means login will only be asked once per session. This means, as long as the session is not expired, dyn/admin will not ask for credentials again. <!-- more -->I have observed that this means a very long time. Sometimes even the the authentication credentials are not asked after restarting the server. Follow the below steps to enable lazyAthentication.
1. Navigate to %DYNAMO_HOME%/localconfig/atg/dynamo
1. Create a folder named servlet if it doesn't already exist
1. Create a folder named adminpipeline inside servlet folder if it doesn't already exist
1. Create a properties files with the name Authenticator.properties with below content in it and restart the server.

{% codeblock atg/dynamo/servlet/adminpipeline/Authenticator.properties lang:java %}
$class=atg.servlet.pipeline.UserAuthorityAuthenticator
$scope=global
lazyAthentication=true
{% endcodeblock %}

## Solution 2 - Disable AuthenticationServlet
First solution provides respite from entering DynAdmin credentials frequently, however we still have to enter it once per session. Here is a better approach, my personal favourite. If we would like to disable the authentication for much longer period, we can disable authentication altogether, it can be acheived by disabling AuthenticationServlet component. Below approach can be followed for achieving that.
1. Navigate to %DYNAMO_HOME%/localconfig/atg/dynamo
1. Create a folder named servlet if it doesn't already exist
1. Create a folder named adminpipeline inside servlet folder if it doesn't already exist
1. Create a properties files with the name AuthenticationServlet.properties with below content in it and restart the server.

{% codeblock atg/dynamo/servlet/adminpipeline/AuthenticationServlet.properties  lang:java %}
$class=atg.servlet.pipeline.BasicAuthenticationPipelineServlet
enabled=false
{% endcodeblock %}

Authentication can be enabled again by changing enabled=true in AuthenticationServlet.properties.

{% admonition warning Watchout %}
It is a good idea to disable lazyAthentication and enable AuthenticationServlet component when testing some authentication related tests. Neither of the approaches have been tested with LDAP or other authentication mechanisms.
{% endadmonition %}
