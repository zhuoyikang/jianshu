本文是[Getting Started Guides](https://spring.io/guides)翻译系列

[Centralized Configuration](https://spring.io/guides/gs/centralized-configuration/)

# 生成配置服务器

-------------------------------------------------------------------------------

生成一个名字为confserver的项目:http://start.spring.io/ ，需要加入confserver的依赖


ConfserverApplication.java

```
package com.example;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

// add
import org.springframework.cloud.config.server.EnableConfigServer;

// add
@EnableConfigServer
@SpringBootApplication
public class ConfserverApplication {

	public static void main(String[] args) {
		SpringApplication.run(ConfserverApplication.class, args);
	}
}

```

application.properties

```
server.port=8888
spring.cloud.config.server.git.uri= https://github.com/indie21/demo-conf-repo.git
```

启动

```
mvn clean package
java -jar target/confserver-0.0.1-SNAPSHOT.jar

http://localhost:8888/xx/master
```

 Resources

| Path             | Description  |
|------------------|--------------|
| /{app}/{profile} | Configuration data for app in Spring profile (comma-separated).|
| /{app}/{profile}/{label} | Add a git label |
| /{app}/{profiels}{label}/{path} | An environment-specific plain text config file (at "path") |


例子:

+ {app} 匹配客户端的 “spring.application.name”
+ {profile} 匹配客户端的”spring.active.profiles”
+ {label} 如果是svn匹配trunk/branchs等.

```
启动后打开浏览器 http://localhost:8888/xx/master
大约是这样的格式 [ip:address]/[filename]/[branch]

{
name: "xx",
profiles: [
"master"
],
label: "master",
version: "0dfc317e05b2fbd004e72fcdad3f229c14882999",
propertySources: [
{
name: "https://github.com/indie21/demo-conf-repo.git/xx.properties",
source: {
server.port: "8888",
spring.cloud.config.server.git.uri: "https://github.com/indie21/demo-conf-repo.git"
}
}
]
}

```

# 配置仓库

-------------------------------------------------------------------------------

新建 confclient.properties

```
message=fwefews
server.port=8802
spring.server.port=8802
```


# 生成配置客户端

-------------------------------------------------------------------------------

生成一个名字为confclientx的项目:http://start.spring.io/ ，需要加入confclient的依赖

修改配置 `bootstrap.properties`

```
spring.application.name=confclient
spring.cloud.config.uri=http://localhost:8888
```

pom:

```

    <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-config-client</artifactId>
    </dependency>



	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>Brixton.BUILD-SNAPSHOT</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>

```

代码

```
package com.example;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import java.util.concurrent.atomic.AtomicLong;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.cloud.context.config.annotation.RefreshScope;
import org.springframework.web.bind.annotation.RequestMapping;


@SpringBootApplication
public class ConfclientxApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConfclientxApplication.class, args);
    }
}

@RestController
class GreetingController {

    @RequestMapping("/greeting")
    public String greeting( ) {
        return "good";
    }
}


@RefreshScope
@RestController
class MessageRestController {

    @Value("${message}")
    private String message;

    @RequestMapping("/message")
    String getMessage() {
        return this.message;
    }
}

```

打开客户端:

```
http://localhost:8802/message
fwefews
```



添加配置:


```

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator </artifactId>
		</dependency>

```

可以得到：http://localhost:8802/refresh 刷新配置


# 行为记录

-------------------------------------------------------------------------------


客户端启动后会到配置服务器默认获取配置:

```
http://localhost:8801/confclient/default
```

每一次都是实时获取的git仓库中的数据，confserver会自动同步，因此你会感觉到这个获取过程较慢。
