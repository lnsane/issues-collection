## 关于SpringBoot 和 JSP的整合问题

### 来看一看官网的说明

> Template Engines As well as REST web services, you can also use Spring MVC to serve dynamic HTML content. Spring MVC supports a variety of templating technologies, including Thymeleaf, FreeMarker, and JSPs. Also, many other templating engines include their own Spring MVC integrations.

> Spring Boot includes auto-configuration support for the following templating engines:

- [FreeMarker](https://freemarker.apache.org/docs/) (这是springboot 官方推荐)
- [Groovy](https://docs.groovy-lang.org/docs/next/html/documentation/template-engines.html#_the_markuptemplateengine)
- [Thymeleaf](https://www.thymeleaf.org/) (开源社区大量使用这个)
- [Mustache](https://mustache.github.io/)

### 为什么没有JSP呢？

> If possible, JSPs should be avoided. There are several known limitations, when using them with embedded servlet containers.


> JSP Limitations
> When running a Spring Boot application that uses an embedded servlet container (and is packaged as an executable archive), there are some limitations in the JSP support.

- With Jetty and Tomcat, it should work if you use war packaging. An executable war will work when launched with java
  -jar, and will also be deployable to any standard container. JSPs are not supported when using an executable jar.

- Undertow does not support JSPs.

- Creating a custom error.jsp page does not override the default view for error handling. Custom error pages should be
  used instead.

综合上面的描述呢！我们有了一个结论：

1. JSP 已经不在是SpringBoot自动装配的组件了
2. JSP 在SpringBoot开发中有限制,这个限制是历史的遗留原因,大家都知道Servlet Web开发一直是JSP的形式的

## What should we do ?

1. 修改模块的Pom.xml 文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <packaging>war</packaging>
    <dependencies>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.tomcat.embed</groupId>
            <artifactId>tomcat-embed-jasper</artifactId>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
        </dependency>
    </dependencies>
</project>

```

2. 增加src/webapp/jsp

3. 修改yml文件

```yaml
  mvc:
    view:
      prefix: /jsp/
      suffix: .jsp
```