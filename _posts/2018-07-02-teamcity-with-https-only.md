---
layout: post
title: Setup TeamCity with HTTPS
categories: [CI, TeamCity, Tools]
tags: [CI, Security]
date: 2018-07-02 23:10:00 +0200
---
{% include JB/setup %}

At work we have recently moved our CI server and made it available without having to use VPN. As we did this switch we also made it accessible only thru HTTPS. Here are the steps we did to make that work.

First we copied out pfx file to the CI server, then we had to make a few adjustments to some of the config files.

In TeamCity/conf/server.xml we added this element, here you can see the path to the pfx file and the password for the file.
```xml
<Connector port="443" protocol="org.apache.coyote.http11.Http11NioProtocol"
    SSLEnabled="true"
    scheme="https"
    secure="true"
    connectionTimeout="60000"
    redirectPort="8543"
    clientAuth="false"
    sslProtocol="TLS"
    useBodyEncodingForURI="true"
    keystoreFile="C:\TeamCity\conf\cert.pfx"
    keystorePass="password"
    socket.txBufSize="64000"
    socket.rxBufSize="64000"
    tcpNoDelay="1"
    /> 
```

After a restert of the TeamCity service it is now possible to reach the server on HTTPS.

The next step for us was to force the use of HTTPS, so we had to make a change to TeamCity/conf/web.xml. We added this XML to the file just before the web-app closing tag.

```xml
    <security-constraint>
        <web-resource-collection>
            <web-resource-name>Restricted URLs</web-resource-name>
            <url-pattern>/*</url-pattern>
        </web-resource-collection>
        <user-data-constraint>
            <transport-guarantee>CONFIDENTIAL</transport-guarantee>
        </user-data-constraint>
    </security-constraint>
</web-app>
```

After a restart we can still access TeamCity over HTTPS, but when using HTTP the redirect goes to HTTPS on port 8543. We open up TeamCity/conf/server.xml again and change one line in the HTTP connector. Here we change the redirectPort from 8543 to 443.

```xml
<Connector port="80" protocol="org.apache.coyote.http11.Http11NioProtocol"
               connectionTimeout="60000"
               redirectPort="443"
               useBodyEncodingForURI="true"
               socket.txBufSize="64000"
               socket.rxBufSize="64000"
               tcpNoDelay="1"
    />
```

After another restart of the TeamCity service all is working as expected and our users is forced over on HTTPS.