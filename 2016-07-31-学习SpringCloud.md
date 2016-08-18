本文来自视频教程

+ [Building Microservices with Spring Boot LiveLessons (Video Training)](https://www.safaribooksonline.com/library/view/building-microservices-with/9780134192468/)
+ [GitHub代码](https://github.com/livelessons-spring/building-microservices)
+ [Spring Boot Reference Guide](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#getting-started-installing-the-cli)
+ [Spring Boot Reference Guide-中文gitbook](https://qbgbook.gitbooks.io/spring-boot-reference-guide-zh/content/)

+ [microservices-demo-github](https://github.com/paulc4/microservices-demo)

+ https://spring.io/blog/2015/07/14/microservices-with-spring
+ http://www.infoq.com/cn/articles/microframeworks1-spring-boot
+ http://techblog.netflix.com/
+ http://blog.loli.io/2016/02/21/spring-cloud-netflix-01/


+ [Spring Cloud Netflix](http://www.xyuu.cn/spring-cloud-netflix-zhcn.html)
+ [知乎翻译spring cloud文档](https://zhuanlan.zhihu.com/p/21778490)
    
# Lesson 1: Bootstrapping (Spring Boot 101)

**安装springcli**

```
brew tap pivotal/tap ; brew install springboot
```

## 建立一个Groovy-Base的App

新建一个example.groovy

```
@RestController
public class Example {

	@RequestMapping("/")
	String hello() {
		return "Hello World!"
	}

}
```

执行启动:

```
spring run example.groovy

# 如果你想生成一个jar包
spring jar example.jar example.groovy
java -jar example.jar
```

打开浏览器 [http://localhost:8080/](http://localhost:8080/) 

```
Hello World!
```

## 使用start.spring.io 生成工程

进去网站按需要添加依赖点击下载即可。http://start.spring.io/ 下载demo.tar，解压后是一个完整的maven工程

```
➜  d1 ls
mvnw     mvnw.cmd pom.xml  src      target
```

添加代码

```
package com.example;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class ExampleController {

    @RequestMapping("/")
    public String hello () {
        return "Hello Java World!";
    }
}

```

打包

```
mvn clean package
```

运行

```
mvn spring-boot:run

或者基于软件如LVS
java -jar target/demo-0.0.1-SNAPSHOT.jar
```
