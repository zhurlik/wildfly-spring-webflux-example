# wildfly-spring-webflux-example
This project was created to be able to show how you can use Spring-Webflux under Wildfly.
# Changes in the standalone.xml
* add a new `<filter-ref>`     
`<filter-ref name="simplewebflux"/>`
* add a new `<filter>`   
`<filter class-name="com.zhurlik.WebfluxHandler" name="simplewebflux" module="com.zhurlik" />`
