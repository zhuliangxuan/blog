---
title: webservice学习一，服务端开发
date: 2018-03-04 20:37:16
type: tags
tags: 
  - webservice
categories:
  - webservice
toc: true
comments: true
---
# idea开发webservice服务端
# SSM集成
## 引入依赖
pom.xml
```xml
<!--cxf webservice依赖-->
<dependency>
	<groupId>org.apache.cxf</groupId>
	<artifactId>cxf-rt-frontend-jaxws</artifactId>
	<version>3.2.1</version>
</dependency>
<dependency>
	<groupId>org.apache.cxf</groupId>
	<artifactId>cxf-rt-transports-http</artifactId>
	<version>3.2.1</version>
</dependency>
<dependency>
	<groupId>org.apache.cxf</groupId>
	<artifactId>cxf-rt-ws-security</artifactId>
	<version>3.2.1</version>
</dependency>
<dependency>
	<groupId>org.apache.cxf</groupId>
	<artifactId>cxf-rt-transports-http-jetty</artifactId>
	<version>3.2.1</version>
</dependency>
```
<!--more-->
## 编写webservice服务接口及实现类
CxfDemo.java
```java
import javax.jws.WebMethod;
import javax.jws.WebParam;
import javax.jws.WebService;

/**
 * Project Name:riskManage
 * Package Name:com.zstimes.riskManage.boot.service
 * File Name:CxfDemo
 * Date:2018/2/7 0007 9:57
 * company: zstax
 * Copyright (c) 2018, zhuliangxuan@zstax.com All Rights Reserved.
 * Description:webservice测试接口
 */
@WebService
public interface CxfDemo {

    @WebMethod
    String   helloWord(@WebParam(name = "userName") String arg);
}
```
CxfDemoImpl.java
```java
/**
 * Project Name:riskManage
 * Package Name:com.zstimes.riskManage.boot.service.impl
 * File Name:CxfDemoImpl
 * Date:2018/2/7 0007 10:02
 * company: zstax
 * Copyright (c) 2018, zhuliangxuan@zstax.com All Rights Reserved.
 * Description:接口实现
 */
public class CxfDemoImpl implements CxfDemo {
    public String helloWord(String arg) {
        return "hello "+arg+"!!!";
    }
}
```
DemoServerPasswordCallback.java
```java
/**
 * Project Name:riskManage
 * Package Name:com.zstimes.riskManage.webservice
 * File Name:DemoServerPasswordCallback
 * Date:2018/2/7 0007 14:19
 * company: zstax
 * Copyright (c) 2018, zhuliangxuan@zstax.com All Rights Reserved.
 * Description:添加服务端身份验证
 */

import org.apache.wss4j.common.ext.WSPasswordCallback;
import javax.security.auth.callback.Callback;
import javax.security.auth.callback.CallbackHandler;
import javax.security.auth.callback.UnsupportedCallbackException;
import java.io.IOException;


public class DemoServerPasswordCallback implements CallbackHandler {
    public void handle(Callback[] callbacks) throws IOException,
            UnsupportedCallbackException {
        WSPasswordCallback pc = (WSPasswordCallback) callbacks[0];
        String pw = pc.getPassword();
        String idf = pc.getIdentifier();
        //特别注意的是 这里的pw肯是null, 无论你password是否传值。cxf 2.4之后，
        //密码的比较是框架自动帮我们完成，因此不需要我们获取传递过来的密码，如果你
        //一定要查看密码的话,可以通过new String(pc.getKey())获取。在该回调函数中
        //我们只需要使用 idf 从数据库中查询出密码，使用pc.setPassword()方法将密码设
        //置进去,框架获取的的密码后会进行比较，并通过抛出异常的方式提示验证出错。
        System.out.println("密码是: " + pw);
        System.out.println("身份是: " + idf);
        //根据idf 我们从数据中查询出的密码 假如是 "abc"
        pc.setPassword("ZDg3NDA5NjUyYmNk");
        pc.setIdentifier("admin");
    }
}
```
## 编写spring-cxf配置文件
spring-cxf-service.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:jaxws="http://cxf.apache.org/jaxws"
       xsi:schemaLocation=" http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://cxf.apache.org/jaxws http://cxf.apache.org/schemas/jaxws.xsd">
    <!-- 引cxf的一些核心配置 -->
    <import resource="classpath:META-INF/cxf/cxf.xml" />
    <import resource="classpath:META-INF/cxf/cxf-servlet.xml" />

    <bean id="cxfDemoImple" class="com.zstimes.riskManage.webservice.CxfDemoImpl"/>
    <bean id="DemoServerPasswordCallback"  class="com.zstimes.riskManage.webservice.DemoServerPasswordCallback"/>
    <jaxws:endpoint id="hello" implementor="#cxfDemoImple" address="/helloWorld" >
        <jaxws:inInterceptors>
            <bean class="org.apache.cxf.binding.soap.saaj.SAAJInInterceptor" />
            <bean class="org.apache.cxf.ws.security.wss4j.WSS4JInInterceptor">
                <constructor-arg>
                    <map>
                        <entry key="action" value="UsernameToken" />
                        <entry key="passwordType"
                               value="PasswordText" />
                        <entry key="user" value="FHDClient" />
                        <entry key="passwordCallbackRef">
                            <ref bean="DemoServerPasswordCallback" />
                        </entry>
                    </map>
                </constructor-arg>
            </bean>
        </jaxws:inInterceptors>
    </jaxws:endpoint>

