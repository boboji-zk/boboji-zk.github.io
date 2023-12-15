---
title: springboot为web层添加统一前缀
date: 2023-12-15 20:17:41
categories: SpringBoot
---

# springboot为web层添加统一前缀

## 一、配置文件方式

`application.properties` 全局配置文件配置：

```properties
server.servlet.context-path=/dataSource
```

## 二、重写configurePathMatch方法

在实现 `WebMvcConfigurer` 接口中重写 `configurePathMatch` 方法。

```java
@Configuration
public class MyWebMvcConfigurerAdapter implements WebMvcConfigurer {
    
    // 类上有注解RestController和Controller的接口上添加统一前缀/dataSource
    @Override
    public void configurePathMatch(PathMatchConfigurer configurer) {
        configurer.addPathPrefix("/dataSource", c -> c.isAnnotationPresent(RestController.class) || c.isAnnotationPresent(Controller.class));
    }
    
}
```

如果一个接口有多个版本，还可通过控制注解的方法进一步优化：

1. 以下是多版本控制的配置类：

```java
@Configuration
public class MyWebMvcConfigurerAdapter implements WebMvcConfigurer {
	
    /**
     * 新增两条匹配规则：
     * /api/v1  ->  @ApiV1RestController
     * /api/v2  ->  @ApiV2RestController
     */
    @Override
    public void configurePathMatch(PathMatchConfigurer configurer) {
        configurer.addPathPrefix("/api/v1", c -> c.isAnnotationPresent(ApiV1RestController.class))
            .addPathPrefix("/api/v2", c -> c.isAnnotationPresent(ApiV2RestController.class));
    
}
```

2. 以下是两个有@RestController的注解：

```java
import org.springframework.core.annotation.AliasFor;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import java.lang.annotation.*;
 
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@RestController
@RequestMapping
public @interface ApiV1RestController {
    /**
     * Alias for {@link RequestMapping#name}.
     */
    @AliasFor(annotation = RequestMapping.class)
    String name() default "";
 
    /**
     * Alias for {@link RequestMapping#value}.
     */
    @AliasFor(annotation = RequestMapping.class)
    String[] value() default {};
 
    /**
     * Alias for {@link RequestMapping#path}.
     */
    @AliasFor(annotation = RequestMapping.class)
    String[] path() default {};
}


import org.springframework.core.annotation.AliasFor;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import java.lang.annotation.*;
 
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@RestController
@RequestMapping
public @interface ApiV2RestController {
    /**
     * Alias for {@link RequestMapping#name}.
     */
    @AliasFor(annotation = RequestMapping.class)
    String name() default "";
 
    /**
     * Alias for {@link RequestMapping#value}.
     */
    @AliasFor(annotation = RequestMapping.class)
    String[] value() default {};
 
    /**
     * Alias for {@link RequestMapping#path}.
     */
    @AliasFor(annotation = RequestMapping.class)
    String[] path() default {};
}
```

3. 以下是controller层的使用

```java
@ApiV1RestController("/demo1")
public class DemoController {
}


@ApiV2RestController("/demo2")
public class DemoController {
}
```

