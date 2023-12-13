---
title: hive-jdbc造成依赖冲突.md
date: 2023-12-13 20:05:25
categories: 踩坑记录
---
# hive-jdbc造成依赖冲突

## 事故现场

**pom依赖：**

```xml
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-jdbc</artifactId>
            <version>1.1.1</version>
        </dependency>
```

**启动报错：**

```bash
2023-12-13 19:38:35.675 ERROR 5116 --- [           main] o.s.boot.SpringApplication               : Application run failed

org.springframework.context.ApplicationContextException: Unable to start web server; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean wi
th name 'tomcatServletWebServerFactory' defined in class path resource [org/springframework/boot/autoconfigure/web/servlet/ServletWebServerFactoryConfiguration$EmbeddedTomcat.class]: I
nitialization of bean failed; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'jettyWebServerFactoryCustomizer' defined in cl
ass path resource [org/springframework/boot/autoconfigure/web/embedded/EmbeddedWebServerFactoryCustomizerAutoConfiguration$JettyWebServerFactoryCustomizerConfiguration.class]: Bean ins
tantiation via factory method failed; nested exception is org.springframework.beans.BeanInstantiationException: Failed to instantiate [org.springframework.boot.autoconfigure.web.embedd
ed.JettyWebServerFactoryCustomizer]: Factory method 'jettyWebServerFactoryCustomizer' threw exception; nested exception is java.lang.NoClassDefFoundError: org/eclipse/jetty/server/Requ
estLog$Writer

```

**奇怪的现象：**

idea上可以正常启动，但是一旦生成jar包后用命令 `java -jar xxx.jar`，则报错。

## 解决

**原因：**

**hive-jdbc** 自带 **jetty-all** 依赖，springboot启动时会去加载jetty的配置，所以造成报错。

**如何解决：**

从 **hive-jdbc依赖** 中排除掉 **jetty-all依赖**

```XML
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-jdbc</artifactId>
            <version>1.1.</version>
            <exclusions>
                <exclusion>
                    <artifactId>jetty-all</artifactId>
                    <groupId>org.eclipse.jetty.aggregate</groupId>
                </exclusion>
            </exclusions>
        </dependency>
```

