本文是[Getting Started Guides](https://spring.io/guides)翻译系列

[使用jdbc访问关系型数据库](https://spring.io/guides/gs/relational-data-access/)

生成一个名字为jdbc的默认项目:http://start.spring.io/


# 依赖

-------------------------------------------------------------------------------

```

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
        </dependency>
    </dependencies>

```


# 新建

-------------------------------------------------------------------------------

Customer.java

```
package com.example;

public class Customer {
    private long id;
    private String firstName, lastName;

    public Customer(long id, String firstName, String lastName) {
        this.id = id;
        this.firstName = firstName;
        this.lastName = lastName;
    }

    @Override
    public String toString() {
        return String.format("Customer[id=%d, firstName='%s', lastName='%s']",
                             id, firstName, lastName);
    }

    // getters & setters omitted for brevity
}

```


# main 

-------------------------------------------------------------------------------


```
package com.example;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.jdbc.core.JdbcTemplate;

import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

@SpringBootApplication
public class JdbcApplication implements CommandLineRunner {

    private static final Logger log = LoggerFactory.getLogger(JdbcApplication.class);

    public static void main(String args[]) {
        SpringApplication.run(JdbcApplication.class, args);
    }

    @Autowired
    JdbcTemplate jdbcTemplate;

    @Override
    public void run(String... strings) throws Exception {

        log.info("Creating tables");

        jdbcTemplate.execute("DROP TABLE customers IF EXISTS");
        jdbcTemplate.execute("CREATE TABLE customers(" +
                "id SERIAL, first_name VARCHAR(255), last_name VARCHAR(255))");

        // Split up the array of whole names into an array of first/last names
        List<Object[]> splitUpNames = Arrays.asList("John Woo", "Jeff Dean", "Josh Bloch", "Josh Long").stream()
                .map(name -> name.split(" "))
                .collect(Collectors.toList());

        // Use a Java 8 stream to print out each tuple of the list
        splitUpNames.forEach(name -> log.info(String.format("Inserting customer record for %s %s", name[0], name[1])));

        // Uses JdbcTemplate's batchUpdate operation to bulk load data
        jdbcTemplate.batchUpdate("INSERT INTO customers(first_name, last_name) VALUES (?,?)", splitUpNames);

        log.info("Querying for customer records where first_name = 'Josh':");
        jdbcTemplate.query(
                "SELECT id, first_name, last_name FROM customers WHERE first_name = ?", new Object[] { "Josh" },
                (rs, rowNum) -> new Customer(rs.getLong("id"), rs.getString("first_name"), rs.getString("last_name"))
        ).forEach(customer -> log.info(customer.toString()));
    }
}

```

由于默认使用的是H2，如果你需要使用mysql需要做一下修改：


```
    <!-- <dependency> -->
    <!--   <groupId>mysql</groupId> -->
    <!--   <artifactId>mysql-connector-java</artifactId> -->
    <!-- </dependency> -->

    <!-- <dependency> -->
    <!--   <groupId>org.springframework.boot</groupId> -->
    <!--   <artifactId>spring-boot-starter-jdbc</artifactId> -->
    <!-- </dependency> -->

   # 注释下面的.

    <dependency>
      <groupId>com.h2database</groupId>
      <artifactId>h2</artifactId>
    </dependency>

```

修改application.properties

```
spring.datasource.url=jdbc:mysql://127.0.0.1/test
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.driver-class-name=com.mysql.jdbc.Driver

```

同时H2的语法和Mysql的语法不兼容，如果你想换成Mysql，需要改改语法。



