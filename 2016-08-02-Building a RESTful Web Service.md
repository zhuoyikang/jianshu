本文是[Getting Started Guides](https://spring.io/guides)翻译系列

[Getting Started Guides-Building a RESTful Web Service](https://spring.io/guides/gs/rest-service/)

# What you’ll build

-------------------------------------------------------------------------------

效果：

```

# You’ll build a service that will accept HTTP GET requests at:

http://localhost:8080/greeting

# and respond with a JSON representation of a greeting:

{"id":1,"content":"Hello, World!"}

# You can customize the greeting with an optional name parameter in the query string:

http://localhost:8080/greeting?name=User

# The name parameter value overrides the default value of "World" and is reflected in the response:

{"id":1,"content":"Hello, User!"}

```

# 你需要些啥

-------------------------------------------------------------------------------

+ About 15 minutes
+ A favorite text editor or IDE
+ JDK 1.8 or later
+ Gradle 2.3+ or Maven 3.0+
+ You can also import the code from this guide as well as view the web page directly into Spring Tool Suite (STS) and work your way through it from there.

生成一个模板项目：start.spring.io，直接生成一个默认就可以，取名字为gs。

修改pom.xml 

```

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.jayway.jsonpath</groupId>
            <artifactId>json-path</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

```


新建一个Greeting.java

```
package com.example;

public class Greeting {

    private final long id;
    private final String content;

    public Greeting(long id, String content) {
        this.id = id;
        this.content = content;
    }

    public long getId() {
        return id;
    }

    public String getContent() {
        return content;
    }
}

```

新建一个GreetingController.java

```
package com.example;

import java.util.concurrent.atomic.AtomicLong;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class GreetingController {

    private static final String template = "Hello, %s!";
    private final AtomicLong counter = new AtomicLong();

    @RequestMapping("/greeting")
    public Greeting greeting(@RequestParam(value="name", defaultValue="World") String name) {
        return new Greeting(counter.incrementAndGet(),
                            String.format(template, name));
    }
}

```



开始跑

```
mvn clean package
java -jar target/gs-0.0.1-SNAPSHOT.jar

```

打开浏览器:

```
http://127.0.0.1:8080/greeting?name=fefe
```










