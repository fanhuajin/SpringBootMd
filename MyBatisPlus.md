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
    private Long id;
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

### 三、插入测试及雪花算法

> 插入测试

```java
//测试插入
@Test
 void testInsert(){
    User user = new User();
    user.setName("fanhuajin");
    user.setAge(3);
    user.setEmail("78235129@qq.com");
    int result = userMapper.insert(user);//帮我们自动生成id
    System.out.println(result);//受影响的行数
    System.out.println(user);//发现id会自动回填
}
```

> 数据库插入的id的默认值为：全局的唯一id

**主键生成策略**

> **雪花算法**SnowFlake   

* ID_WORKER 全局唯一id     @TableId(type = IdType.ID_WORKER)
  * 第一个部分，是 1 个 bit：0，这个是无意义的。
  * 第二个部分是 41 个 bit：表示的是时间戳。
  * 第三个部分是 5 个 bit：表示的是机房 id，10001。
  * 第四个部分是 5 个 bit：表示的是机器 id，1 1001。
  * 第五个部分是 12 个 bit：表示的序号，就是某个机房某台机器上这一毫秒内同时生成的 id 的序号，0000 00000000。



> 主键自增 AUTO

* AUTO  @TableId(type = IdType.AUTO)
* 需要将自动主键字段设置为自增才行

> 其余的源码解释

```java
AUTO(0),//数据库id自增
NONE(1),//未设置主键
INPUT(2),//手动输入
ID_WORKER(3),//默认的全局 唯一id
UUID(4),//全局唯一id uuid
ID_WORKER_STR(5);//ID_WORKER 字符串表示法
```

### 四、更新测试及自动填充

```java
//测试更新
@Test
void restUpdate(){
    User user = new User();
    //通过条件自动拼接动态sql
    
    user.setId(2L);
    user.setName("fanhuajin");
    
    //注意：udpdteById 但是参数是一个 对象
    int i = userMapper.updateById(user);
    System.out.println(i);
}
```

> 自动填充

创建时间、修改时间！这些操作都是自动完成的，我们不希望手动更新！

阿里巴巴开发手册：所有的数据库表：gmt_create、gmt_modified几乎所有的表都要配置上。而且需要自动化。

* 代码级别

  * 在数据库中添加字段create_time 、update_time

* 在测试插入方法，我们需要先把实体类同步

  ```java
  @TableField(value = "createTime",fill = FieldFill.INSERT)
  private Date createTime;
  
  @TableField(value = "updateeTime",fill = FieldFill.INSERT_UPDATE)
  private Date updateeTime;
  ```

* 编写处理器处理注解

  ```java
  package com.fan.handler;
  
  import com.baomidou.mybatisplus.core.handlers.MetaObjectHandler;
  import lombok.extern.slf4j.Slf4j;
  import org.apache.ibatis.reflection.MetaObject;
  import org.springframework.stereotype.Component;
  
  import java.util.Date;
  @Slf4j
  @Component//一定不要忘记把处理器加到IOC容器中！
  public class MyMetaObjectHandler implements MetaObjectHandler {
      //插入时填充策略
      @Override
      public void insertFill(MetaObject metaObject) {
          log.info("start insert fill...");
          this.setFieldValByName("createTime",new Date(),metaObject);
          this.setFieldValByName("updateTime",new Date(),metaObject);
      }
      //更新时填充策略
      @Override
      public void updateFill(MetaObject metaObject) {
          log.info("start update fill...");
          this.setFieldValByName("updateTime",new Date(),metaObject);
      }
  }
  ```

* 测试插入

* 测试更新

### 五、乐观锁

> 乐观锁：顾名思义十分乐观，它总是认为不会出现问题，无论干什么都不去上锁！如果出现问题，再次跟新值测试！

> 悲观锁：顾名思义十分悲观，它总是认为会出现问题，无论干什么都去上锁！再去操作！

* 乐观锁
  * 取出记录时，获取当前version
  * 更新时，带上这个version
  * 执行更新时， set version = yourVersion+1 where version = yourVersion
  * 如果version不对，就更新失败

> 测试一个乐观锁

* 个数据库中增加version字段 并让其值默认为1

* 我们实体类加上version字段

  ```java
  @Version//乐观锁Version注解
  private Integer version;
  ```

* 注册组件**（将主启动类中的MapperScan注解放到该文件夹下）**

  ```java
  package com.fan.config;
  
  import com.baomidou.mybatisplus.extension.plugins.OptimisticLockerInterceptor;
  import org.mybatis.spring.annotation.MapperScan;
  import org.springframework.context.annotation.Bean;
  import org.springframework.context.annotation.Configuration;
  import org.springframework.transaction.annotation.EnableTransactionManagement;
  
  //将主启动类中的MapperScan注解放到该文件夹下
  //扫描我们的mapper 文件夹
  @MapperScan("com.fan.mapper")
  @EnableTransactionManagement //事务操作注解
  @Configuration//配置类
  public class MyBatisPlusConfig {
      //注册乐观锁插件
      @Bean
      public OptimisticLockerInterceptor optimisticLockerInterceptor() {
          return new OptimisticLockerInterceptor();
      }
  
  }
  
  
  ```

