---
layout: post
title: "SpringMVC 静态资源缓存"
category: "java"
tags: ['java']
---

## ResourceHttpRequestHandler

`ResourceHttpRequestHandler` 可以使用 `ResourceResolvers` 和 `ResourceTransformers` 来优化处理静态资源。处理过程如下：

```
 Request for Resource
      |
      | HTTP request
      v
  Resolvers chain: FirstResolver, SecondResolver, ThirdResolver
  (each resolver can return the resource or delegate to the next one)
      |
      | Resolved Resource
      v
  Transformers chain: FirstTransformer, SecondTransformer
  (each transformer can transform the resource or just pass it along without modification)
      |
      | Transformed Resource
      v
  HTTP Response with Resource content
```

<!-- more -->

### `ResourceHttpRequestHandler` 的配置

配置方法一：

```java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {
  @Override
  public void addResourceHandlers(ResourceHandlerRegistry registry) {
    registry.addResourceHandler("/resources/**")
            .addResourceLocations("/public-resources/")
            .setCacheControl(CacheControl.maxAge(1, TimeUnit.HOURS).cachePublic());
  }

}
```


配置方法二：

```xml
<mvc:resources mapping="/resources/**" location="/public-resources/">
  <mvc:cache-control max-age="3600" cache-public="true"/>
</mvc:resources>
```


## ResourceResolvers

用于解析资源，将外面请求的 public 路径解析为内部的资源路径。

### 处理过程

```
 Resource link in a template source file
 |
 | Resource path (like “/css/main.css”)
 v
 Resolvers chain: FirstResolver, SecondResolver, ThirdResolver
 (each resolver can modify the resource path or delegate to the next one)
 |
 | Updated resource path (like “/css/main-0e37f12.css”)
 v
 Resource link in a rendered template
```

### 配置

```xml
<mvc:resources mapping="/resources/**" location="/public/">
  <mvc:resource-chain>
    <mvc:resource-cache/>
    <mvc:resolvers>
      <mvc:version-resolver>
        <mvc:content-version-strategy patterns="/**"/>
      </mvc:version-resolver>
    </mvc:resolvers>
  </mvc:resource-chain>
</mvc:resources>
```


### 实现类

| 实现类  | 作用 |
|:----------------:|:----------------------|
|PathResourceResolver | finds resources under configured locations (classpath, file system…) matching to the request path|
| CachingResourceResolver | resolves resources from a Cache instance or delegates to the next Resolver in the chain |
| GzipResourceResolver | finds variations of a resource with a “.gz” extension when HTTP clients support gzip compression |
| VersionResourceResolver | resolves request paths containing a version string, i.e. version information about the resource being requested. This resolver can be useful to set up HTTP caching strategies by changing resources’ URLs as they are updated. |



## ResourceTransformers

用于修改资源内容。

### 实现类

| 实现类  | 作用 |
|:----------------:|:----------------------|
| CssLinkResourceTransformer | modifies links in a CSS file to match the public URL paths that should be exposed to clients |
| CachingResourceTransformer | caches the result of transformations in a Cache or delegates to the next Transformer in the chain |
| AppCacheManifestTransfomer | helps handling resources within HTML5 AppCache manifests for HTML5 offline applications |

## 静态资源添加 Hash

### 1 添加 `filter`

修改 `web.xml`:

```xml
<!-- 静态资源 URL 添加 Hash -->
<filter>
  <filter-name>resourceUrlEncodingFilter</filter-name>
  <filter-class>org.springframework.web.servlet.resource.ResourceUrlEncodingFilter</filter-class>
</filter>
<!-- Ensure that you map it to the DispatcherServlet -->
<filter-mapping>
  <filter-name>resourceUrlEncodingFilter</filter-name>
  <servlet-name>webServlet</servlet-name>
  <!--<url-pattern>/*</url-pattern>-->
</filter-mapping>
```

### 2 添加 `resolver`

修改 SpringMVC 的配置文件：

```xml
<mvc:resources mapping="/static/**" location="/static/" cache-period="31536000">
  <mvc:resource-chain resource-cache="true" auto-registration="true">
    <mvc:resolvers>
      <mvc:version-resolver>
        <mvc:content-version-strategy patterns="/**"/>
        <!-- <mvc:fixed-version-strategy version="1.1.1" patterns="/**"/> -->
      </mvc:version-resolver>
    </mvc:resolvers>
  </mvc:resource-chain>
</mvc:resources>
```

### 3 修改 `view`

修改相应的 `JSP`：

```html
<!-- css -->
<link rel="stylesheet" href="<c:url value="/static/vendors/bootstrap/css/bootstrap.min.css"/>">

<!-- javascript -->
<script src="<c:url value="/static/vendors/bootstrap/js/bootstrap.min.js"/>"></script>

```

## 参考

1. [handling static web resources](https://spring.io/blog/2014/07/24/spring-framework-4-1-handling-static-web-resources)
2. [Spring Web MVC](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc)
3. [Cachable Static Assets with Spring MVC](http://www.baeldung.com/cachable-static-assets-with-spring-mvc)
4. [Preventing caching of static resources in Spring MVC legacy projects using XML config](http://sunitkatkar.blogspot.com/2016/04/preventing-caching-of-static-resources.html)