</beans>
```
## shiro配置/webservice/** 匿名访问
## web.xml配置
web.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns="http://java.sun.com/xml/ns/javaee"
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
	version="3.0" metadata-complete="true">
	<display-name>riskManageWeb</display-name>

	<context-param>
		<param-name>log4jConfiguration</param-name>
		<param-value>classpath:log4j2.xml</param-value>
	</context-param>
	<context-param>
		<param-name>webAppRootKey</param-name>
		<param-value>riskManageWeb.root</param-value>
	</context-param>

	<!-- 编码过滤器 -->
	<filter>
		<filter-name>encodingFilter</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<async-supported>true</async-supported>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>UTF-8</param-value>
		</init-param>
		<init-param>
			<param-name>forceEncoding</param-name>
			<param-value>true</param-value>
		</init-param>
	</filter>
	<filter-mapping>
		<filter-name>encodingFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>

	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>
			classpath*:spring/spring-ckts-base.xml,
			classpath*:spring/spring-shiro-config.xml,
			classpath*:spring/spring-cxf-service.xml
		</param-value>
	</context-param>

	<!-- shiro 安全过滤器 -->
	<filter>
		<filter-name>shiroFilter</filter-name>
		<filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
		<init-param>
			<param-name>targetFilterLifecycle</param-name>
			<param-value>true</param-value>
		</init-param>
	</filter>
	<filter-mapping>
		<filter-name>shiroFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>

	<!-- Spring监听器 -->
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>

	<!-- 防止Spring内存溢出监听器 -->
	<listener>
		<listener-class>org.springframework.web.util.IntrospectorCleanupListener</listener-class>
	</listener>

	<!-- Spring MVC servlet -->
	<servlet>
		<servlet-name>SpringMVC</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>classpath:spring/spring-ckts-mvc.xml</param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>
		<async-supported>true</async-supported>
	</servlet>
	<servlet-mapping>
		<servlet-name>SpringMVC</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>
	<!--webservice servlet-->
	<servlet>
		<servlet-name>CXFServlet</servlet-name>
		<servlet-class>org.apache.cxf.transport.servlet.CXFServlet</servlet-class>
		<load-on-startup>2</load-on-startup>
	</servlet>

	<servlet-mapping>
		<servlet-name>CXFServlet</servlet-name>
		<url-pattern>/webservice/*</url-pattern>
	</servlet-mapping>

	<!-- 配置session超时时间，单位分钟 -->
	<session-config>
		<session-timeout>30</session-timeout>
	</session-config>
	<welcome-file-list>
		<welcome-file>/index.jsp</welcome-file>
	</welcome-file-list>
	<!-- 出错页面定义 -->
	<error-page>
		<exception-type>java.lang.Throwable</exception-type>
		<location>/error/500.jsp</location>
	</error-page>
	<!-- 找不到页错误转向 -->
	<error-page>
		<error-code>404</error-code>
		<location>/error/404.jsp</location>
	</error-page>
	<!-- 系统内部错误转向 -->
	<error-page>
		<error-code>500</error-code>
		<location>/error/500.jsp</location>
	</error-page>
	<error-page>
		<error-code>403</error-code>
		<location>/error/403.jsp</location>
	</error-page>

</web-app>
```
## 测试
访问`http://zssd.com:7201/riskManageWeb/webservice/helloWorld?wsdl`
```xml
<wsdl:definitions xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:wsdl="http://schemas.xmlsoap.org/wsdl/" xmlns:tns="http://webservice.riskManage.zstimes.com/" xmlns:soap="http://schemas.xmlsoap.org/wsdl/soap/" xmlns:ns1="http://schemas.xmlsoap.org/soap/http" name="CxfDemoImplService" targetNamespace="http://webservice.riskManage.zstimes.com/">
<wsdl:types>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:tns="http://webservice.riskManage.zstimes.com/" elementFormDefault="unqualified" targetNamespace="http://webservice.riskManage.zstimes.com/" version="1.0">
<xs:element name="helloWord" type="tns:helloWord"/>
<xs:element name="helloWordResponse" type="tns:helloWordResponse"/>
<xs:complexType name="helloWord">
<xs:sequence>
<xs:element minOccurs="0" name="userName" type="xs:string"/>
</xs:sequence>
</xs:complexType>
<xs:complexType name="helloWordResponse">
<xs:sequence>
<xs:element minOccurs="0" name="return" type="xs:string"/>
</xs:sequence>
</xs:complexType>
</xs:schema>
</wsdl:types>
<wsdl:message name="helloWordResponse">
<wsdl:part element="tns:helloWordResponse" name="parameters"></wsdl:part>
</wsdl:message>
<wsdl:message name="helloWord">
<wsdl:part element="tns:helloWord" name="parameters"></wsdl:part>
</wsdl:message>
<wsdl:portType name="CxfDemo">
<wsdl:operation name="helloWord">
<wsdl:input message="tns:helloWord" name="helloWord"></wsdl:input>
<wsdl:output message="tns:helloWordResponse" name="helloWordResponse"></wsdl:output>
</wsdl:operation>
</wsdl:portType>
<wsdl:binding name="CxfDemoImplServiceSoapBinding" type="tns:CxfDemo">
<soap:binding style="document" transport="http://schemas.xmlsoap.org/soap/http"/>
<wsdl:operation name="helloWord">
<soap:operation soapAction="" style="document"/>
<wsdl:input name="helloWord">
<soap:body use="literal"/>
</wsdl:input>
<wsdl:output name="helloWordResponse">
<soap:body use="literal"/>
</wsdl:output>
</wsdl:operation>
</wsdl:binding>
<wsdl:service name="CxfDemoImplService">
<wsdl:port binding="tns:CxfDemoImplServiceSoapBinding" name="CxfDemoImplPort">
<soap:address location="http://zssd.com:7201/riskManageWeb/webservice/helloWorld"/>
</wsdl:port>
</wsdl:service>
</wsdl:definitions>
```
# springboot集成