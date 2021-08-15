# Spring Data🌈

Spring Data 是 Spring 的一个子项目，用于简化数据库访问，支持 NoSL 和关系数据存储，其主要目标是使数据库的访问变得方便快捷。

## 参考文档

👉  [Spring Data 官方合集](https://spring.io/projects/spring-data)



# Spring Data Repositories

## 核心概念

`Repository` 接口，用于帮助发现扩展此接口的接口。

 `CrudRepository`  接口为实体类提供了 CRUD 相关的功能

`PagingAndSortingRepository`  接口提供了一些方法，简化对实体的分页访问

<img src="http://store.secretcamp.cn/uPic/image-20210602094525317202106020945251622598325nbwfZdnbwfZd.png" alt="image-20210602094525317" style="zoom:50%;" />



## 查询流程

1. 定义一个接口，继承 `Repository` 或者它的子类 ，并用实体类作为泛型

   ```java
   public interface AccountRepository extends ElasticsearchRepository<Account, String> {
   		// ...
   }
   ```



2. 定义查询方法

   ```java
   public interface AccountRepository extends ElasticsearchRepository<Account, String> {
     
       List<Account> findByAge(int age);
     
   }
   ```

   



3. 开启对 repository 的支持，这会在运行时为之前创建的接口生成代理对象

   Springboot 自动配置中默认开启，不需要再另行配置，如果要配置，和 `@Configuration` 配合使用。

   ```java
   @EnableElasticsearchRepositories(basePackages = "kzq.yorua.es")
   @Configuration
   public class RestClientConfig {
   		// ...
   }
   ```



4. 使用存储对象实例

   ```java
   @RunWith(SpringRunner.class)
   @SpringBootTest(classes = ElasticSearchDemoApplication.class)
   public class RepositoryTest {
   
       @Autowired
       AccountRepository repository;
   
       @Test
       public void testRepo() {
           List<Account> accounts = repository.findByAge(30);
           for (Account account : accounts) {
               System.out.println(account);
           }
       }
   }
   ```



## 查询机制

### 查询策略

- `CREATE`：根据方法名构建查询，通过移除一系列前缀并解析剩余部分
- `USE_DECLARED_QUERY`：尝试查找已声明的查询，如果找不到则抛出异常。
- `CREATE_IF_NOT_FOUND (default)`：以上两者的结合



### 查询创建

`findDistinctPeopleByLastname`

- 主语： `find...By`
- 谓语：`People` 、`Lastname`
- 描述语：在主语结构之间，`Distinct` 、`OrderBy`

可以参考对应子项目文档中 `Appendix：Repository query keywords` 部分

```java
interface PersonRepository extends Repository<Person, Long> {

  List<Person> findByEmailAddressAndLastname(EmailAddress emailAddress, String lastname);

  // Enables the distinct flag for the query
  List<Person> findDistinctPeopleByLastnameOrFirstname(String lastname, String firstname);
  List<Person> findPeopleDistinctByLastnameOrFirstname(String lastname, String firstname);

  // Enabling ignoring case for an individual property
  List<Person> findByLastnameIgnoreCase(String lastname);
  // Enabling ignoring case for all suitable properties
  List<Person> findByLastnameAndFirstnameAllIgnoreCase(String lastname, String firstname);

  // Enabling static ORDER BY for a query
  List<Person> findByLastnameOrderByFirstnameAsc(String lastname);
  List<Person> findByLastnameOrderByFirstnameDesc(String lastname);
}
```



### 属性表达式

```java
List<Person> findByAddress_ZipCode(ZipCode zipCode);
```



### 指定方法参数

方法参数则是为了确定查询语句中的参数，这里也接受一些特殊参数例如 `Pageable` 、 `Sort`

```java
Page<User> findByLastname(String lastname, Pageable pageable);

Slice<User> findByLastname(String lastname, Pageable pageable);

List<User> findByLastname(String lastname, Sort sort);

List<User> findByLastname(String lastname, Pageable pageable);
```



### 方法返回值

Repository 相关方法的返回值有以下几种类型：

- Java 普通 `Iterable` 、 `List` 、 `Set`

- Spring Data 的 `Streamable`
- 自定义 `Iterable`

所有类型可以参考官方文档的 `Appendix : Repository query return types`





## 创建Repository实例

1. xml配置

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans:beans xmlns:beans="http://www.springframework.org/schema/beans"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xmlns="http://www.springframework.org/schema/data/jpa"
     xsi:schemaLocation="http://www.springframework.org/schema/beans
       https://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/data/jpa
       https://www.springframework.org/schema/data/jpa/spring-jpa.xsd">
   
     <repositories base-package="com.acme.repositories" />
   
   </beans:beans>
   ```

   

2. 注解配置

   ```java
   @Configuration
   @EnableJpaRepositories("com.acme.repositories")
   class ApplicationConfiguration {
   
     @Bean
     EntityManagerFactory entityManagerFactory() {
       // …
     }
   }
   ```

   

3. 单独使用

   直接实例化一个 Repository 工厂对象

   ```java
   RepositoryFactorySupport factory = … // Instantiate factory here
   UserRepository repository = factory.getRepository(UserRepository.class);
   ```

   

## 自定义Repository实现

Repository 接口的默认实现是 Spring Data 创建代理对象中实现



# Spring Data Annotation🌈

## 公共注解🌟

### @Id

将模型类中的字段标记为主键



### @Param



### @Transactional



### @NoRepositoryBean

有时我们想要创建存储库接口，其唯一目标是为子存储库提供通用方法。也就是说，使用了该注解的接口不会被单独创建实例，只会作为其他接口的父接口而被使用。



### @Transient

映射忽略的字段



### @Version



## @PersistenceConstructor



## ElasticSearch🌟

### @Document

### @Field

### @Query

`@Query` 可以自定义查询语句

```java
interface BookRepository extends ElasticsearchRepository<Book, String> {
    @Query("{\"match\": {\"name\": {\"query\": \"?0\"}}}")
    Page<Book> findByName(String name,Pageable pageable);
}
```



### @Highlight

```java
interface BookRepository extends Repository<Book, String> {

    @Highlight(fields = {
        @HighlightField(name = "name"),
        @HighlightField(name = "summary")
    })
    List<SearchHit<Book>> findByNameOrSummary(String text, String summary);
}
```



### @Mapping



#### @GeoSpatialIndexed



### @EnableElasticsearchRepositories



## MongoDB🌟

### @MongoId





### @Document

把一个 Java 类声明为 mongodb 的文档，可以通过 collection 参数指定这个类对应的文档

- value：collection 的别名
- collection：与实体类相关联的文档，如果不指定，默认是第一个字符小写的实体类名



### @Field

配置 MongoDB 持久化保存文档时要使用的字段的名称。

- value：name 的别名
- name：对应 mongodb 中的字段名



### @DBRef



### @Indexed

声明当前字段需要索引



### @CompoundIndex



### @GeoSpatialIndexed



### @Query

可以在 MongoDB 存储库方法上实现查询。



### @EnableMongoRepositories

开启 MongoDB 存储库的配置



# Auditing🌈

日常开发中，经常会遇到实体有创建时间、更新时间、创建人、更新人等字段。这些字段不是核心的业务字段，但是对于跟踪数据记录却非常重要。由于重要程度不是那么高，经常会忘了赋值，导致数据库存的是过期数据。

Spring Data 的审计（Auditing）功能提供一组注解来自动填充这些非核心字段。

## 审计相关注解

- `@CreatedDate`：创建时间
- `@LastModifiedDate`：最后更新时间
- `@CreatedBy`：创建人信息
- `@LastModifiedBy`：最后更新人信息





# Spring Data - JDBC🌈

## 参考文档

👉  [Document：Springboot - SQL](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#features.sql)

👉  [Document：SpringData - Jdbc](https://docs.spring.io/spring-data/jdbc/docs/2.2.1/reference/html/#reference)

👉  [JavaDoc：SpringData - Jdbc](https://docs.spring.io/spring-data/jdbc/docs/2.2.1/api/)



## 引入依赖

```xml
<!-- Springboot -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jdbc</artifactId>
</dependency>
```



```xml
<!-- Spring -->
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-jdbc</artifactId>
</dependency>
```



## 配置数据源

```yaml
# application.yaml
spring:
  datasource:
    username: root
    password: kzqkzq
    url: jdbc:mysql://localhost:3306/common-example
    driver-class-name: com.mysql.cj.jdbc.Driver
```





## 配置数据源

### DriverManagerDataSource

`DriverManagerDataSource` 是 Spring 内置的一个数据源，但是其建立连接是只要有连接就新建一个 connection，没有连接池的作用。

```xml
<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource" >
    <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
    <property name="url" value="jdbc:mysql://localhost:3306/common-example"></property>
    <property name="username" value="root"></property>
    <property name="password" value="kzqkzq"></property>
</bean>
```



```yaml
# application.yaml
spring:
  datasource:
    username: root
    password: kzqkzq
    url: jdbc:mysql://localhost:3306/common-example
    driver-class-name: com.mysql.cj.jdbc.Driver
    # 如果不指定type，则默认为 DriverManagerDataSource
    type: org.springframework.jdbc.datasource.DriverManagerDataSource
```



### C3P0

```xml
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
    <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/common-example"></property>
    <property name="user" value="root"></property>
    <property name="password" value="kzqkzq"></property>
</bean>
```



### DBCP

```xml
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
    <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
    <property name="url" value="jdbc:mysql://localhost:3306/common-example"></property>
    <property name="username" value="root"></property>
    <property name="password" value="kzqkzq"></property>
</bean>
```



### Driud

```xml
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="com.mysql.jdbc.Driver" />
    <property name="url" value="jdbc:mysql://localhost:3306/common-example" />
    <property name="username" value="root" />
    <property name="password" value="kzqzkq" />
</bean>
```



```yaml
# application.yaml
spring:
  datasource:
    username: root
    password: kzqkzq
    url: jdbc:mysql://localhost:3306/common-example
    driver-class-name: com.mysql.cj.jdbc.Driver
    type: com.alibaba.druid.pool.DruidDataSource
```



## JdbcTemplate

`JdbcTemplate` 主要提供以下五类方法：

- execute：可以用于执行任何SQL语句，一般用于执行DDL语句；
- update：用于执行新增、修改、删除等语句；
- batchUpdate：方法用于执行批处理update语句；
- query | queryForList | queryForObject：用于执行查询相关语句；
- call：用于执行存储过程、函数相关语句。

spring-jdbc 还提供了一个 `BeanPropertyRowMapper` 类，实现了 `RowMapper` 接口 ，可以实现查询后的数据封装

```java
List<Account> accounts = jdbcTemplate.query("select * from account where id = ?",
        											new BeanPropertyRowMapper<Account>(Account.class), id);
```



## 代码示例

```java
package Jdbc.SpringTemplate;

public class demo {
		
    // 传递一个数据库连接对象，这里通过 druid 取得
    private JdbcTemplate template = new JdbcTemplate(DruidUtils.getDataSource());

    @Test
    public void test01() {
        String sql = "update student set score = score - ? where id = ?";
        int i = template.update(sql, 10, 1);
        System.out.println(i);
    }

    @Test
    public void test02() {
        String sql = "insert into student(id, name, score, sex) values(?, ?, ?, ?)";
        int i = template.update(sql, null, "克总", 40, 0);
    }

    @Test
    public void test03() {
        String sql = "select * from student where id = ?";
        Map<String, Object> map = template.queryForMap(sql, 1);
        System.out.println(map);
    }

    @Test
    public void test04() {
        String sql = "select * from student";
        List<Map<String, Object>> list = template.queryForList(sql);
        System.out.println(list);
    }

    @Test
    public void test05() {
        // 手动实现封装，比较麻烦
        String sql = "select * from student";
        List<Student> list = template.query(sql, new RowMapper<Student>() {
            @Override
            public Student mapRow(ResultSet rs, int i) throws SQLException {
                Student student = new Student();
                int id = rs.getInt("id");
                String name = rs.getString("name");
                double score = rs.getDouble("score");
                boolean sex = rs.getBoolean("sex");

                student.setId(id);
                student.setName(name);
                student.setScore(score);
                student.setSex(sex);
              
                return student;
            }
        });
        System.out.println(list);
    }

    @Test
    public void test06() {
        // 自动实现封装
        String sql = "select * from student";
        List<Student> list = template.query(sql, new BeanPropertyRowMapper<Student>(Student.class));
        System.out.println(list);
    }

    @Test
    public void test07() {
        String sql = "select count(id) from student";
        Long total = template.queryForObject(sql, Long.class);
        System.out.println(total);

    }
}
```





## 自动配置原理

### 自动配置类

```spreadsheet
org.springframework.boot.autoconfigure.data.jdbc.JdbcRepositoriesAutoConfiguration
org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration
org.springframework.boot.autoconfigure.jdbc.JdbcTemplateAutoConfiguration
org.springframework.boot.autoconfigure.jdbc.JndiDataSourceAutoConfiguration
org.springframework.boot.autoconfigure.jdbc.XADataSourceAutoConfiguration
org.springframework.boot.autoconfigure.jdbc.DataSourceTransactionManagerAutoConfiguration
org.springframework.boot.autoconfigure.jdbc.DataSourceBeanCreationFailureAnalyzer
org.springframework.boot.autoconfigure.jdbc.HikariDriverConfigurationFailureAnalyzer
```



# Spring Data - Redis🌈

Springboot 2.x 之后使用 lettuce 作为默认客户端



## 参考文档

👉  [Blog：RedisTemplate 基本使用](https://blog.csdn.net/lydms/article/details/105224210)

👉  [Github：SpringData - Redis](https://github.com/spring-projects/spring-data-redis)

[👉  Document：Springboot- Redis](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#features.nosql.redis)  

👉  [Document：SpringData - Redis  ](https://docs.spring.io/spring-data/redis/docs/current/reference/html/#reference)

👉  [JavaDoc：SpringData - Redis](https://docs.spring.io/spring-data/redis/docs/current/api/)



## 引入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<!-- redis默认依赖的连接池 -->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
</dependency>
```



## 相关配置

### redis属性配置

```properties
# application.properties

spring.redis.host=127.0.0.1
spring.redis.port=6379
spring.redis.password=
# 连接超时时间（毫秒）
spring.redis.connect-timeout=5000
# 读超时时间（毫秒）
spring.redis.timeout=5000
# 连接池最大连接数（负值表示没有限制）
spring.redis.lettuce.pool.max-active=8
# 连接池最大阻塞等待时间（负值表示没有限制）
spring.redis.lettuce.pool.max-wait=-1
# 连接池中的最大空闲连接
spring.redis.lettuce.pool.max-idle=8
# 连接池中的最小空闲连接
spring.redis.lettuce.pool.min-idle=0
# 在关闭客户端连接之前等待任务处理完成的最长时间，在这之后，无论任务是否执行完成，都会被执行器关闭
spring.redis.lettuce.shutdown-timeout=100
# 是否缓存空值
spring.cache.redis.cache-null-values=true
```



### 序列化器配置

使用较多的主要有以下三种序列化器类型：

- `JdkSerializationRedisSerializer`：pojo 对象的存取场景。使用 JDK 本身的序列化机制，将 pojo类通过ObjectInputStream/ObjectOutputStream 进行序列化操作，最终 redis-server 中将存储「字节序列」。

- `StringRedisSerializer`：key 或 value 为字符串的场景。根据指定的 charset 对数据的字节序列编码成 String ，是 `new String(bytes, charset)` 和 `String.getBytes(charset)` 的直接封装。是最轻量级和高效的策略。

- `JacksonJsonRedisSerializer`：jackson-json 工具提供了 javabean 与 json 之间的转换能力，可以将 pojo 实例序列化成json 格式存储在 redis 中，也可以将 json 格式的数据转换成 pojo 实例。因为 jackson 工具在序列化和反序列化时，需要明确指定 Class 类型，因此此策略封装起来稍微复杂。
  
  



如果使用默认序列化器进行存储操作，那么 key 字段会出现一段字符类似于 `\xac\xed\x00\x05t\x00`

原因：把任何数据保存到 redis 中时，都需要进行序列化，这里是因为使用了  `JdkSerializationRedisSerializer` 才导致了以上字符的出现。

解决方法：更换默认的序列化器



1. `RedisTemplate`

   key：默认序列化器是 `StringRedisSerializer`

   value：默认序列化器是 `JdkSerializationRedisSerializer`

2. `StringRedisTemplate`

   key & value：默认的序列化器都是 `StringRedisSerializer`。



使用 `使用Jackson2JsonRedisSerialize` 替代 `RedisTemplate` 默认的序列化器

```java
@Configuration
public class RedisConfig {
    @Bean
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory connectionFactory) {
        RedisTemplate<Object, Object> redisTemplate = new RedisTemplate<>();
        redisTemplate.setConnectionFactory(connectionFactory);

        // 使用Jackson2JsonRedisSerialize 替换默认序列化器
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);

        ObjectMapper objectMapper = new ObjectMapper();
        objectMapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        // 存储到redis里的数据将是有类型的json数据，java获取到数据后，将会将数据自动转化为javabean
        objectMapper.activateDefaultTyping(
                LaissezFaireSubTypeValidator.instance,
                ObjectMapper.DefaultTyping.NON_FINAL,
                JsonTypeInfo.As.WRAPPER_ARRAY);
        jackson2JsonRedisSerializer.setObjectMapper(objectMapper);

        // 设置key和value的序列化规则
        redisTemplate.setValueSerializer(jackson2JsonRedisSerializer);
        redisTemplate.setKeySerializer(new StringRedisSerializer());
      	redisTemplate.setHashKeySerializer(jackson2JsonRedisSerializer);
        redisTemplate.setHashValueSerializer(jackson2JsonRedisSerializer);
        redisTemplate.afterPropertiesSet();

        return redisTemplate;
    }
}
```







## RedisTemplate

`RedisTemplate` 提供了 Redis 的各种操作、异常处理及序列化，支持发布订阅，并对 Spring Cache 进行了实现。

```java
public class RedisTemplate<K, V> extends RedisAccessor implements RedisOperations<K, V>, BeanClassLoaderAware {
		// ...
}
```







## RedisOperations

<img src="http://store.secretcamp.cn/uPic/image-20210601144426970202106011444271622529867dSZ1CNdSZ1CN.png" alt="image-20210601144426970" style="zoom:50%;" />

### opsForXXX

对 Redis 操作的 API 进行了封装，将同一类型操作封装为 `XXXOperations` 对象



### boundXXX

`boundXXX` 方法提供了对便捷化操作 API，可以将 key 封装成 `BoundXXXOperations` 对象，然后进行一系列的操作而无须显式的再次指定 key。





### executeXXX





## 自动配置原理

### 自动配置类

```spreadsheet
org.springframework.boot.autoconfigure.data.redis.RedisAutoConfiguration
org.springframework.boot.autoconfigure.data.redis.RedisReactiveAutoConfiguration
org.springframework.boot.autoconfigure.data.redis.RedisRepositoriesAutoConfiguration
org.springframework.boot.autoconfigure.data.redis.RedisUrlSyntaxFailureAnalyzer
```



### RedisAutoConfiguration

Redis 相关的自动配置的主类是 `RedisAutoConfiguration` 

```java
package org.springframework.boot.autoconfigure.data.redis;


@Configuration(proxyBeanMethods = false)
@ConditionalOnClass(RedisOperations.class)
@EnableConfigurationProperties(RedisProperties.class)
// lettuce、jedis的自动配置类
@Import({ LettuceConnectionConfiguration.class, JedisConnectionConfiguration.class })
public class RedisAutoConfiguration {
	
  // 自动配置并将redisTemplate注入容器
	@Bean
  // 如果容器中没有redisTemplate，则进行自动配置，没有则不配置
	@ConditionalOnMissingBean(name = "redisTemplate")
	@ConditionalOnSingleCandidate(RedisConnectionFactory.class)
	public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
		RedisTemplate<Object, Object> template = new RedisTemplate<>();
		template.setConnectionFactory(redisConnectionFactory);
		return template;
	}

  // 自动配置并将StringRedisTemplate注入容器
	@Bean
	@ConditionalOnMissingBean
	@ConditionalOnSingleCandidate(RedisConnectionFactory.class)
	public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory) {
		StringRedisTemplate template = new StringRedisTemplate();
		template.setConnectionFactory(redisConnectionFactory);
		return template;
	}
}
```





#### LettuceConnectionConfiguration

`LettuceConnectionConfiguration` 自动配置类的作用是在容器中注入一个类型为 `LettuceConnectionFactory` 的工厂对象

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnClass(RedisClient.class)
@ConditionalOnProperty(name = "spring.redis.client-type", havingValue = "lettuce", matchIfMissing = true)
class LettuceConnectionConfiguration extends RedisConnectionConfiguration {

	LettuceConnectionConfiguration(RedisProperties properties,
			ObjectProvider<RedisSentinelConfiguration> sentinelConfigurationProvider,
			ObjectProvider<RedisClusterConfiguration> clusterConfigurationProvider) {
		super(properties, sentinelConfigurationProvider, clusterConfigurationProvider);
	}

	@Bean(destroyMethod = "shutdown")
	@ConditionalOnMissingBean(ClientResources.class)
	DefaultClientResources lettuceClientResources() {
		return DefaultClientResources.create();
	}

	@Bean
	@ConditionalOnMissingBean(RedisConnectionFactory.class)
	LettuceConnectionFactory redisConnectionFactory(
			ObjectProvider<LettuceClientConfigurationBuilderCustomizer> builderCustomizers,
			ClientResources clientResources) {
		LettuceClientConfiguration clientConfig = getLettuceClientConfiguration(builderCustomizers, clientResources,
				getProperties().getLettuce().getPool());
		return createLettuceConnectionFactory(clientConfig);
	}
  
 // ... 后面的方法全都是围绕 redisConnectionFactory() 方法展开
}
```



#### JedisConnectionConfiguration

`JedisConnectionConfiguration` 自动配置类的作用是在容器中注入一个类型为 `JedisConnectionFactory` 的工厂对象

```java
@Configuration(proxyBeanMethods = false)
// springdata默认不导入jedis相关包，不会有Jedis.class，所以该配置类默认不生效
@ConditionalOnClass({ GenericObjectPool.class, JedisConnection.class, Jedis.class })
@ConditionalOnMissingBean(RedisConnectionFactory.class)
@ConditionalOnProperty(name = "spring.redis.client-type", havingValue = "jedis", matchIfMissing = true)
class JedisConnectionConfiguration extends RedisConnectionConfiguration {

	JedisConnectionConfiguration(RedisProperties properties,
			ObjectProvider<RedisSentinelConfiguration> sentinelConfiguration,
			ObjectProvider<RedisClusterConfiguration> clusterConfiguration) {
		super(properties, sentinelConfiguration, clusterConfiguration);
	}

	@Bean
	JedisConnectionFactory redisConnectionFactory(
			ObjectProvider<JedisClientConfigurationBuilderCustomizer> builderCustomizers) {
		return createJedisConnectionFactory(builderCustomizers);
	}
	
	// ...  后面的方法全都是围绕 redisConnectionFactory() 方法展开
	
}
```



### RedisRepositoriesAutoConfiguration

```java
@Configuration(proxyBeanMethods = false)
// 必须要通过 @EnableRedisRepositories 注解，才能开启 RedisRepositories 的自动配置
@ConditionalOnClass(EnableRedisRepositories.class)
@ConditionalOnBean(RedisConnectionFactory.class)
@ConditionalOnProperty(prefix = "spring.data.redis.repositories", name = "enabled", havingValue = "true",
		matchIfMissing = true)
@ConditionalOnMissingBean(RedisRepositoryFactoryBean.class)
@Import(RedisRepositoriesRegistrar.class)
@AutoConfigureAfter(RedisAutoConfiguration.class)
public class RedisRepositoriesAutoConfiguration {

}
```



#### 开启RedisRepositories

```java
@EnableRedisRepositories
@SpringBootApplication
public class RedisApplication {
    public static void main(String[] args) {
        SpringApplication.run(RedisApplication.class, args);
    }
}
```



# Spring Data - ElasticSearch🌈

Elasticsearch 提供的 Java 客户端有一些不太方便的地方：

- 很多地方需要拼接 json字符串
- 需要手动把对象序列化为 json 存储
- 查询到结果也需要手动反序列化为对象

因此最好使用 Spring Data Elasticsearch

Spring Data Elasticsearch 是 Spring Data 项目下的一个子模块。

## 参考文档

👉  [Document：SpringBoot - ElasticSearch](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#features.nosql.elasticsearch)

👉  [Document：SpringData - ElasticSearch](https://docs.spring.io/spring-data/elasticsearch/docs/4.2.1/reference/html/#reference)

👉  [JavaDoc：SpringData - ElasticSearch](https://docs.spring.io/spring-data/elasticsearch/docs/4.2.1/api/)



## 引入依赖

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
</dependency>
```



## 客户端配置

```java
@Configuration
public class RestClientConfig extends AbstractElasticsearchConfiguration {

    @Override
    @Bean
    public RestHighLevelClient elasticsearchClient() {

        final ClientConfiguration clientConfiguration = ClientConfiguration.builder()
                .connectedTo("localhost:9200")
                .withConnectTimeout(Duration.ofSeconds(8))
                .withSocketTimeout(Duration.ofSeconds(5))
                .build();

        return RestClients.create(clientConfiguration).rest();
    }
}
```





## ElasticsearchOperations

### ElasticsearchRestTemplate

只要引入了相关依赖，基本类 `AbstractElasticsearchConfiguration` 就已经向容器中注入了 `ElasticsearchRestTemplate`

`ElasticsearchOperations` 是一个接口，定义了 ElasticSearch 中的相关操作

<img src="http://store.secretcamp.cn/uPic/image-202106011447330292021060114473316225300539dYXX29dYXX2.png" alt="image-20210601144733029" style="zoom:50%;" />



### Queries



## ElasticsearchRepository

通过继承 `ElasticsearchRepository` 可以来完成基本的 CRUD 以及分页操作。

<img src="http://store.secretcamp.cn/uPic/image-20210601144613470202106011446131622529973ycpZgjycpZgj.png" alt="image-20210601144613470" style="zoom:50%;" />







### 定义实体类



### 创建查询

```java
public interface AccountRepository extends ElasticsearchRepository<Account, String> {
    List<Account> findByAge(int age);

    Account findByAccountNumber(int accountNumber);

    public int deleteByAccountNumber(int accountNumber);

    Page<Account> findByAddress(String address, Pageable pageable);
}
```



## 自动配置原理

### 自动配置类

```spreadsheet
org.springframework.boot.autoconfigure.elasticsearch.ElasticsearchRestClientAutoConfiguration
org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchDataAutoConfiguration
org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchRepositoriesAutoConfiguration
org.springframework.boot.autoconfigure.data.elasticsearch.ReactiveElasticsearchRepositoriesAutoConfiguration
org.springframework.boot.autoconfigure.data.elasticsearch.ReactiveElasticsearchRestClientAutoConfiguration
```



### ElasticsearchRestClientAutoConfiguration

```java
package org.springframework.boot.autoconfigure.elasticsearch;

@Configuration(proxyBeanMethods = false)
@ConditionalOnClass(RestHighLevelClient.class)
@ConditionalOnMissingBean(RestClient.class)
@EnableConfigurationProperties(ElasticsearchRestClientProperties.class)
// 导入三个子配置类
@Import({ RestClientBuilderConfiguration.class, RestHighLevelClientConfiguration.class,
		RestClientSnifferConfiguration.class })
public class ElasticsearchRestClientAutoConfiguration {

}

```



### ElasticsearchRepositoriesAutoConfiguration

```java
@Configuration(
    proxyBeanMethods = false
)
@ConditionalOnClass({Client.class, ElasticsearchRepository.class})
// 默认开启 elasticsearch-repositories 的支持
@ConditionalOnProperty(
    prefix = "spring.data.elasticsearch.repositories",
    name = {"enabled"},
    havingValue = "true",
    matchIfMissing = true
)
@ConditionalOnMissingBean({ElasticsearchRepositoryFactoryBean.class})
// 导入 ElasticsearchRepositoriesRegistrar
@Import({ElasticsearchRepositoriesRegistrar.class})
public class ElasticsearchRepositoriesAutoConfiguration {
    public ElasticsearchRepositoriesAutoConfiguration() {
    }
}
```





### ElasticsearchDataAutoConfiguration

```java
package org.springframework.boot.autoconfigure.data.elasticsearch;

@Configuration(proxyBeanMethods = false)
@ConditionalOnClass({ElasticsearchRestTemplate.class})
// 在 ElasticsearchRestClientAutoConfiguration 和 ReactiveElasticsearchRestClientAutoConfiguration 两个自动配置类之后配置
@AutoConfigureAfter({ElasticsearchRestClientAutoConfiguration.class,
        ReactiveElasticsearchRestClientAutoConfiguration.class})
@Import({ElasticsearchDataConfiguration.BaseConfiguration.class,
        ElasticsearchDataConfiguration.RestClientConfiguration.class,
        ElasticsearchDataConfiguration.ReactiveRestClientConfiguration.class})
public class ElasticsearchDataAutoConfiguration {

}
```









# Spring Data - MongoDB🌈

## 参考文档

👉  [Document：Springboot - MongoDB](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#features.nosql.mongodb)

👉  [Document：SpringData - MongoDB](https://docs.spring.io/spring-data/mongodb/docs/3.2.1/reference/html/#reference)

👉  [JavaDoc ：SpringData - MongoDB](https://docs.spring.io/spring-data/mongodb/docs/3.2.1/api/)



## 配置文件

```properties
# way1
spring.data.mongodb.uri=mongodb://user:secret@mongo1.example.com:12345,mongo2.example.com:23456/test

# way2
spring.data.mongodb.host=mongoserver.example.com
spring.data.mongodb.port=27017
spring.data.mongodb.database=test
spring.data.mongodb.username=user
spring.data.mongodb.password=secret
```



## 自动配置原理

### 自动配置类

```spreadsheet
org.springframework.boot.autoconfigure.data.mongo.MongoDataAutoConfiguration
org.springframework.boot.autoconfigure.data.mongo.MongoReactiveDataAutoConfiguration
org.springframework.boot.autoconfigure.data.mongo.MongoReactiveRepositoriesAutoConfiguration
org.springframework.boot.autoconfigure.data.mongo.MongoRepositoriesAutoConfiguration
org.springframework.boot.autoconfigure.mongo.embedded.EmbeddedMongoAutoConfiguration
org.springframework.boot.autoconfigure.mongo.MongoAutoConfiguration
org.springframework.boot.autoconfigure.mongo.MongoReactiveAutoConfiguration
```



### MongoAutoConfiguration

```java
package org.springframework.boot.autoconfigure.mongo;

@Configuration(proxyBeanMethods = false)
@ConditionalOnClass(MongoClient.class)
@EnableConfigurationProperties(MongoProperties.class)
// 只有容器中没有 MongoDatabaseFactory 才开启自动配置，如果手动注入 MongoDatabaseFactory 则表示不开启自动配置，即手动接管 mongodb 的所有配置
@ConditionalOnMissingBean(type = "org.springframework.data.mongodb.MongoDatabaseFactory")
public class MongoAutoConfiguration {
	
   // 该方法为容器中注入一个 MongoClient
   @Bean
   @ConditionalOnMissingBean(MongoClient.class)
   public MongoClient mongo(ObjectProvider<MongoClientSettingsBuilderCustomizer> builderCustomizers,
         MongoClientSettings settings) {
      return new MongoClientFactory(builderCustomizers.orderedStream().collect(Collectors.toList()))
            .createMongoClient(settings);
   }
	
   @Configuration(proxyBeanMethods = false)
   @ConditionalOnMissingBean(MongoClientSettings.class)
   static class MongoClientSettingsConfiguration {
	    // 该方法为容器中注入一个 MongoClientSettings
      @Bean
      MongoClientSettings mongoClientSettings() {
         return MongoClientSettings.builder().build();
      }
			// 该方法为容器中注入一个 MongoPropertiesClientSettingsBuilderCustomizer
      @Bean
      MongoPropertiesClientSettingsBuilderCustomizer mongoPropertiesCustomizer(MongoProperties properties,
            Environment environment) {
         return new MongoPropertiesClientSettingsBuilderCustomizer(properties, environment);
      }

   }

}
```



### MongoDataAutoConfiguration

```java
package org.springframework.boot.autoconfigure.data.mongo;

@Configuration(proxyBeanMethods = false)
@ConditionalOnClass({ MongoClient.class, MongoTemplate.class })
@EnableConfigurationProperties(MongoProperties.class)
// 导入以下三个子配置类
@Import({ MongoDataConfiguration.class, MongoDatabaseFactoryConfiguration.class,
		MongoDatabaseFactoryDependentConfiguration.class })
// 在 MongoAutoConfiguration 完成自动配置之后配置
@AutoConfigureAfter(MongoAutoConfiguration.class)
public class MongoDataAutoConfiguration {

}
```

