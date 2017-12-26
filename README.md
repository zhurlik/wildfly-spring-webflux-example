# wildfly-spring-webflux-example
This project was created to be able to show how you can use **[Spring Webflux](https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html#spring-webflux)** under **Wildfly**.
# Changes in the standalone.xml
* add a new `<filter-ref>`     
`<filter-ref name="simplewebflux"/>`
* add a new `<filter>`   
`<filter class-name="com.zhurlik.WebfluxHandler" name="simplewebflux" module="com.zhurlik" />`    

You should have something like this:    
```
        <subsystem xmlns="urn:jboss:domain:undertow:4.0">    
            <buffer-cache name="default"/>    
            <server name="default-server">    
                <http-listener name="default" socket-binding="http" redirect-socket="https" enable-http2="true"/>    
                <https-listener name="https" socket-binding="https" security-realm="ApplicationRealm" enable-http2="true"/>    
                <host name="default-host" alias="localhost">    
                    <location name="/" handler="welcome-content"/>    
                    <filter-ref name="server-header"/>    
                    <filter-ref name="x-powered-by-header"/>    
                    <filter-ref name="simplewebflux"/>    
                    <http-invoker security-realm="ApplicationRealm"/>    
                </host>    
            </server>
            <servlet-container name="default">
                <jsp-config/>
                <websockets/>
            </servlet-container>
            <handlers>
                <file name="welcome-content" path="${jboss.home.dir}/welcome-content"/>
            </handlers>
            <filters>
                <filter class-name="com.zhurlik.WebfluxHandler" name="simplewebflux" module="com.zhurlik" />
                <response-header name="server-header" header-name="Server" header-value="WildFly/11"/>
                <response-header name="x-powered-by-header" header-name="X-Powered-By" header-value="Undertow/1"/>
            </filters>
        </subsystem>
```

# Wildfly module
To be able `HttpHandler` we have to create our custom Wyldfly module that will use **Spring Webflux**. It means that at least we need to collect all required jars. Another important stuff is to use `org.springframework.http.server.reactive.UndertowHttpHandlerAdapter`.    
This class provides as an ability to convert `org.springframework.http.server.reactive.HttpHandler` -> `io.undertow.server.HttpHandler`    

I have used gradle plugin [com.github.zhurlik.jbossmodules](https://plugins.gradle.org/plugin/com.github.zhurlik.jbossmodules) for generating the following **module.xml**
```
<?xml version='1.0' encoding='utf-8'?>
<module xmlns='urn:jboss:module:1.6' name='com.zhurlik'>
  <resources>
    <resource-root path='simple-webflux-app-0.1.jar' />
    <resource-root path='spring-webflux-5.0.2.RELEASE.jar' />
    <resource-root path='spring-core-5.0.2.RELEASE.jar' />
    <resource-root path='spring-context-5.0.2.RELEASE.jar' />
    <resource-root path='spring-beans-5.0.2.RELEASE.jar' />
    <resource-root path='spring-web-5.0.2.RELEASE.jar' />
    <resource-root path='reactor-core-3.1.2.RELEASE.jar' />
    <resource-root path='reactive-streams-1.0.1.jar' />
    <resource-root path='jackson-dataformat-smile-2.9.3.jar' />
    <resource-root path='jackson-databind-2.9.3.jar' />
    <resource-root path='jackson-core-2.9.3.jar' />
    <resource-root path='jackson-annotations-2.9.0.jar' />
  </resources>
  <dependencies>
    <module name='io.undertow.core' />
    <module name='org.slf4j' />
    <module name='org.wildfly.extension.undertow' />
    <module name='javax.api' />
    <module name='org.apache.commons.logging' />
    <module name='org.jboss.xnio' />
  </dependencies>
</module>
```

You can look at [modules/build.gradle](modules/build.gradle)
