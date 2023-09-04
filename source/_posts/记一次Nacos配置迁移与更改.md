---
title: 记一次Nacos配置迁移与更改
date: 2023-09-01 15:45:54
categories:
- 技术
tags:
- java
- Nacos
---

之前配合运维把Nacos的配置迁到了另一台机器上，看界面应该也更新了Nacos的版本，结果在一次本地启动时发现报了如下错误：

```
Whitelabel Error Page
This application has no explicit mapping for /error, so you are seeing this as a fallback.

Sun May 30 22:19:43 CST 2021
There was an unexpected error (type=Forbidden, status=403).
unknown user!
```

查了一下，发现是运维对nacos添加了权限...之后获取nacos都需要账户才行。

解决方法是首先将spring-cloud-alibaba-dependencies的版本改为2.2.0以上，之后移除spring-cloud-starter-alibaba-nacos-discovery以及spring-cloud-starter-alibaba-nacos-config依赖中的nacos-client，最后加入nacos-client依赖版本1.2.1以上版本。

```xml
<dependency>
  <groupId>com.alibaba.cloud</groupId>
  <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
  <exclusions>
		<exclusion>
			<groupId>com.alibaba.nacos</groupId>
			<artifactId>nacos-client</artifactId>
		</exclusion>
	</exclusions>
</dependency>
<dependency>
  <groupId>com.alibaba.cloud</groupId>
  <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
  <exclusions>
		<exclusion>
 			<groupId>com.alibaba.nacos</groupId>
			<artifactId>nacos-client</artifactId>
		</exclusion>
  </exclusions>
</dependency>

<dependency>
  	<groupId>com.alibaba.nacos</groupId>
  	<artifactId>nacos-client</artifactId>
   	<version>1.2.1</version>
</dependency>
```

之后就要修改自己的yml了

```yaml
spring:
  application:
    name: xxx
  cloud:
    nacos:
      config:
        server-addr: xxx
        namespace: xxx
        file-extension: yaml
        username: xxx
        password: xxx
        contextPath: /nacos
      discovery:
        server-addr: xxx
        namespace: xxx
        username: xxx
        password: xxx
```

改完之后，读取配置时又开始报错，看日志发现是存在一些空的配置，而估计是Nacos升级后不再支持空配置了，此时就需要在空配置后增加`""`来表示空值了。
