---
title: webservice学习三，soapui测试
date: 2018-03-05 19:37:16
type: tags
tags: 
  - webservice
  - soapui
categories:
  - webservice
toc: true
comments: true
---
# soapui测试
## 加入header头验证
<!--more-->
```xml
<soapenv:Header>
    <wsse:Security xmlns:wsse="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd">
    <wsse:UsernameToken >
    <wsse:Username>admin</wsse:Username>
    <wsse:Password Type="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-username-token-profile-1.0#PasswordText">ZDg3NDA5NjUyYmNk</wsse:Password>
    </wsse:UsernameToken>
    </wsse:Security>
</soapenv:Header>
```
## request和response
request
```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:web="http://webservice.riskManage.zstimes.com/">
  <soapenv:Header>
	<wsse:Security xmlns:wsse="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd">
	<wsse:UsernameToken >
	<wsse:Username>admin</wsse:Username>
	<wsse:Password Type="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-username-token-profile-1.0#PasswordText">ZDg3NDA5NjUyYmNk</wsse:Password>
	</wsse:UsernameToken>
	</wsse:Security>
</soapenv:Header>
   <soapenv:Body>
      <web:helloWord>
         <!--Optional:-->
         <userName>"zhangsan"</userName>
      </web:helloWord>
   </soapenv:Body>
</soapenv:Envelope>
```
response
```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
   <soap:Body>
      <ns2:helloWordResponse xmlns:ns2="http://webservice.riskManage.zstimes.com/">
         <return>hello "zhangsan"!!!</return>
      </ns2:helloWordResponse>
   </soap:Body>
</soap:Envelope>
```