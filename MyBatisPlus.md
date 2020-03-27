### 一、快速入门

> 1.创建数据库

```mysql
create database if not exists mybatis_plus character set utf8 collate utf8_general_ci
```

> 2.创建user表

```mysql
DROP TABLE IF EXISTS user;

CREATE TABLE user
(
	id BIGINT(20) NOT NULL COMMENT '主键ID',
	name VARCHAR(30) NULL DEFAULT NULL COMMENT '姓名',
	age INT(11) NULL DEFAULT NULL COMMENT '年龄',
	email VARCHAR(50) NULL DEFAULT NULL COMMENT '邮箱',
	PRIMARY KEY (id)
);
-- 真是开发中，version（乐观锁）、deleted（逻辑删除）、gmt_create\gmt_modified
INSERT INTO user (id, name, age, email) VALUES
(1, 'Jone', 18, 'test1@baomidou.com'),
(2, 'Jack', 20, 'test2@baomidou.com'),
(3, 'Tom', 28, 'test3@baomidou.com'),
(4, 'Sandy', 21, 'test4@baomidou.com'),
(5, 'Billie', 24, 'test5@baomidou.com');
```

> 3.初始化项目

> 4.导入依赖

```xml
<!--mybitis_plus-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.0.5</version>
</dependency>
 <!--druid -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.21</version>
</dependency>
<!-- log4j -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>

```

说明：我们使用mybatis-plus可以节省我们大量的代码，尽量不要同时导入mybatis和mybatis-plus

> 5.连接数据库在application.yaml中配置文件

```java
spring:
  datasource:
    username: root
    password: root
    url: jdbc:mysql://localhost:3306/mybatis_plus?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8
    driver-class-name: com.mysql.jdbc.Driver
    type: com.alibaba.druid.pool.DruidDataSource

    #Spring Boot 默认是不注入这些属性值的，需要自己绑定
    #druid数据源令有配量
    initialSize: 5
    minIdle: 5
    maxActive: 20
    maxWait: 60000
    timeBetweenEvictionRunsMillis: 60000
    minEvictableIdleTimeMillis: 300000
    validationQuery: SELECT 1 FROM DUAL
    testWhileIdle: true
    testOnBorrow: false
    testOnReturn: false
    poolPreparedStatements: true
    #配置监控统计拦藏的filters, stat: 监控统计、log4j: 日志记录、wall: 防御sqL注入
    #如果允许时报错java. lang. ClassNotFoundException: org. apache. log4j. Priority
    #则导入Log4j 依赖即可，Maven地址: https://mvnrepository. com/artifact/log4j/log4j
    filters: stat,wall,log4j
    maxPoolPreparedstatementPerConnectionSize: 20
    useGlobalDataSourceStat: true
    connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500


```

> 6.传统方式pojo-dao(连接mybatis，配置mapper.xml文件)-service-controller

6.使用mybatis-plus之后

pojo

```java
package com.fan.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private long id;
    private String name;
    private Integer age;
    private String email;
}

```

mapper接口

```java
package com.fan.mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.fan.pojo.User;
import org.springframework.stereotype.Repository;

//在对应的Mapper上面继承基本的类BaseMapper
@Repository
public interface UserMapper extends BaseMapper<User> {
    //所有的crcd已经编写完成了
    //你不需要像以前一样配置文件
}

```

注意点,我们需要在主启动类上去扫描我们mapper包下的所有接口

```java
@MapperScan("com.fan.mapper")
```

测试类中测试

```java
 //继承了BaseMapper，所有的方法，我们也可以编写自己的扩展方法
   @Autowired
    private UserMapper userMapper;

    @Test
    void contextLoads() {
        List<User> users = userMapper.selectList(null);
        for (User user : users) {
            System.out.println(user);
        }
    }
```

### 二、配置日志

```xml
#开启mybatis-plus日志
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

