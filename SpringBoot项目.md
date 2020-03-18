### springboot可返回数据接口

#### 1.导包

在pom.xml中导包

```java
<!--Lombok-->
    <!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
    <dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.12</version>
    <scope>provided</scope>
</dependency>

<!--fastjson-->
<!-- https://mvnrepository.com/artifact/com.alibaba/fastjson -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.66</version>
</dependency>
```

#### 2.解决跨域问题

java下的com.fan下创建config目录并创建CorsFilter类解决跨域问题

```java
package com.fan.config;

import org.springframework.stereotype.Component;

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * 跨域的配置，必须这么做，原因是，shiro 的拦截器优先级在 CorsFilter 之前
 */
@Component
public class CorsFilter implements Filter {

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletResponse response = (HttpServletResponse) servletResponse;
        HttpServletRequest request = (HttpServletRequest) servletRequest;

        response.setHeader("Access-Control-Allow-Origin", request.getHeader("Origin"));
        response.setHeader("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept, If-Modified-Since, x-token, token");
        response.setHeader("Access-Control-Allow-Methods", "POST, GET, OPTIONS, DELETE, PUT");
        response.setHeader("Access-Control-Max-Age", "3600");
        response.addHeader("Access-Control-Allow-Credentials", "true");

        filterChain.doFilter(request, response);
    }

}

```

#### 3.创建数据返回格式类

创建数据返回格式类ResultSets，前端需要什么给什么

```java
package com.fan.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
@Data
@NoArgsConstructor
@AllArgsConstructor
public class ResultSets {
    private String code;
    private Object data;
}
```

#### 4.创建一个学生类

普通的学生类Student	

```java
package com.fan.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class Student {
    private String name;
    private int age;
}

```

#### 5.创建一个controller层

在com.fan下创建一个controller目录并创建一个StudentControll类

```java
package com.fan.controller;

import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONArray;
import com.fan.pojo.ResultSets;
import com.fan.pojo.Student;
import org.springframework.web.bind.annotation.*;

import java.util.ArrayList;
import java.util.List;

@RestController
public class StudentControll {
    @GetMapping("/hello")
    public ResultSets test() {
        List<Student> stulist = new ArrayList<>();
        for (int i = 0; i < 5; i++) {
            stulist.add(new Student("student" + i, i));
        }
        ResultSets resultSets = new ResultSets();
        resultSets.setCode("100");
        resultSets.setData(jsonArrays);
        return resultSets;
    }
}

```

### SpringBoot整合Mybatis

#### 1.创建SpringBoot项目

选择项目依赖

* Developer Tools
  * Spring Boot DevTools
  * Lombok
* Web
  * Spring Web
* SQL
  * JDBC API
  * MySQL Server Driver

#### 2.查看Maven仓库

检查Maven仓库是否为阿里仓库

Settings.. --> Build,Execution,Deployment-->Build Tools -->maven

#### 3.删除多余的文件

删除多余文件保持项目干净

- 删除：

  - .mvn
  - .gitignore 
  - HELP.md 
  - mvnw
  - mvnw.cmd

  然后点击右下角的 Enable Auto-Import

#### 4.连接数据库

在idea中连接上数据库

创建文件文件application.yaml

src-->main-->resources-->application.yaml

```java
spring:
  datasource:
    username: root
    password: root
    url: jdbc:mysql://localhost:3306/test?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8
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

在pom.xml中导入依赖

导入druid 和mybatis依赖

```java
 <!-- https://mvnrepository.com/artifact/com.alibaba/druid -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.21</version>
</dependency>

<!-- https://mvnrepository.com/artifact/org.mybatis.spring.boot/mybatis-spring-boot-starter -->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.2</version>
</dependency>

```

#### 5.创建一个用户类pojo

创建目录pojo

在src-->main-->java-->com-->fan-->pojo

创建User类

```java
package com.fan.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private int id;
    private String name;
    private String pwd;
}

```

#### 6.创建一个接口类mapper

创建目录mapper

在src-->main-->java-->com-->fan-->mapper

创建UserMapper类

* @Mapper可以直接在类中使用
* 也可以在项目启动文件中加@MapperScan("com.fan.mapper") 而不需要@Mapper

```java
package com.fan.mapper;

import com.fan.pojo.User;
import org.apache.ibatis.annotations.Mapper;
import org.springframework.stereotype.Repository;

import java.util.List;

@Mapper
@Repository
public interface UserMapper {

    //获取所有用户
    List<User> queryUserList();

    //根据id获取用户
    User queryUserById(int id);

    //新增一个用户
    int addUser(User user);

    //修改一个用户
    int updateUser(User user);

    //删除一个用户
    int deleteUser(int id);

}