* 测试乐观锁

  ```java
  //测试乐观锁成功
  @Test
  void testOptimisticLocker(){
      //1.查询用户信息
      User user = userMapper.selectById(1L);
      //2.修改用户信息
      user.setName("fanhuajin");
      user.setEmail("78235129@qq.co");
      //3.执行跟新操作
      userMapper.updateById(user);
  }
  
  //测试乐观锁失败
  @Test
  void testOptimisticLocker2(){
      //1.线程1
      User user = userMapper.selectById(1L);
      user.setName("fanhuajin");
      user.setEmail("78235129@qq.co");
  
      //模拟另外一个线程执行插队操作
      User user2 = userMapper.selectById(1L);
      user2.setName("fanhuajin2222222");
      user2.setEmail("78235129@qq.co");
      userMapper.updateById(user2);
  
      userMapper.updateById(user);//如果没有乐观锁就会覆盖插队线程的值
  }
  ```

### 六、查询测试

```java
//测试查询
@Test
void testSelectById(){
    User user = userMapper.selectById(1L);
    System.out.println(user);
}
//测试批量查询
@Test
void testSelectByBatchId(){
    List<User> users = userMapper.selectBatchIds(Arrays.asList(1, 2, 3));
    for (User user : users) {
        System.out.println(user);
    }
}

//条件查询 map
@Test
void testSelectByBatchIds(){
    HashMap<String, Object> map = new HashMap<>();
    //自定义要查询的条件
    map.put("name","fanhuajin");
    List<User> users = userMapper.selectByMap(map);
    for (User user : users) {
        System.out.println(user);
    }
}
```

### 七、分页查询

* 配置拦截器组件

  ```java
  //在 MyBatisPlusConfig 类中导入分页插件
  @Bean
  public PaginationInterceptor paginationInterceptor() {
      return new PaginationInterceptor();
  }
  ```

* 直接使用Page对象即可

  ```java
  //测试分页查询
  @Test
  void testPage(){
      //参数一：当前页 1
      //参数二：页面大小 5
      Page<User> page = new Page<>(1,5);
      userMapper.selectPage(page,null);
      List<User> records = page.getRecords();
      System.out.println(page.getTotal());//数据总数
      for (User record : records) {
          System.out.println(record);
      }
  }
  ```

### 八、删除测试

```java
//测试删除
@Test
void testDeleteById(){
int i = userMapper.deleteById(1L);
System.out.println(1);
}

//测试批量删除
@Test
void testDeleteBatchId(){
int i = userMapper.deleteBatchIds(Arrays.asList(1L, 1L));
System.out.println(i);
}
//通过map删除
@Test
void testDeleteMap(){
    HashMap<String, Object> map = new HashMap<>();
    //自定义要查询的条件
    map.put("name","fanhuajin");
    int i = userMapper.deleteByMap(map);
}
```

### 九、逻辑删除

> 物理删除：从数据库中直接移除
>
> 逻辑删除：在数据库中没有被移除，而是通过一个变量来让他失效！deleted=0 --》 deleted=1

管理员可以查看被删除的记录！防止数据的丢失，类似于回收站！

测试一下：

* 在数据表中增加一个deleted字段 并让其值默认为0

* 实体类中增加属性

  ```java
  @TableLogic //逻辑删除
  private Integer deleted;
  ```

* 配置逻辑删除组件

  ```java
  //在 MyBatisPlusConfig 类中导入逻辑删除插件
  @Bean
  public ISqlInjector sqlInjector(){
       return new LogicSqlInjector();
  }
  ```

* 配置application.yaml

  ```yaml
  #开启mybatis-plus日志 和配置逻辑删除
  mybatis-plus:
    configuration:
      log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
    global-config:
      db-config:
        logic-delete-value: 1
        logic-not-delete-value: 0
  ```

* 测试删除

### 十、性能分析插件

* 配置逻辑删除组件

  ```java
  /**
   * SQL执行效率插件
   */
  @Bean
  @Profile({"dev","test"})// 设置 dev test 环境开启
  public PerformanceInterceptor performanceInterceptor() {
      PerformanceInterceptor performanceInterceptor = new PerformanceInterceptor();
      //在工作中，不允许用户等待
      performanceInterceptor.setMaxTime(100);//设置sql执行的最大时间默认为毫秒，如果超过了则不执行
      performanceInterceptor.setFormat(true);
      return performanceInterceptor;
  }
  ```

  

* 配置application.yaml

  ```yaml
  #设置开发环境
  spring: 
  	profiles:
      	active: dev
  ```

* 测试删除

### 十一、条件构造器

```java
//条件查询器
@Test
void testwrapper(){
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    wrapper.between("age",20,30);
    List<Map<String, Object>> maps = userMapper.selectMaps(wrapper);
    for (Map<String, Object> map : maps) {
        System.out.println(map);
    }
}
```

