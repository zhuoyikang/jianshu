本文是[Getting Started Guides](https://spring.io/guides)翻译系列

[Scheduling Tasks](https://spring.io/guides/gs/scheduling-tasks/)

生成一个名字为schedule的默认项目:http://start.spring.io/


新建ScheduledTasks.java

```
package com.example;

import java.text.SimpleDateFormat;
import java.util.Date;

import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

@Component
public class ScheduledTasks {

    private static final SimpleDateFormat dateFormat = new SimpleDateFormat("HH:mm:ss");

    @Scheduled(fixedRate = 5000)
    public void reportCurrentTime() {
        System.out.println("The time is now " + dateFormat.format(new Date()));
    }
}

```

Enable Scheduling


```

package com.example;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

// 添加
import org.springframework.scheduling.annotation.EnableScheduling;

@SpringBootApplication
@EnableScheduling //添加这一行
public class ScheduleApplication {

	public static void main(String[] args) {
		SpringApplication.run(ScheduleApplication.class, args);
	}
}
```



启动

```
mvn clean package
java -jar target/schedule-0.0.1-SNAPSHOT.jar
```