```

#### 7.创建mapper.xml

创建mybatis和mapper文件夹

创建UserMapper.xml文件

resources-->mybatis-->mapper-->UserMapper.xml

```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.fan.mapper.UserMapper">
    <select id="queryUserList" resultType="User">
        select *from user
    </select>
    <select id="queryUserById" resultType="User">
        select *from user where id=#{id}
    </select>
    <insert id="addUser" parameterType="User">
        insert into user (id,name,pwd) value (#{id},#{name},#{pwd})
    </insert>
    <update id="updateUser" parameterType="User">
        update user set name=#{name},pwd=#{pwd} where id=#{id}
    </update>
    <delete id="deleteUser" parameterType="int">
        delete from user where id=#{id}
    </delete>

</mapper>
```

在application.yaml文件下整合mybatis

在文件下加入以下内容

```java
mybatis:
  type-aliases-package: com.fan.pojo
  mapper-locations: classpath:mybatis/mapper/*.xml
```

#### 8.创建Service层

创建目录Service

在src-->main-->java-->com-->fan-->Service

创建UserService类

```java
package com.fan.service;

import com.fan.pojo.User;

import java.util.List;

public interface UserService {
    //获取所有用户
    List<User> queryUserList();

    //根据id获取用户
    User queryUserById(int id);

    //新增一个用户
    int addUser(User user);

    //修改一个用户
    int updateUser(User user);

    //删除一个用户
    int deleteUser(int id);

}

```

创建目录ServiceImpl

在src-->main-->java-->com-->fan-->Service-->ServiceImpl

创建ServiceImpl类

```java
package com.fan.service.Impl;

import com.fan.mapper.UserMapper;
import com.fan.pojo.User;
import com.fan.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class UserServiceImpl implements UserService {
    @Autowired
    private UserMapper userMapper;

    @Override
    public List<User> queryUserList() {
        List<User> userList = userMapper.queryUserList();
        return userList;
    }

    @Override
    public User queryUserById(int id) {
        User user = userMapper.queryUserById(id);
        return user;
    }

    @Override
    public int addUser(User user) {
        int i = userMapper.addUser(user);
        return i;
    }

    @Override
    public int updateUser(User user) {
        int i = userMapper.updateUser(user);
        return i;
    }

    @Override
    public int deleteUser(int id) {
        int i = userMapper.deleteUser(3);
        return i;
    }
}

```

#### 9.创建控制层controller

创建目录controller

在src-->main-->java-->com-->fan-->controller

创建UserController类

```java
package com.fan.controller;

import com.fan.pojo.User;
import com.fan.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

@RestController
public class UserController {
    @Autowired
    private UserService userService;

    @GetMapping("/queryUserList")
    public List<User> queryUserList() {
        List<User> users = userService.queryUserList();
        return users;
    }
    @GetMapping("/queryUserById")
    public User queryUserById() {
        User user = userService.queryUserById(3);
        return user;
    }

    @GetMapping("/addUser")
    public String addUser() {
        int i = userService.addUser(new User(3, "马儿扎哈222", "123456"));
        return "ok";
    }

    @GetMapping("/updateUser")
    public String updateUser() {
        int i = userService.updateUser(new User(3, "马儿扎哈", "123456"));
        return "ok";
    }

    @GetMapping("/deleteUser")
    public String deleteUser() {
        int i = userService.deleteUser(3);
        return "ok";
    }
}

```

#### 10.后台监控配置Druid

导包

```java
<!-- https://mvnrepository.com/artifact/log4j/log4j -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>

```

创建目录config

在src-->main-->java-->com-->fan-->config

创建DruidConfig类

```java
package com.fan.config;

import com.alibaba.druid.pool.DruidDataSource;
import com.alibaba.druid.support.http.StatViewServlet;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.web.servlet.ServletRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;
import java.util.HashMap;

@Configuration
public class DruidConfig {
    @ConfigurationProperties(prefix = "spring.datasource")
    @Bean
    public DataSource druid(){
        return  new DruidDataSource();
    }

    //配置Druid的监控
    //1、配置一个管理后台的Servlet
    @Bean
    public ServletRegistrationBean statViewServlet(){
        ServletRegistrationBean bean = new ServletRegistrationBean(new StatViewServlet(), "/druid/*");
        HashMap<String,String> initParams = new HashMap<>();
        //登陆配置
        initParams.put("loginUsername","admin"); //固定的key  loginUsername loginPassword
        initParams.put("loginPassword","123456");

        initParams.put("allow","");//默认就是允许所有访问
        initParams.put("fanhuajin","192.168.15.21");//禁止谁登陆

        bean.setInitParameters(initParams);
        return bean;
    }

}
//登陆后台管理页面
//http://localhost:8080/druid/login.html
```

