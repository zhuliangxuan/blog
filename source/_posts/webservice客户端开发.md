---
title: webservice学习二，客户端开发
date: 2018-03-04 19:37:16
type: tags
tags: 
  - webservice
categories:
  - webservice
toc: true
comments: true
---
# maven
## 创建maven项目
pom.xml
```xml
<dependencies>
	<!-- 6.webservice-cxf -->
	<dependency>
		<groupId>org.apache.cxf</groupId>
		<artifactId>cxf-rt-frontend-jaxws</artifactId>
		<version>3.0.5</version>
	</dependency>
	<dependency>
		<groupId>org.apache.cxf</groupId>
		<artifactId>cxf-rt-transports-http</artifactId>
		<version>3.0.5</version>
	</dependency>
	<dependency>
		<groupId>org.apache.cxf</groupId>
		<artifactId>cxf-rt-ws-security</artifactId>
		<version>3.0.5</version>
	</dependency>
</dependencies>
```
<!--more-->
## 创建验证
MyPasswordCallBack.java
```java
import org.apache.wss4j.common.ext.WSPasswordCallback;
import javax.security.auth.callback.Callback;
import javax.security.auth.callback.CallbackHandler;
import javax.security.auth.callback.UnsupportedCallbackException;
import java.io.IOException;

public class MyPasswordCallBack implements CallbackHandler {

	public void handle(Callback[] callbacks) throws IOException, UnsupportedCallbackException {

		WSPasswordCallback pwdcb = (WSPasswordCallback) callbacks[0];
		pwdcb.setPassword("ZDg3NDA5NjUyYmNk");
		pwdcb.setIdentifier("admin");

	}

}
```
## 根据wsdl生成客户端代码
输入命令
```
wsimport -keep -p com.demo.client -d D:\\(存放的地址) http://XX/Account?wsdl（wsdl地址）

命令参数说明： 
-d:生成客户端执行类的class文件的存放目录（默认存放在C:\Users\Administrator\） 
-s:生成客户端执行类的源文件的存放目录（默认存放在C:\Users\Administrator\） 
-p:定义生成类的包名
```
## 导入代码&测试
测试代码App.java
```java
import com.zstimes.util.MyPasswordCallBack;
import com.zstimes.riskmanage.webservice.CxfDemo;
import org.apache.cxf.jaxws.JaxWsProxyFactoryBean;
import org.apache.cxf.ws.security.wss4j.WSS4JOutInterceptor;
import org.apache.wss4j.dom.handler.WSHandlerConstants;

import java.util.HashMap;
import java.util.Map;

/**
 * Hello world!
 *
 */
public class App {
    public static void main(String[] args) {

        JaxWsProxyFactoryBean jax = new JaxWsProxyFactoryBean();

        jax.setAddress("http://zssd.com:7201/riskManageWeb/webservice/helloWorld?wsdl");

        jax.setServiceClass(CxfDemo.class);

        //
        Map<String, Object> map = new HashMap<String, Object>();
        map.put(WSHandlerConstants.ACTION, WSHandlerConstants.USERNAME_TOKEN);
        map.put(WSHandlerConstants.PASSWORD_TYPE, "PasswordText");
        map.put(WSHandlerConstants.PW_CALLBACK_CLASS, MyPasswordCallBack.class.getName());
        map.put(WSHandlerConstants.USER, "username");
        WSS4JOutInterceptor wss4jOutInterceptor = new WSS4JOutInterceptor(map);
        jax.getOutInterceptors().add(wss4jOutInterceptor);
        //

        CxfDemo create = (CxfDemo) jax.create();

        String callTrans = create.helloWord("zhangsan");

        System.out.println(callTrans);

    }
}
```
控制台输出
```
hello "zhangsan"!!!
```