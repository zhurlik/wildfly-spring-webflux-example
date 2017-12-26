# wildfly-spring-webflux-example
This project was created to be able to show how you can use Spring-Webflux under Wildfly.
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
