# Spring🍀

Spring 是分层的 Java SE/EE 应用全栈轻量级开源框架，以 IOC（反转控制）和 AOP（面向切面编程）为内核，提供了表现层 Spring MVC 和持久层 Spring JDBC 以及业务层事务管理等众多的企业级应用技术，还能整合开源世界众多著名的第三方框架和类库。

## 参考文档

👉  [Spring Framework 官方文档](https://docs.spring.io/spring-framework/docs/5.3.6/reference/html/index.html)

👉  [Spring Framework API Doc](https://docs.spring.io/spring-framework/docs/current/javadoc-api/)

👉  [Spring Framework 中文文档](https://www.docs4dev.com/docs/zh/spring-framework/5.1.3.RELEASE/reference)



## Spring的优势

- 方便解耦，简化开发 

  通过 Spring 提供的 IoC 容器，可以将对象间的依赖关系交由Spring进行控制，避免硬编码所造成的过度程序耦合。用户也不必再为单例模式类、属性文件解析等这些很底层的需求编写代码，可以更专注于上层的应用。 

- AOP 编程的支持

  通过 Spring 的 AOP 功能，方便进行面向切面的编程，许多不容易用传统 OOP 实现的功能可以通过AOP 轻松应付。

- 声明式事务的支持

  通过声明式方式灵活的进行事务的管理，提高开发效率和质量。 

- 方便程序的测试

  可以用非容器依赖的编程方式进行几乎所有的测试工作



## spring-context

Maven中配置 spring-context 依赖

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.1.5.RELEASE</version>
</dependency>
```



导入 `spring-context` 后自动导入其他依赖，在 `spring-context-5.x.x-RELEASE.pom` 中有以下几项依赖：

- `spring-core`
- `spring-beans`
- `spring-aop`
- `spring-expression`





# 控制反转🍀

控制反转（Inversion of Control，IOC），是面向对象编程中的一种设计原则，可以用来减低计算机代码之间的耦合度。

通过控制反转，对象在被创建的时候，由一个调控系统内所有对象的外界实体将其所依赖的对象的引用传递给它。也可以说，依赖被注入到对象中。

使用 IOC 之前，创建对象的主动权和创建时机是由自己把控的，现在这种权力转移到第三方，比如转移交给了 IOC 容器。对象 A 获得依赖对象 B 的过程，由主动行为变为了被动行为，控制权颠倒过来了，这就是 “控制反转” 的由来，即获得依赖对象的过程反转了。

IOC 最常见的实现方式叫做依赖注入（Dependency Injection，DI）。

<img src="http://store.secretcamp.cn/uPic/image-202103110902442812021031109024416154245645pDLYb5pDLYb.png" alt="image-20210311090244281" style="zoom: 50%;" />

<img src="http://store.secretcamp.cn/uPic/image-20210311090309549202103110903091615424589tPAKOitPAKOi.png" alt="image-20210311090309549" style="zoom:47%;" />



## 核心容器

ApplicationContext 的三个常用实现类

- `ClassPathXmlApplicationContext`：可以加载类路径下的配置文件，要求配置文件必须在类路径下。

- `FileSystemXmlApplicationContext`：可以加载磁盘任意路径下的配置文件（必须有访问权限） 

- `AnnotationConfigApplicationcontext`：使用注解配置容器对象时，需要使用此类来创建 spring 容器，传递的参数是配置类，所以仅在纯注解配置的情况下使用。

核心容器的两个接口引发出的可题：

`BeanFactory` 是 Spring 容器中的顶层接口，`ApplicationContext` 是它的子接口。



## Bean定义

Spring Bean 就是被 Spring 容器所管理的 Java 对象

在基于 XML 的配置中， `bean.xml` 为 Spring 容器管理 bean 提供元数据



## Bean标签

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="accountService" class="kzq.spring.service.implement.AccountServiceImpl"></bean>
    <bean id="accountMapper" class="kzq.spring.mapper.implement.AccountMapperImpl"></bean>
</beans>
```

bean标签中的属性：

- `id`：bean的唯一标识
- `class`：指定类的全限定类名，用于反射创建对象，默认情况下调用无参构造函数
- `scope`：指定对象的作用范围。
  - `singleton`：默认值，单例
  - `prototype`：原型，每次请求都会创建一个新的 bean 实例
  - `request`：每一次 http 请求都会产生一个新的 bean，该 bean 仅在当前 http request 内有效
  - `session`：每一次 http 请求都会产生一个新的bean，该bean仅在当前 http session 内有效。
  - `global-session`：应用在集群环境，如果没有集群环境，那么就相当于 session
- `init-method`：指定类中的初始化方法名称。 
- `destroy-method`：指定类中销毁方法名称。
- `primary`：表示一个 bean 是一个主 bean，如果容器中有多个同类型组件，如果没有指定 id 注入则优先选择有 primary 属性的组件。
- `lazy-init` ：是否懒加载，对于原型 bean 该属性不起作用



## 创建Bean对象

### 使用默认构造函数创建

在 spring 的配置文件中使用 bean 标签，配置 id 和 class 属性之后，且没有其他属性和标签时，采用的就是默认构造函数创建 bean对象，此时如果类中没有默认构造函数，则对象无法创建。

```xml
<bean id="accountService01" class="kzq.spring.service.impl.AccountServiceImpl"></bean>
```



### 方法创建

使用普通工厂中的方法创建对象，使用某个类中的方法创建对象，并存入spring容器

```xml
<bean id="instanceFactory" class="kzq.spring.factory.InstanceFactory"></bean>
<bean id="accountService02" factory-bean="instanceFactory" factory-method="getAccountService"></bean>
```



### 静态方法创建

使用工厂中的静态方法创建对象，使用某个类中的静态方法创建对象，并存入spring容器

```xml
<bean id="accountService03" class="kzq.spring.factory.StaticFactory" factory-method="getAccountService"></bean>
```





## bean范围与生命周期

- 单例对象：`scope="singleton"` 

  一个应用只有一个对象的实例。它的作用范围就是整个引用。

  生命周期:

  - 对象出生：当应用加载，创建容器时，对象就被创建了。 

  - 对象活着：只要容器在，对象一直活着。 

  - 对象死亡：当应用卸载，销毁容器时，对象就被销毁了。

- 原型对象：`scope="prototype"` 

  每次访问对象时，都会重新创建对象实例。

  生命周期: 

  - 对象出生：当使用对象时，创建新的对象实例。 
  - 对象活着：只要对象在使用中，就一直活着。
  - 对象死亡：当对象长时间不用时，被java的垃圾回收器回收。



## 引入配置文件

可以使用 `<context:property-placeholder>` 标签引入其他配置文件，再使用SpEL表达式引用

```xml
<context:property-placeholder location="classpath:jdbcConfig.properties"/>
```





# 依赖注入🍀

依赖注入（Dependency Injection，DI）是Spring框架核心 IOC 的具体实现。

Spring项目中，程序在编写时，通过控制反转，把对象的创建交给了Spring，但是代码中不可能出现没有依赖的情况。

IOC 解耦只是降低代码之间的的依赖关系，但不会消除。例如，我业务层仍会调用持久层的方法。那这种业务层和持久层的依赖关系，在使用 Spring 之后，就交由 Spring 来维护。

简单的说，就是等待 Spring 框架将持久层对象传入业务层，而不用我们自己去获取。



## 构造函数注入

使用标签：`constructor-arg` 

标签中的属性：

- type：用于指定要汪入的数据的数据类型，该数据类型也是构造函数中某个或某些参数的类型  

- index：用于指定要注入的数据给构造函数中指定索引位置的参数值。索引的位置是从开始 

- name：用于指定给构造函数中指定名称的参数赋值

- value：用于提供基本类型和string

- ref：用于指定其他的 bean 类型数据，指的是在spring的IoC核心容器中存在的 bean对象的 id



设置三个成员变量，并提供一个三个参数的构造方法

```java
public class AccountServiceImpl implements IAccountService {
    private String name;
    private Integer age;
    private Date birthday;

    public AccountServiceImpl(String name, Integer age, Date birth) {
        this.name = name;
        this.age = age;
        this.birthday = birth;
    }
		// ...
}
```



如果是基本数据类型，则使用 value

如果是引用数据类型，则使用 ref

```xml
<bean id="birthday" class="java.util.Date"></bean>

<bean id="accountService01" class="kzq.spring.service.implement.AccountServiceImpl">
    <constructor-arg name="name" value="yorua"></constructor-arg>
    <constructor-arg name="age" value="20"></constructor-arg>
    <constructor-arg name="birth" ref="birthday"></constructor-arg>
</bean>
```



构造函数注入的优缺点：

优点：获取 bean 对象时，注入数据是必须的操作，否则对象无法创建成功。

缺点：改变了 bean 对象的实例化方式，使我们在创建对象时，必须要提供所有参数，如果用不到这些数据，也必须提供。



## set方法注入

标签中的属性：

- name：与 set 方法的方法名有关，setXXX，则 name=XXX
- value：用于提供基本类型和string
- ref：用于指定其他的 bean 类型数据，指的是在spring的IoC核心容器中存在的 bean对象的 id



set 方法注入的优缺点：

优点：创建对象时没有明确的限制，可以直接使用默认构造函数

缺点：如果有某个成员必须有值，set方法无法保证

### 基本数据类型注入

使用 set 方法注入，必须要在实体类中先生成 set 方法

```xml
<bean id="birth" class="java.util.Date"></bean>

<bean id="accountService02" class="kzq.spring.service.implement.AccountServiceImpl02">
    <property name="name" value="zsh"></property>
    <property name="age" value="23"></property>
    <property name="birthday" ref="birth"></property>
</bean>
```



### 复杂数据类型注入

用于给List 结构集合注入的标签：list、array、set

用于给Map结构集合注入的标签：map、props

结构相同，标签可以互换

```xml
<property name="myString">
    <array>
        <value>a</value>
        <value>bb</value>
        <value>ccc</value>
    </array>
</property>
<property name="myList">
    <list>
        <value>a</value>
        <value>bb</value>
        <value>ccc</value>
    </list>
</property>
<property name="mySet">
    <set>
        <value>a</value>
        <value>bb</value>
        <value>ccc</value>
    </set>
</property>
<property name="myMap">
    <map>
        <entry key="name" value="kiana"></entry>
        <entry key="age" value="18"></entry>
    </map>
</property>
<property name="myProp">
    <props>
        <prop key="name">zzz</prop>
        <prop key="age">15</prop>
    </props>
</property>
```





## 注解注入

- [@AutoWired](#@AutoWired)

- [@Qualifier](#@Qualifier)

- [@Resource](#@Resource)
- [@Value](#@Value )



# 常用注解🍀

## 开启注解扫描

如果不用 `@Configuration` 配置注解类，还想用容器相关注解，则需要在 `bean.xml` 中开启注解扫描

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">
		
  <!-- 开启注解扫描 -->
    <context:component-scan base-package="kzq.spring"></context:component-scan>

</beans>
```





## 容器相关注解🌵

创建对象相关注解的作用就和在 xml 文件中编写一个 bean 标签实现的功能是一样的

```xml
<bean id="accountService01" class="kzq.spring.service.impl.AccountServiceImpl"
      scope="singleton"
      init-method="init" destroy-method="destroy">
    <property name="" value="" | ref=""></property>
</bean>
```



### @Component

用于将当前的类对象存入 Spring 的 IOC 容器中

- value：用于指定bean的唯一id，如果为空，则默认值是当前类名，且首字母改成小写。

  eg：AccountService（ClassName） -> accountService（bean-id）

`@Component `是通用注解，其他三个注解是这个注解的拓展，并且具有了特定的功能



### @Controller

用于表现层，除了与 `@Component` 相同的作用，还具有将请求进行转发，重定向的功能。



### @Service

用于业务层，实际上作用与 `@Component` 相同



### @Repository

用于持久层，除了与 `@Component` 相同的作用，还具有将数据库操作抛出的原生异常翻译转化为Spring的持久层异常的功能。



### @Bean

jar包中的类无法修改，所以不能加 `@Component` 之类的注解，一种有效的方法是在配置类中创建 `getXXX` 方法，在方法上加上 `@Bean` 注解，则可以实现将 `XXX` 加入 ioc 容器中

`@Bean` 用于把当前方法的返回值作为 bean 对象存入 Spring 的 ioc 容器中

- name：用于指定 bean 的 id，如果为空，则默认值是当前方法的名称
- value：效果和name相同
- initMethod：初始化执行方法
- destroyMethod：销毁执行方法

```java
@Target({ElementType.METHOD, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Bean {
    @AliasFor("name")
    String[] value() default {};

    @AliasFor("value")
    String[] name() default {};

    /** @deprecated */
    @Deprecated
    Autowire autowire() default Autowire.NO;

    boolean autowireCandidate() default true;

    String initMethod() default "";

    String destroyMethod() default "(inferred)";
}
```

当使用 ` @Bean` 注解配置到方法时，如果方法有参数，Spring框架会去容器中査找是否有可用的 bean 对象，

查找的方式和 `@Autowired` 的方式是一样的

如果在ioc容器中存在多个同类型对象，则无法区分注入的参数具体是哪一个，则需要在方法参数上使用 [@Qualifier](#@Qualifier) 注解



## 注入数据相关注解🌵

以下作用和在 xml 中 bean 标签中配置 `<property>` 标签的作用相同

集合类型的注入只能通过 xml 来实现。



### @AutoWired

自动按照类型注入，只要容器中有唯一的一个 bean 对象类型与要注入的变量类型相匹配，就可以注入成功。

注解中的 `required` 属性默认为 true，表示如果没有对象可以注入给属性则抛异常。

```java
@Repository("accountMapper")
public class AccountMapperImpl implements IAccountMapper {
    public void saveAccount() {
        // ...
    }
}
```

根据变量类型 `IAccountMapper` 查找，只有一个 id="accountMapper" 的  `IAccountMapper` 类型的 bean

```java
@Service("accountService")
public class AccountServiceImpl implements IAccountService {

    @Autowired
    private IAccountMapper iAccountMapper;   
		// ...
  
    }
}
```



当 `@Autowired` 注解写在某个方法上时，Spring 在 bean 生命周期的属性填充阶段，会根据方法的参数类型、参数名字从 Spring 容器找到对象当做方法入参，自动反射调用该方法。



### @Qualifier

如果有多个匹配时，`@AutoWired `无法确定要注入哪个对象，此时可以使用 `@Qualifier` ，需要和 `@AutoWired` 配合使用。

首先根据按照数据类型找到所有自动注入的候选对象，再根据变量名称选择唯一的匹配对象。

它在给类成员注入时不能单独使用，但是在给方法参数注入时可以。



```java
@Repository("accountMapper01")
public class AccountMapperImpl implements IAccountMapper {
    public void saveAccount() {
        // ...
    }
}

@Repository("accountMapper02")
public class AccountMapperImpl implements IAccountMapper {
    public void saveAccount() {
        // ...
    }
}
```

首先根据变量类型AccountMapper查找，发现有多个匹配，再根据bean的唯一标识"accountMapper01" 查找

```java
@Service("accountService")
public class AccountServiceImpl implements IAccountService {
  
		@Qualifier("accountMapper01")
    @Autowired
    private IAccountMapper accountMapper01;
		// ...
  
    }
}
```





如果容器中对象的某个方法的参数需要注入，但是容器中存在多个同类型对象，此时无法区分注入的参数具体是哪一个。

这时可以在参数前加 `@Qualifier` 注解，指定具体注入的参数对象。

```java
@Bean(name = "runner")
@Scope("prototype")
public QueryRunner createQueryRunner(@Qualifier("ds1") DataSource dataSource) {
    return new QueryRunner(dataSource);
}

@Bean(name = "ds1")
public DataSource createDataSource01() {
    ComboPooledDataSource ds = new ComboPooledDataSource();
    try {
        ds.setDriverClass(driver);
        ds.setJdbcUrl(url);
        ds.setUser(username);
        ds.setPassword(password);
        System.out.println("ds1");
        return ds;
    } catch (Exception e) {
        throw new RuntimeException(e);
    }
}

@Bean(name = "ds2")
public DataSource createDataSource02() {
    ComboPooledDataSource ds = new ComboPooledDataSource();
    try {
        ds.setDriverClass(driver);
        ds.setJdbcUrl(url);
        ds.setUser(username);
        ds.setPassword(password);
        System.out.println("ds2");
        return ds;
    } catch (Exception e) {
        throw new RuntimeException(e);
    }
}
```



### @Resource

`@Autowired` 是 Spring 所提供的注解，而 `@Resource` 是 Java 层面所提供的注解， 

通过 name 属性可以直接指定 bean 的 id 注入，该注解可以独立使用。

```java
@Service("accountService")
public class AccountServiceImpl implements IAccountService {

    @Resource(name = "accountMapper01")
    private IAccountMapper accountMapper;

		// ...
}
```





### @Value

`@Value` 用于从 properties 文件中获取值，可以注入基本类型和 String 类型的数据。

value：用于指定数据的值，可以解析 SpEL 表达式（Spring 的 EL 表达式）

SpEL 的写法：`${}`

```java
public class JdbcConfig {
		
  	// 将Spring域中的数据注入成员变量
  
    @Value("${jdbc.driver}")
    private String driver;
    @Value("${jdbc.url}")
    private String url;
    @Value("${jdbc.username}")
    private String username;
    @Value("${jdbc.password}")
    private String password;

    @Bean
    public DataSource createDataSource01() {
        ComboPooledDataSource ds = new ComboPooledDataSource();
        try {
            ds.setDriverClass(driver);
            ds.setJdbcUrl(url);
            ds.setUser(username);
            ds.setPassword(password);
            System.out.println("ds1");
            return ds;
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }

}
```



`@Value` 的作用相当于 property 中的 value 值

- `${key}`：从环境变量、配置文件中获取值

- `#{SpEL}`：SpEL表达式

```xml
<bean class="Person">
     <property name="lastName" value="字面量/${key}/#{SpEL}"></property>
<bean/>
```



`@Value` 无法对复杂数据类型进行封装，例如 List 和 Map

|                      | @ConfigurationProperties | @Value     |
| -------------------- | ------------------------ | ---------- |
| 功能                 | 批量注入配置文件中的属性 | 一个个指定 |
| 松散绑定（松散语法） | 支持                     | 不支持     |
| SpEL                 | 不支持                   | 支持       |
| JSR303数据校验       | 支持                     | 不支持     |
| 复杂类型封装         | 支持                     | 不支持     |



## 改变作用范围相关注解🌵

作用就和在 `<bean>` 标签中使用scope属性实现的功能是一样的

### @scope

用于指定bean的作用范围

- singleton：单例
- prototype：多例

```java
@Bean(name = "runner")
@Scope("prototype")
public QueryRunner createQueryRunner() {
    return new QueryRunner();
}
```



## 生命周期相关注解🌵

作用就和在 `<bean>` 标签中使用 init-method 以及 destroy-method 的作用是相同的

### @PreDestroy

用于指定销毁方法



### @PostConstruct

用于指定初始化方法







## 配置文件相关注解🌵

### @Configuration

指定当前类是一个配置类


```java
@Configuration
public class MyAppConfig {
  
	//将方法的返回值添加到容器中；容器中这个组件默认的id就是方法名
  @Bean
  public HelloService helloService(){
      return new HelloService();
}
```



如果配置类是创建 `AnnotationConfigApplicationContext` 对象的参数时，该注解可以不用加

```java
// SpringConfig.class作为参数传递，此时SpringConfig这个配置类可以不加 @Configuration
ApplicationContext ac = new AnnotationConfigApplicationContext(SpringConfig.class);
```



相关参数：

- proxyBeanMethods：指定 `@Bean` 注解标注的方法是否使用代理，默认为 true



### @ComponentScan

用于通过注解指定 Spring 在创建容器时要扫描的包。创建一个配置类，在配置类上添加 @ComponentScan 注解。该注解默认会扫描该类所在的包下所有的配置类，相当于之前的 `<context:component-scan>` 。

- value：用于指定创建容器时要扫描的包。
- basePackages：和value相同

```java
@ComponentScan
public class SpringConfig {

}
```

如果使用了 `@ComponentScan` ，将不再需要 bean.xml 中的扫描配置

```xml
<context:component-scan base-package="kzq.spring"></context:component-scan>
```



### @Import

用于导入其他的配置类，参数为其他配置类的字节码文件

使用 `@Import` 注解之后，有 `@Import` 注解的类为父配置类，而导入的都是子配置类



```java
// 创建一个 JdbcConfig.java 作为子配置类
public class JdbcConfig {
  
    @Bean(name = "runner")
    @Scope("prototype")
    public QueryRunner createQueryRunner(DataSource dataSource) {
        return new QueryRunner(dataSource);
    }

    @Bean
    public DataSource createDataSource() {
        ComboPooledDataSource ds = new ComboPooledDataSource();
        try {
            ds.setDriverClass(driver);
            ds.setJdbcUrl(url);
            ds.setUser(username);
            ds.setPassword(password);
            return ds;
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
}
```



```java
// 导入子配置
@Import(JdbcConfig.class)
@Configuration
@ComponentScan(basePackages = "kzq.spring")
public class SpringConfig {
	// ...
}
```





### @ImportResource

在注解配置中导入 xml 的配置文件

例如创建一个 Spring 的配置文件 `bean.xml`

```java
@ImportResource("classpath:bean.xml")
@SpringBootApplication
public class SpringbootApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootApplication.class, args);
    }

}
```





### @Conditional

`@Conditional` 的作用是按照一定的条件进行判断，满足条件给容器注册 bean







### @PropertySource

用于指定 properties 文件的位置，作用类似于以下标签

```xml
<context:property-placeholder location="classpath:jdbc.properties" />
```

- value：指定文件的名称和路径。关键字：classpath，表示类路径下

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Repeatable(PropertySources.class)
public @interface PropertySource {
    String name() default "";

    String[] value();

    boolean ignoreResourceNotFound() default false;

    String encoding() default "";

    Class<? extends PropertySourceFactory> factory() default PropertySourceFactory.class;
}
```



配置文件：

```properties
# jdbc.properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/spring_learn
jdbc.user=root
jdbc.password=kzqkzq
```



可以配合 `@Value`  和  `@ConfigurationProperties` 使用。

👉  配合  `@ConfigurationProperties` 使用

```java
// 指定properties文件的位置
@Component
@PropertySource("classpath:dataSource.properties")
@ConfigurationProperties(prefix = "jdbc")
public class DataSource {
    private String driverClass;
    private String url;
    private String username;
    private String password;
    // ...
}
```



👉  配合  `@Value` 使用

```java
// 指定properties文件的位置
@Component
@PropertySource("classpath:dataSource.properties")
public class DataSource {
    @Value("jdbc.driver")
    private String driverClass;
    @Value("jdbc.url")
    private String url;
    @Value("jdbc.username")
    private String username;
    @Value("jdbc.password")
    private String password;
    // ...
}
```



## 缓存相关注解🌵

### @EnableCaching



## 空值相关注解🌵

### @NonNullApi

作用在包上，声明相关参数和返回值既不接受 null，也不返回null



### @NonNull

作用在方法、参数或返回值上，声明不能是null



### @Nullable 

作用在参数或返回值上，声明可以是 null





# AOP🍀

面向切面编程（Aspect Oriented Programming，AOP）是一种编程思想，是面向对象编程（OOP）的一种补充。

简单的说是将程序中重复的代码抽取出来，在需要执行的时候，使用动态代理的技术，在不修改源码的基础上，对已有的方法进行增强。



## 动态代理

代理模式给某一个对象提供一个代理对象，并由代理对象控制对原对象的引用，不修改对象源码的基础上对方法增强。

- 静态代理：需要手动的去实现目标对象的代理类，字节码一开始就创建好，并完成加载。

- 动态代理：在程序运行期间，使用反射机制动态的生成代理类。

特点：字节码随用随创建，随用随加载



### 基于接口的动态代理（jdk）

涉及的类：`Proxy` 

提供者：JDK官方

如何创建代理对象：使用 `Proxy` 类中的 `newProxyInstance` 方法，返回一个 `Object` 对象

要求：被代理类的至少要实现一个接口，否则无法使用动态代理



`newProxyInstance` 方法的参数：

- `ClassLoader`：类加载器，用于加载代理对象字节码的。和被代理对象使用相同的类加载器。

- `Class[]`：它是用于让代理对象和被代理对象有相同方法，只要实现相同的接口，就都拥有接口中定义的方法

- `InvocationHandler`：用于提供增强的代码，一般传递一个该接口的实现类，通常情况下是匿名内部类，但不是必须的。

```java
public interface Producer {
    void saleProduct(float money);

    void afterService(float money);
}

public class Client {
    public static void main(String[] args) {
      	// 匿名内部类访问外部成员变量时，要求外部成员变量必须被final修饰
        final Producer producer = new ProducerImpl();
        Producer proxyInstance = (Producer) Proxy.newProxyInstance(
                producer.getClass().getClassLoader(),
                producer.getClass().getInterfaces(),
                new InvocationHandler() {
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                      	// 提供增强的代码
                        return method.invoke(producer, args)
                    }
                });
    }
}
```

重写的 `invoke()` 方法：执行被代理对家的任何接口方法都会经过该方法

- Object proxy：代理对象的引用
- Method method：当前执行的方法
- Object[] args：当前执行方法所需的参数
- return：和被代理对象有相同的返回值

```java
/**
 * @description 使用代理对象增强producer对象的saleProduct方法
 *              producer是被代理对象
 */
public class Client {
    public static void main(final String[] args) {
        // 匿名内部类访问外部成员变量时，要求外部成员变量必须被final修饰
        final Producer producer = new ProducerImpl();
      
        Producer proxyInstance = (Producer) Proxy.newProxyInstance(
                producer.getClass().getClassLoader(),
                producer.getClass().getInterfaces(),
                new InvocationHandler() {
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        Object returnValue = null;
                        Float money = (Float) args[0];
                        System.out.println(args.getClass());
                        if ("saleProduct".equals(method.getName())) {
                            returnValue = method.invoke(producer, money * 0.8f);
                        }
                        return returnValue;
                    }
                });
				
        proxyInstance.saleProduct(1000);
    }
}
```



### 基于子类的动态代理（cglib）

基于子类的动态代理基于第三方 cglib 库

首先导入 maven 依赖

```xml
<dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib</artifactId>
    <version>2.2.2</version>
</dependency>
```

涉及的类：`Enhancer` 

提供者：第三方 cglib 库

如何创建代理对象：使用 `Enhancer` 类中的 `create` 方法

要求：被代理类不能是 final 修饰的最终类  



`create()` 方法的参数：

- Class：字节码，用于指定被代理对象的字节码
- Callback：接口类型，用于提供增强的代码，一般是传递一个该接口的实现类，通常情况下都是匿名内部类，一般写的都是该接口的子接口实现类 `MethodInterceptor`

```java
public interface Callback {
	// Callback 中没有定义任何方法
}
```



```java
public class Producer {
    public void saleProduct(float money) {
        System.out.println("生产商品卖了" + money + "元");
    }

    public void afterService(float money) {
        System.out.println("售后服务收了" + money + "元");
    }
}

public class Client {
    public static void main(final String[] args) {
        final Producer producer = new Producer();
        Producer cglibProducer = (Producer) Enhancer.create(producer.getClass(),
                new MethodInterceptor() {
                    @Override
                    public Object intercept(Object proxy, Method method, Object[] args,
                                            MethodProxy methodProxy) throws Throwable {
                        Object returnValue = null;
                        Float money = (Float) args[0];
                        if ("saleProduct".equals(method.getName())) {
                            returnValue = method.invoke(producer, money * 0.8f);
                        }
                        return returnValue;
                    }
                });

        cglibProducer.saleProduct(12000);
    }
}
```

`intercept()` 方法的参数：

- Object proxy：代理对象的引用
- Method method：当前执行的方法
- Object[] args：当前执行方法所需的参数
- MethodProxy methodProxy：当前执行方法的代理对象

```java
public class Client {
    public static void main(final String[] args) {
        final Producer producer = new Producer();
        Producer cglibProducer = (Producer) Enhancer.create(producer.getClass(),
                new MethodInterceptor() {
                    @Override
                    public Object intercept(Object proxy, Method method, Object[] args,
                                            MethodProxy methodProxy) throws Throwable {
                        Object returnValue = null;
                        Float money = (Float) args[0];
                        if ("saleProduct".equals(method.getName())) {
                            returnValue = method.invoke(producer, money * 0.8f);
                        }
                        return returnValue;
                    }
                });

        cglibProducer.saleProduct(12000);
    }
}
```



### 两种动态代理实现的区别

jdk 动态代理具体实现原理：

- 通过实现 `InvocationHandler` 接口创建自己的调用处理器；

- 通过为 `Proxy` 类指定 ClassLoader 对象和一组 interface 来创建动态代理；

- 通过反射机制获取动态代理类的构造函数，其唯一参数类型就是调用处理器接口类型；

- 通过构造函数创建动态代理类实例，构造时调用处理器对象作为参数参入；



cglib 动态代理：

- 利用 ASM 开源包，对代理对象类的 class 文件加载进来，通过修改其字节码生成子类来处理

- 通过字节码底层继承要代理类来实现，因此如果被代理类被 final 关键字所修饰，会失败。



总结：

- 如果要被代理的对象是个实现类（实现了一个接口），那么 Spring 会使用 JDK 动态代理来完成操作（Spring默认采用 JDK 动态代理）

- 如果要被代理的对象不是个实现类，那么 Spring 会强制使用 CGLib 来实现动态代理



## AOP术语

- Joinpoint（连接点）

  连接点是指被拦截到的点，在Spring中，这些点指的是方法，因为Spring只支持方法类型的

  连接点，一般是指业务层接口中的方法。

- 业务层接口中所有的方法

- Pointcut（切入点）

  切入点是指要对哪些Joinpoint进行拦截的定义，一般指业务层接口中被增强的方法

- Advice（通知/增强）：拦截到Joinpoint之后所要做的事情就是通知。

  通知的类型

  - 前置通知：业务层代码之前的语句
  
  - 后置通知：业务层代码之后的语句
  
  - 异常通知：catch中的语句
  
  - 最终通知：finally中的语句
  
  - 环绕通知：整个invoke方法语句
  
    <img src="http://store.secretcamp.cn/uPic/image-202012122053258572020121220532616077776068OGvvA8OGvvA.png" alt="image-20201212205325857" style="zoom:47%;" />



- Introduction（引介）

  引介是一种特殊的通知，在不修改类代码的前提下，Introduction可以在运行期为类动态地添加一些方法或Field。

- Target（目标对象）

  代理的目标对象，即被代理对象

- Weaving（织入）

  是指把增强应用到目标对象来创建新的代理对象的过程，例如，使用动态代理对象提供事务支持的过程，就叫做 “织入”。

  Spring采用动态代理织入，而AspectJ采用编译期织入和类装载期织入。

- Proxy（代理）

  一个类被 AOP 织入增强后，就产生一个结果代理类。

- Aspect（切面）

  是切入点和通知（引介）的结合



## AOP的工作原理

AOP 是发生在 Bean 的生命周期过程中：

1. Spring 生成 bean 对象时，先实例化出来一个对象，也就是 Target 对象 
2. 再对 Target 对象进行属性填充

3. 在初始化后步骤中，会判断 Target 对象有没有对应的切面 
4. 如果有切面，就表示当前 Target 对象需要进行 AOP

5. 通过 cglib 或 jdk 的动态代理机制生成一个代理对象，作为最终的 bean 对象





## XML配置AOP

### 流程

1. 配置依赖

   ```xml
   <!-- 用于解析切入点表达式 -->
   <dependency>
       <groupId>org.aspectj</groupId>
       <artifactId>aspectjweaver</artifactId>
       <version>1.8.7</version>
   </dependency>
   ```


2. 将通知 bean 交给 Spring 来管理，即注入容器

3. 使用 `<aop: config>` 标签，表明开始 AOP 的配置 

4. 使用 `<aop: aspect>` 标签，表明配置切面

5. 在 `<aop: aspect>` 标签的内部使用对应标签来配通知的类型





### aop:aspect 属性

`<aop:aspect>` 属性：

- `id`：给切面提供一个唯一标识
- `ref`：是指定通知类 bean 的 id

在 `<aop:aspect>` 中可以添加相关通知方法的标签

```xml
<!-- Spring中基于xml的aop配置 -->
<aop:config>
    <!-- 配置切面 -->
    <aop:aspect id="logAdvice" ref="logger">
        <!-- 建立通知方法和切入点方法的关联 -->
        <aop:before method="printLog"
                    pointcut="execution(public void kzq.yorua.service.impl.
                              AccountServiceImpl.saveAccount())">
      	</aop:before>
    </aop:aspect>
</aop:config>
```



### XML配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">
  
    <!-- 将service的实现注入容器 -->
    <bean id="accountService" class="kzq.yorua.service.impl.AccountServiceImpl"/>
  
  	<!-- 将通知类注入容器 -->
    <bean id="logger" class="kzq.yorua.utils.Logger"/>
  
    <!-- Spring中基于xml的aop配置 -->
    <aop:config>
        <!-- 配置切面 -->
        <aop:aspect id="logAdvice" ref="logger">
            <!-- 建立通知方法和切入点方法的关联 -->
            <aop:before method="printLog"
                        pointcut="execution(public void kzq.yorua.service.impl.AccountServiceImpl.saveAccount())"></aop:before>
        </aop:aspect>
    </aop:config>
</beans>
```





## 切入点表达式

### 基本语法

execution( 表达式... )

表达式语法：execution([修饰符] 返回值类型 包名.类名.方法名(参数))

全匹配方式：

```
execution(public void kzq.yorua.service.impl.AccountServiceImpl.saveAccount())
```

注意事项：

- 访问修饰符可以省略

- 返回值可以用通配符，表示任意返回值

- 包名可以使用通配符，表示任意包，但必须清楚表达包的层级关系

- 包名可以使用 `..` ，表示当前包及其子包
- 类名和方法名都可以使用通配符
- 方法的参数可以用通配符表示任意参数，但必须有参数
  - 基本类型：直接写名称（int）
  - 引用类型：写全限定类名（java.lang.String）

- 可以使用 `..` 表示有无参数均可

因此，以上全匹配方式可以简化为

```
execution(* kzq.yorua.service..*.*(..))
```

`kzq.yorua.service..` 表示 kzq.yorua.service包及其子包

`*.*(..)` 表示目标包中的任意方法



### 引用切入点表达式

可以将 `<aop:pointcut>` 配置在切面中，切点则在当前配置的切面中被引用。

```xml
<aop:config>
    <aop:aspect id="logAdvice" ref="logger">
        <aop:before method="beforePrintLog" pointcut-ref="servicePos"></aop:before>
        <aop:after method="afterPrintLog" pointcut-ref="servicePos"></aop:after>
        <!-- 配置通用的切入点表达式 -->
        <aop:pointcut id="servicePos" expression="execution(* kzq.yorua.service..*(..))"/>      
    </aop:aspect>
</aop:config>
```

`<aop:pointcut>` 也可以配置在 `<aop:aspect>` 标签外部，但必须配置在 `<aop:aspect>` 标签的前面，切点可以在所有的切面中被引用。

```xml
<aop:config>
    <!-- 配置通用的切入点表达式 -->
    <aop:pointcut id="servicePos" expression="execution(* kzq.yorua.service..*(..))"/>
    <aop:aspect id="logAdvice" ref="logger">
        <aop:before method="beforePrintLog" pointcut-ref="servicePos"></aop:before>
        <aop:after method="afterPrintLog" pointcut-ref="servicePos"></aop:after>
    </aop:aspect>
</aop:config>
```



## 通知方法配置

AOP 通知的参数：

- `method`：指定通知中方法的名称
- `pointcut`：定义切入点表达式
- `pointcut-ref`：指定切入点表达式的引用



AOP 通知的类型：

1. 前置通知： `<aop:before>`

2. 后置通知： `<aop:after-returning>`

3. 异常通知： `<aop:after-throwing>`

4. 最终通知： `<aop:after>`

5. 环绕通知： `<aop:around>`

通知方法同时最多只能执行三个，异常通知和后置通知不能同时触发

```xml
<bean id="logger" class="kzq.yorua.utils.Logger"/>
<aop:config>
    <aop:pointcut id="servicePos" expression="execution(* kzq.yorua.service..*(..))"/>
    <aop:aspect id="logAdvice" ref="logger">
        <aop:before method="beforePrintLog" pointcut-ref="servicePos"/>
        <aop:after-returning method="afterReturningPrintLog" pointcut-ref="servicePos"/>
        <aop:after-throwing method="afterThrowingPrintLog" pointcut-ref="servicePos"/>
        <aop:after method="afterPrintLog" pointcut-ref="servicePos"/>
        <aop:around method="aroundThrowingPrintLog" pointcut-ref="servicePos"/>
    </aop:aspect>
</aop:config>
```



## 环绕通知

Spring中的环绕通知：它是Spring框架提供的一种可以在代码中手动控制増强方法何时执行的方式。

Spring框架为我们提供了一个接口：`Proceedingjoinpoint` ，该接口有一个 `proceed()` 方法 ，此方法就相当于明确调用切入点方法，该接口可以作为环绕通知的方法参数，在程序执行时，Spring 框架会为我们提供该接口的实现类供我们使用。

```java
public Object aroundThrowingPrintLog(ProceedingJoinPoint pjp) {
    Object rtValue = null;
    try {
        System.out.println("前置通知...");
        Object[] args = pjp.getArgs();
        Object proceed = pjp.proceed();
        System.out.println("后置通知...");
        return rtValue;
    } catch (Throwable throwable) {
        System.out.println("异常通知...");
        throw new RuntimeException(throwable);
    }   finally {
        System.out.println("最终通知...");
    }

}
```





## AOP注解配置

### XML方式开启

通过 `<aop:aspectj-autoproxy></aop:aspectj-autoproxy>` 开启 AOP 相关的注解扫描

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 配置Spring创建容器时要扫描的包 -->
    <context:component-scan base-package="kzq.yorua"></context:component-scan>
  
    <!-- 开启AOP注解配置-->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
</beans>
```



### 注解方式开启

`@EnableAspectJAutoProxy`：开启AOP注解，等同于XML配置中的  `<aop:aspectj-autoproxy>` 标签

```java
@Configuration
@ComponentScan("kzq.yorua")
@EnableAspectJAutoProxy
public class SpringConfig {

}
```



### 相关注解

`@Aspect`：表示当前类是一个切面类

`@Pointcut`：配置切入点表达式

`@Before`：标识前置通知方法

`@AfterReturning`：标识后置通知方法

`@AfterThrowing`：标识异常通知方法

`@After`：标识最终通知方法

`@Around`：标识环绕通知方法

> 需要注意的是，注释配置AOP中，`@After` 和 `@AfterReturning` 的触发时机是相互调换的

```java
@Component("logger")
@Aspect
public class Logger {
    @Pointcut("execution(* kzq.yorua.service..*(..))")
    public void pointCut(){};

    @Before("pointCut()")
    public void beforePrintLog() { 
        System.out.println("记录日志... before");
    }

    @AfterReturning("pointCut()")
    public void afterReturningPrintLog() {
        System.out.println("记录日志... after returning");
    }
    @AfterThrowing("pointCut()")
    public void afterThrowingPrintLog() {
        System.out.println("记录日志... after throwing");
    }

    @After("pointCut()")
    public void afterPrintLog() {
        System.out.println("记录日志... after");
    }

}
```







# 事务🍀

## 配置方式

Spring 支持编程式事务管理以及声明式事务管理两种方式。



### 编程式事务管理

编程式事务管理是侵入性事务管理，使用 `TransactionTemplate` 或者直接使用 `PlatformTransactionManager`，对于编程式事务管理，Spring 推荐使用 `TransactionTemplate` 。



### 声明式事务管理

声明式事务管理建立在 AOP 之上，其本质是对方法前后进行拦截，然后在目标方法开始之前创建或者加入一个事务，执行完目标方法之后根据执行的情况提交或者回滚。
编程式事务每次实现都要单独实现，但业务量大功能复杂时，使用编程式事务无疑是痛苦的，而声明式事务不同，声明式事务属于无侵入式，不会影响业务逻辑的实现，只需要在配置文件中做相关的事务规则声明或者通过注解的方式，便可以将事务规则应用到业务逻辑中。
显然声明式事务管理要优于编程式事务管理，这正是Spring倡导的非侵入式的编程方式。唯一不足的地方就是声明式事务管理的粒度是方法级别，而编程式事务管理是可以到代码块的，但是可以通过提取方法的方式完成声明式事务管理的配置。





## 事务的传播机制

事务的传播性一般用在事务嵌套的场景，比如一个事务方法里面调用了另外一个事务方法，那么两个方法是各自作为独立的方法提交还是内层的事务合并到外层的事务一起提交，这就是需要事务传播机制的配置来确定怎么样执行。

常用的事务传播机制如下：

- `PROPAGATION_REQUIRED`

  Spring默认的传播机制，能满足绝大部分业务需求，如果外层有事务，则当前事务加入到外层事务，一块提交，一块回滚。如果外层没有事务，新建一个事务执行

- `PROPAGATION_REQUES_NEW`

  该事务传播机制是每次都会新开启一个事务，同时把外层事务挂起，当当前事务执行完毕，恢复上层事务的执行。如果外层没有事务，执行当前新开启的事务即可

- `PROPAGATION_SUPPORT`

  如果外层有事务，则加入外层事务，如果外层没有事务，则直接使用非事务方式执行。完全依赖外层的事务

- `PROPAGATION_NOT_SUPPORT`

  该传播机制不支持事务，如果外层存在事务则挂起，执行完当前代码，则恢复外层事务，无论是否异常都不会回滚当前的代码

- `PROPAGATION_NEVER`

  该传播机制不支持外层事务，即如果外层有事务就抛出异常

- `PROPAGATION_MANDATORY`

  与NEVER相反，如果外层没有事务，则抛出异常

- `PROPAGATION_NESTED`

  该传播机制的特点是可以保存状态保存点，当前事务回滚到某一个点，从而避免所有的嵌套事务都回滚，即各自回滚各自的，如果子事务没有把异常吃掉，基本还是会引起全部回滚的。





# 声明式事务控制🍀

Spring 的事务控制都是基于 AOP 的，它既可以使用编程的方式实现，也可以使用配置的方式实现，这里学习的重点是使用配置的方式实现。

使用声明式的事务控制，需要导入以下 maven 依赖，spring-tx 用于Spring 框架中的事务控制。

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-tx</artifactId>
    <version>5.2.12.RELEASE</version>
</dependency>
```



## 相关配置

引入 `bean.xml` 的约束文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/tx
        https://www.springframework.org/schema/tx/spring-tx.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">
</beans>
```



声明式事务需要使用 `<aop:advisor>` 标签

```xml
<!-- 配置数据源 -->
<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
    <property name="url" value="jdbc:mysql://localhost:3306/spring_learn"></property>
    <property name="username" value="root"></property>
    <property name="password" value="kzqkzq"></property>
</bean>

<!-- 配置事务管理器 -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"></property>
</bean>

<!-- 配置事务的通知 -->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
    <tx:attributes>
        <tx:method name="*" isolation="DEFAULT" read-only="false"/>
      	<tx:method name="find*" isolation="DEFAULT" read-only="false"/>
      	<tx:method name="transfer" isolation="DEFAULT" read-only="false"/>
    </tx:attributes>
</tx:advice>

<!-- 配置aop -->
<aop:config>
    <aop:pointcut id="pointCut" expression="execution(* kzq.spring.service..*.*(..))"/>
  	<!-- 建立切入点表达式和事务通知之间的对应关系 -->
    <aop:advisor advice-ref="txAdvice" pointcut-ref="pointCut"></aop:advisor>
</aop:config>
```



## 事务管理器

`PlatformTransactionManager` 接口是 Spring 提供的事务管理器，里面提供了我们常用的操作事务的方法

- `getTransaction`：获取事务状态信息 
- `commit`：提交
- `rollback`：回滚 

真正用于管理事务的对象则是它的实现类：

- `org.springframework.jdbc.datasource.DataSourceTransactionManager`
- `org.springframework.orm.hibernate5.HibernateTransactionManager`

```xml
<!-- 配置事务管理器 -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"></property>
</bean>
```





## 事务通知

配置事务通知需要导入 tx 名称空间和约束

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/tx
        https://www.springframework.org/schema/tx/spring-tx.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">
</beans>
```



配置事务的属性：<`tx:advice` >标签的内部的 `<tx:attributes>` 标签中

```xml
<tx:advice id="txAdvice" transaction-manager="transactionManager">
    <tx:attributes>
        <tx:method name="*" isolation="DEFAULT" read-only="false"/>
      	<tx:method name="find*" isolation="DEFAULT" read-only="false"/>
      	<tx:method name="transfer" isolation="DEFAULT" read-only="false"/>
    </tx:attributes>
</tx:advice>
```

事务通知的属性：

- method：指定配置事务的方法，方法位于切入点表达式范围内，可以使用通配符

- read-only：是否是只读事务，默认false

- isolation：指定事务的隔离级别，默认使用数据库的默认隔离级别
  - DEFAULT：
  - READ COMMITTED
  - READ UNCOMMITTED
  - REPEATABLE READ
  - SERIALIZABLE

- propagation：指定事务的传播行为，默认为REQUIRED

  - REQUIRED（默认值）：如果当前没有事务，就新建一个事务，如果已经存在一个事务中，加入到这个事务中。


  - SUPPORTS：支持当前事务，如果当前没有事务，就以非事务方式执行
  - REQUERS_NEW：新建事务，如果当前在事务中，把当前事务挂起。
  - NOT_SUPPORTED：以非事务方式执行操作，如果当前存在事务，就把当前事务挂起
  - NEVER：以非事务方式运行，如果当前存在事务，抛出异常
  - NESTED：如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行REQUIRED类似的操作。

- timeout：指定超时时间，默认值为-1，永不超时，如果有，以秒为单位进行设置。

- rollback-for：用于指定一个异常，当执行产生该异常时，事务回滚。产生其他异常，事务不回滚。默认为任何异常都回滚

- no-rollback-for：用于指定一个异常， 当产生该异常时，事务不回滚，产生其他异常时，事务回滚。默认为任何异常都回滚



## 注解配置

### XML开启声明式事务

```xml
<!-- 开启Spring对注解事务的支持 -->
<tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>
```



### 纯注解开启声明式事务

对于纯注解配置，则需要在配置类上添加 `@EnableTransactionManagement` 注解，表示开启声明式事务

```java
@Configuration
@ComponentScan("kzq.spring")
@Import({JdbcConfig.class, TransactionConfig.class})
@PropertySource("jdbc.properties")
@EnableTransactionManagement
public class SpringConfig {
  
}
```



### 配置声明式事务注解

在需要事务支持的地方使用 `@Transactional` 注解

1. 事务的传播性：`@Transactional(propagation=Propagation.REQUIRED)`

2. 事务的隔离级别：`@Transactional(isolation = Isolation.READ_UNCOMMITTED)`

3. 只读：`@Transactional(readOnly=true)`

4. 事务的超时性：`@Transactional(timeout=30)`

5. 回滚：

   指定单一异常类：`@Transactional(rollbackFor=RuntimeException.class)`

   指定多个异常类：`@Transactional(rollbackFor={RuntimeException.class, Exception.class})`



如果不同的方法需要不同的事务控制，则需要添加多个 `@Transactional` ，并传递响应的参数。

```java
// Service中所有方法采取的事务控制
@Transactional(propagation = Propagation.SUPPORTS, readOnly = true)
@Service("accountService")
public class AccountServiceImpl implements AccountService {
    @Autowired
    private AccountMapper accountMapper;

    public Account findAccountByName(String name) {
        return accountMapper.findAccountByName(name);
    }
  
  	// 当前方法采取的事务控制
		@Transactional(propagation = Propagation.REQUIRED, readOnly = false)
    public void updateAccount(Account account) {
        accountMapper.updateAccount(account);
    }
		
    // 当前方法采取的事务控制
    @Transactional(propagation = Propagation.REQUIRED, readOnly = true)
    public void transfer(String sourceName, String targetName, float money) {
        Account source = accountMapper.findAccountByName(sourceName);
        Account target = accountMapper.findAccountByName(targetName);
        source.setMoney(source.getMoney() - money);
        target.setMoney(target.getMoney() + money);
        accountMapper.updateAccount(source);
        int i = 1/0;
        accountMapper.updateAccount(target);
    }
}

```

使用这种方式，无需再配置 `<tx:advice>` 和 `<aop:config>`





# Spring-源码分析🍀

## 核心概念

### BeanDefinition

`BeanDefinition` 表示 bean 的定义，Spring 根据 `BeanDefinition` 来创建 bean 对象， `BeanDefinition` 有很多的属性用来描述 bean 。



### BeanDefinitionReader

`BeanDefinitionReader` 将配置转换成 `BeanDefinition`，再注入容器

```java
package org.springframework.beans.factory.support;


public interface BeanDefinitionReader {

	BeanDefinitionRegistry getRegistry();

	@Nullable
	ResourceLoader getResourceLoader();

	@Nullable
	ClassLoader getBeanClassLoader();

	BeanNameGenerator getBeanNameGenerator();

	int loadBeanDefinitions(Resource resource) throws BeanDefinitionStoreException;

	int loadBeanDefinitions(Resource... resources) throws BeanDefinitionStoreException;

	int loadBeanDefinitions(String location) throws BeanDefinitionStoreException;

	int loadBeanDefinitions(String... locations) throws BeanDefinitionStoreException;

}

```





直接使用 `BeanDefinition` 注入容器：

```java
public class Main {
	public static void main(String[] args) {
		AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext();
		AbstractBeanDefinition beanDefinition = BeanDefinitionBuilder.genericBeanDefinition().getBeanDefinition();
		beanDefinition.setBeanClass(User.class);
		ac.registerBeanDefinition("user", beanDefinition);
		ac.refresh();
	}
}
```





### FactoryBean

`FactoryBean` 是 Spring 所提供的一种较灵活的创建 bean 的方式，可以通过实现 `FactoryBean` 接口中的 `getObject()` 方法来返回一个对象，这个对象就是最终的 bean 对象。

`Beanfactory` 是一个 Spring 容器，是一个大型工厂它可以生产出各种各样的 bean。

`FactoryBean` 对象本身也是一个 Bean，同时它相当于一个小型工厂，可以生产出另外的 bean。

```java
public class YoruaFactory implements FactoryBean {
	
  // 返回的是 bean 对象
	@Override
	public User getObject() throws Exception {
		return new User();
	}
	
  // 返回的是 bean 类型
	@Override
	public Class<?> getObjectType() {
		return User.class;
	}

  // 返回的是 是否是单例对象
	@Override
	public boolean isSingleton() {
		return false;
	}
}
```

将该类注入容器中，上述代码实际上对应了两个 bean 对象：

1. bean id 为 `yorua ` ，bean 对象为 `getObject()` 方法所返回的 `User` 对象。

2. bean id 为 `&yorua ` ，bean 对象为 `YoruaFactory` 类的实例对象。

```java
public class Main {
	public static void main(String[] args) {
		AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext();
		AbstractBeanDefinition beanDefinition = BeanDefinitionBuilder.genericBeanDefinition().getBeanDefinition();
		beanDefinition.setBeanClass(YoruaFactory.class);
		ac.registerBeanDefinition("yorua", beanDefinition);
		ac.refresh();

		User yorua = ac.getBean("yorua", User.class);
		System.out.println(yorua);
		YoruaFactory yoruaFactory = ac.getBean("&yorua", YoruaFactory.class);
		System.out.println(yoruaFactory);
	}
}
```







### BeanFactory

`BeanFactory` 是访问 Spring 容器的最基本、也是最底层的接口，其中有很多获取单个 bean 的方法。

`BeanFactory` 将利用 `BeanDefinition` 来生成 bean 对象， `BeanDefinition` 相当于 `BeanFactory` 的原材料，bean 对象就相当于 `BeanFactory` 所生产出来的产品。

`BeanFactory` 只是一个接口，并不是 IOC 容器的具体实现，原始的 `BeanFactory` 无法支持 Spring 的许多功能，如 AOP 、Web 等。

<img src="http://store.secretcamp.cn/uPic/image-202106051743028132021060517430316228861834qaPzA4qaPzA.png" alt="image-20210605174302813" style="zoom:50%;" />



Beanfactory 的核心子接口和实现类有：

- `ListableBeanFactory`

  `ListableBeanFactory` 直接继承  `BeanFactory` ，其中有很多获取多个 bean 的方法

  <img src="http://store.secretcamp.cn/uPic/image-20210605174442756202106051744421622886282JJxIS8JJxIS8.png" alt="image-20210605174442756" style="zoom: 33%;" />

- `AutowireCapableBeanFactory`

  

- `ConfigurableBeanFactory`

  <img src="http://store.secretcamp.cn/uPic/image-20210614230229381202106142302291623682949Ewzm4WEwzm4W.png" alt="image-20210614230229381" style="zoom:33%;" />



- `AbstractBeanFactory`

  <img src="http://store.secretcamp.cn/uPic/image-20210614230630394202106142306301623683190OQvfJfOQvfJf.png" alt="image-20210614230630394" style="zoom:33%;" />

- `DefaultListableBeanFactory` 

  

  <img src="http://store.secretcamp.cn/uPic/image-202106142312489922021061423124916236835692csq2s2csq2s.png" alt="image-20210614231248992" style="zoom: 33%;" />





#### AutowireCapableBeanFactory

`AutowireCapableBeanFactory` 直接继承 `BeanFactory`，扩展了功能。

<img src="http://store.secretcamp.cn/uPic/image-20210621002222577202106210022221624206142uwOy6NuwOy6N.png" alt="image-20210621002222577" style="zoom: 50%;" />



#### ApplicationContext

`ApplicationContext` 继承自 `BeanFactory`，包含 `BeanFactory` 的所有功能，是比 `BeanFactory` 更加强大的  Spring 容器，提供了更多的有用的功能。

- 国际化（MessageSource）
- 访问资源，如URL和文件（ResourceLoader）
- 消息发送、响应机制（ApplicationEventPublisher）
- 拦截器（AOP）

![image-20210621001758706](http://store.secretcamp.cn/uPic/image-20210621001758706202106210017581624205878MWJqv8MWJqv8.png)



加载 bean 的区别：

-  `BeanFactory`：在启动的时候不会去实例化Bean，中有从容器中拿Bean的时候才会去实例化；

- `ApplicationContext`：在启动的时候就把所有的 bean 全部实例化，也可以配置 `lazy-init` 实现延迟实例化； 





#### ConfigurableApplicationContext

此接口结合了所有 `ApplicationContext` 需要实现的接口，此基础上增加了一系列配置应用上下文的功能。

<img src="http://store.secretcamp.cn/uPic/image-20210621003438578202106210034381624206878RTBgHDRTBgHD.png" alt="image-20210621003438578" style="zoom:50%;" />



#### AbstractApplicationContext

`AbstractApplicationContext` 是 `ApplicationContext` 的抽象实现类，该抽象类实现应用上下文的一些具体操作。

<img src="http://store.secretcamp.cn/uPic/image-20210621004737257202106210047371624207657nnS7QXnnS7QX.png" alt="image-20210621004737257" style="zoom: 50%;" />





#### ConfigurableBeanFactory

<img src="http://store.secretcamp.cn/uPic/image-20210621002124151202106210021241624206084FG5nYlFG5nYl.png" alt="image-20210621002124151" style="zoom: 50%;" />



#### ConfigurableListableBeanFactory

![image-20210621004101626](http://store.secretcamp.cn/uPic/image-20210621004101626202106210041011624207261o0Ek7Ro0Ek7R.png)





#### AbstractBeanFactory

<img src="http://store.secretcamp.cn/uPic/image-20210621085009625202106210850091624236609vBwDtevBwDte.png" alt="image-20210621085009625" style="zoom: 43%;" />



#### DefaultListableBeanFactory

`DefaultListableBeanFactory` 是功能最强大的 `BeanFactory` 

支持单例 Bean、支持 Bean 别名、支持父子 Beanfactory、支持 Bean 类型转化、支持 Bean 后置处理、支持 FactoryBean、支持自动装配等。

对于 `ClassPathXmlApplicationContext` 的实现，会在解析 bean 定义之前实例化一个 `DefaultListableBeanFactory` 作为核心容器。

![image-20210621004336294](http://store.secretcamp.cn/uPic/image-20210621004336294202106210043361624207416Hy6OTtHy6OTt.png)





### DefaultSingletonBeanRegistry

`DefaultSingletonBeanRegistry` 用于注册，获得，管理单例对象。

- `AliasRegistry` : 提供别名注册的接口
- `SimpleAliasRegistry`：简单地实现了 `AliasRegistry` 接口。
- `SingletonBeanRegistry`：提供单例 bean 注册的接口

<img src="http://store.secretcamp.cn/uPic/image-20210620160927752202106201609281624176568A02GQdA02GQd.png" alt="image-20210620160927752" style="zoom: 50%;" />



该类中有许多获取单例 bean 的方法，也定义了创建和获取单例 bean 过程中用到的多级缓存

```java
public class DefaultSingletonBeanRegistry extends SimpleAliasRegistry implements SingletonBeanRegistry {

	private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);

	private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<>(16);

	private final Map<String, Object> earlySingletonObjects = new ConcurrentHashMap<>(16);
  
  private final Set<String> registeredSingletons = new LinkedHashSet<>(256);
  
  // ...
  
}
```



### Aware

`Aware` 是 Spring 中的一个根接口，继承该接口的子接口有很多，但是该接口没有任何方法，所以可以理解成一个标记接口

`Aware` 类型的接口的作用就是让 bean 能够拿到 Spring 容器中的一些资源。Spring 的依赖注入特点是所有的 bean 对 Spring 容器的存在是没有意识的，但是在实际项目中，我们不可避免的要用到 Spring 容器本身提供的资源，这时候要让 bean 主动感知到 Spring 容器的存在，才能调用 Spring 所提供的资源。

常见的 Spring Aware 接口：

| Aware的子接口                  | 描述                                                 |
| :----------------------------- | :--------------------------------------------------- |
| BeanNameAware                  | 获取容器中 bean 的名称                               |
| BeanFactoryAware               | 获取当前 beanFactory ，这样可以调用容器的服务        |
| ApplicationContextAware        | 获取当前 ApplicationContext ，这样可以调用容器的服务 |
| MessageSourceAware             | 获取 Message Source 相关文本信息                     |
| ApplicationEventPublisherAware | 发布事件                                             |
| ResourceLoaderAware            | 获取资源加载器，这样获取外部资源文件                 |





### BeanPostProcessor

`BeanPostProcessor` 是一个接口，定义ー个后置处理器，就是提供一个类实现该接口，它是 Spring 所提供的一种扩展机制，可以利用该机制对 bean 进行定制化加工，在 Spring 底层源码实现中，也广泛的用到了该机制，`BeanPostProcessor` 通常也叫做 Bean 后置处理器。

```java
public interface BeanPostProcessor {

  // 初始化之前执行
	@Nullable
	default Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
		return bean;
	}

	// 初始化之后执行
	@Nullable
	default Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
		return bean;
	}

}
```



在 Spring 中还存在一些接口继承了 `BeanPostProcessor`，这些子接口在 `BeanPostProcessor` 的基础上增加了一些其他的功能。

```java
public interface InstantiationAwareBeanPostProcessor extends BeanPostProcessor {
	
  // 实例化前
	@Nullable
	default Object postProcessBeforeInstantiation(Class<?> beanClass, String beanName) throws BeansException {
		return null;
	}
	
  // 实例化后
	default boolean postProcessAfterInstantiation(Object bean, String beanName) throws BeansException {
		return true;
	}
  
  // 属性注入后，可以在这里写入逻辑对属性值进行修改
	@Nullable
	default PropertyValues postProcessProperties(PropertyValues pvs, Object bean, String beanName)
			throws BeansException {

		return null;
	}
  
  // ...
}

```



实现了这些接口的 bean 会切入到多个 bean 的生命周期中。Spring 内部扩展也经常使用这些接口，例如自动注入以及 AOP 的实现都和它们有关。

```java
@Component
public class MyBeanPostProcessor implements BeanPostProcessor {

	@Override
	public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
		System.out.println("postProcessBeforeInitialization..." + beanName + "=>" + bean);
		return bean;
	}

	@Override
	public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
		System.out.println("postProcessAfterInitialization..." + beanName + "=>" + bean);
		return bean;
	}

}
```



### BeanFactoryPostProcessor

bean factory 的后处理器，通俗说就是可以管理 bean factory 内所有的 `BeanDefinition` ，可以随心所欲的修改属性。

在 bean 实例化之前调用

```java
@FunctionalInterface
public interface BeanFactoryPostProcessor {

	/**
	 * Modify the application context's internal bean factory after its standard
	 * initialization. All bean definitions will have been loaded, but no beans
	 * will have been instantiated yet. This allows for overriding or adding
	 * properties even to eager-initializing beans.
	 * @param beanFactory the bean factory used by the application context
	 * @throws org.springframework.beans.BeansException in case of errors
	 */
	void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException;

}
```



 

## 核心容器启动

容器启动的核心方法为 `refresh()`

```java
@Override
public void refresh() throws BeansException, IllegalStateException {
  synchronized (this.startupShutdownMonitor) {
    // Prepare this context for refreshing.
    prepareRefresh();
		
    // 重要方法
    // 通知子类刷新内部bean工厂：创建BeanFactory，如果已有就销毁，没有就创建；
    // 解析xml以及扫描注解，将扫描到的bean配置属性封装到BeanDefinition对象中
    ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

    // 对 bean factory 进行一些上下文标准化配置
    prepareBeanFactory(beanFactory);

    try {
      // 允许修改BeanFactory的后处理器配置，这是留给子类的扩展方法
      // 这一阶段所有bean定义都已加载，但没有bean被实例化。
      postProcessBeanFactory(beanFactory);
			
      // 调用所有BeanFactoryPostProcessor
      invokeBeanFactoryPostProcessors(beanFactory);

      // Register bean processors that intercept bean creation.
      registerBeanPostProcessors(beanFactory);

      // 国际化相关
      initMessageSource();

      // 初始化事件广播
      initApplicationEventMulticaster();

      // 空方法，留给子类扩展
      onRefresh();

      // 注册监听事件
      registerListeners();

      // 将所有非懒加载的单实例对象实例化，并放入缓存池
      finishBeanFactoryInitialization(beanFactory);

      // 完成BeanFactory的初始化创建工作
      finishRefresh();
    }

    catch (BeansException ex) {
      // ...
    }

    finally {
      // ...
    }
  }
}
```



## bean的生命周期

Bean 生命周期描述的是 Spring 中ー个 Bean 创建过程和销过程中所经历的步骤，其中 Bean 创建过程是重点。

程序员可以利用 Bean 生命周期机制对 Bean 进行自定义加工。



获取 bean 定义 -> 实例化 -> 属性赋值 -> 初始化 -> 销毁



bean 的生命周期过程：

1. 获取 bean 定义：解析 bean 的定义并转化为 `BeanDefinition`

2. 实例化：通过构造方法反射得到一个实例化对象，在 Spring 中，可以通过  `BeanPostProcessor` 机制对实例化进行干预。

   - 如果容器中有 `InstantiationAwareBeanPostProcessor` 的实现，在实例化之前执行 `postProcessBeforeInstantiation()` 方法
   - 如果容器中有 `InstantiationAwareBeanPostProcessor` 的实现，在实例化之后执行 `postProcessAfterInstantiation()` 方法

3. 属性赋值：实例化所得到的对象，是早期 bean 对象，是指该对象中的某些属性还没有进行填充，也就是 Spring 还没有自动给某些属性赋值，属性填充就是通常说的自动注入、依赖注入。

4. 初始化：在一个对象的属性填充之后，Spring 提供了初始化机制，程序员可以利用初始化机制对 bean 进行自定义加工。

   初始化的逻辑在 `initializeBean()` 方法中

   1. 调用 `Aware` 相关功能，例如 `BeanNameAware.setBeanName()` 设置该 bean 在 Bean Factory 中的名称，
   2. 如果容器中有 `BeanPostProcessor` 的实现，执行 `postProcessBeforeInitialization()` 方法逻辑。

   2. 如果 bean 实现了 `InitializingBean` 接口，执行 `afterPropertiesSet()` 方法。

   3. 如果 bean 在配置文件中的定义包含 `init-method` 属性，执行指定的方法。

   4. 如果容器中有 `BeanPostProcessor` 的实现，执行 `postProcessAfterInitialization()` 方法逻辑。

5. 销毁：当要销毁 bean 的时候，如果 bean 实现了`DisposableBean` 接口，执行 `destroy()` 方法；如果 bean 在配置文件中的定义包含 `destroy-method` 属性，执行指定的方法。

   



### bean的解析

源码分析详细步骤：

4. 进入 `obtainFreshBeanFactory()` 方法

5. 进入 `refreshBeanFactory()` 方法，判断 beanFactory 是否存在，如果存在就销毁

6. 进入 `createBeanFactory()`方法，方法中 `new DefaultListableBeanFactory()` 创建了一个 `DefaultListableBeanFactory` 作为 `BeanFactory` 的实例

7. 进入 `loadBeanDefinitions(beanFactory)` ，正式开始加载 bean 定义的流程

8. 通过 `new XmlBeanDefinitionReader(beanFactory)` 创建了一个 `BeanDefinitionReader` 的实现

9. 进入重载方法 `loadBeanDefinitions(beanDefinitionReader)`

10. 进入 `reader.loadBeanDefinitions(configLocations)`

11. 进入 `loadBeanDefinitions(location)`，之后进入各种重载的 `loadBeanDefinitions` 方法，最终进入 `loadBeanDefinitions(new EncodedResource(resource))`

12. 进入 `doLoadBeanDefinitions(inputSource, encodedResource.getResource())` ，加载 bean 的定义

13. `doLoadDocument(inputSource, resource)` 方法将输入流转为 `Document` 对象

14.  `registerBeanDefinitions(doc, resource)` 方法，首先创建了一个`BeanDefinitionDocumentReader` 的对象，再调用其 `registerBeanDefinitions(doc, createReaderContext(resource))` 方法，将 `Document` 对象解析，并将解析后生成的 bean 对象注入到 IOC 容器中，最终该方法返回注入容器的 bean 的个数 (int)。

15. 深入分析 `documentReader.registerBeanDefinitions(doc, createReaderContext(resource))`，进入方法之中，这个方法由  `DefaultBeanDefinitionDocumentreader` 真正实现。

    进入`doRegisterBeanDefinitions(doc.getDocumentElement())` 方法，该方法含义是解析 xml 文件的内容，并将解析后的 bean 的定义注入容器。

16. `parseBeanDefinitions(root, this.delegate)` 方法解析文件，该方法将 xml 中的 bean 标签解析成 `BeanDefinitionHolder` 对象，这一步真正将 xml 解析成了 bean 对象。

    - 解析会分成默认标签的解析以及自定义标签的解析
    - `parseDefaultElement(ele, delegate)` 执行默认标签的解析
    - `delegate.parseCustomElement(ele)` 执行自定义标签的解析

17. `BeanDefinitionReaderUtils.registerBeanDefinition(bdHolder, getReaderContext().getRegistry())` 将解析后的 bean 对象注入容器，第一个参数是刚刚生成的 `BeanDefinitionHolder` 对象，第二个参数是 ioc 容器，即 `DefaultListableBeanFactory` 。



### bean的加载

以单例 bean 的加载为例

源码分析详细步骤：

1. 进入 `finishBeanFactoryInitialization(beanFactory)` 方法

2. 进入 `beanFactory.preInstantiateSingletons()` 方法，会进入 `DefaultListableBeanFactory` 的实现，会遍历所有 beanName，并获取合并后的 bean 定义 `RootBeanDefinition` 

   - 如果是 `FactoryBean` ，则进入对应流程
   - 如果不是，则进入  `getBean(beanName)` ，执行 `doGetBean()`   --->

3. 执行 `Object sharedInstance = getSingleton(beanName)` ，会先从一级缓存中尝试获取 bean

4. 判断是否是原型正在创建、是否有 ParentBeanFactory、判断是否有 dependsOn .....

5. `RootBeanDefinition mbd = getMergedLocalBeanDefinition(beanName)` 会再获取一次 mbd

   - 判断其是否是单例，如果是则调用 `getSingleton()`  ----->

     ```java
     sharedInstance = getSingleton(beanName, () -> {
       try {
         return createBean(beanName, mbd, args);
       }
       catch (BeansException ex) {
         destroySingleton(beanName);
         throw ex;
       }
     });
     ```

   - 判断其是否是原型，如果是则调用对应逻辑

6. `getSingleton()` 方法中，首先尝试从一级缓存中获取，如果没有，将当前 beanName 加入一个集合 `singletonsCurrentlyInCreation` ，该集合用于记录正在创建的 bean，该集合用于判断循环依赖。

7. 调用 `singletonObject = singletonFactory.getObject()` ，此时会调用步骤 4 中的匿名内部类的实现，也就是执行 `createBean()` 方法

8. `createBean()` 方法会获取当前 bean 对应的 class 类

   - 执行 `resolveBeforeInstantiation()` 方法，进行后处理器操作

   - 通过 `doCreateBean()` 方法开始创建 bean 对象，在 `createBeanInstance()` 方法中通过构造器反射创建 `BeanWrapper` ，再通过 `getWrappedInstance()` 方法最终获得了 bean 的早期对象。简而言之，`doCreateBean()` 通过反射创建了 bean 的早期对象。
   - 执行 `applyMergeBeanDefnitionPostProcessor()` 方法，进行后处理器操作

9. 通过 `populateBean()` 方法对 bean 进行属性注入，如果发现当前 bean 依赖另一个 bean 且该 bean 未创建，则会先去创建别的 bean

10. 调用 `initializeBean()` 方法执行初始化操作

    - `invokeAwareMethods()` 获取 beanName、ClassLoader、BeanFactory 等容器提供的资源
    - `applyBeanPostProcessorsBeforeInitialization()` 执行后处理器的初始化前逻辑
    - `invokeInitMethods()` 执行初始化方法
    - `applyBeanPostProcessorsAfterInitialization()` 执行后处理器的初始化后逻辑

11. 调用 `registerDisposableBeanIfNecessary()` 注册 bean 销毁的相关逻辑



## 三级缓存

Spring 在启动过程中，使用到了三个 map，称为三级缓存。

- `singletonObjects` 是第一级缓存，用于保存 BeanName 和创建的 bean 实例之间的关系。

- `earlySingletonObjects` 是第二级缓存，用于保存 BeanName 和创建的早期 bean 实例之间的关系。

- `singletonFactories` 是第三级缓存，用于保存 BeanName 和创建 bean 实例的工厂之间的关系。

```java
public class DefaultSingletonBeanRegistry extends SimpleAliasRegistry implements SingletonBeanRegistry {

	/** Cache of singleton objects: bean name to bean instance. */
	private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);

	/** Cache of singleton factories: bean name to ObjectFactory. */
	private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<>(16);

	/** Cache of early singleton objects: bean name to bean instance. */
	private final Map<String, Object> earlySingletonObjects = new ConcurrentHashMap<>(16);
  
  /** 保存所有已经注册了的bean */
  private final Set<String> registeredSingletons = new LinkedHashSet<>(256);
  // ...
  
}
```

Spring 首先从 `singletonObjects` 中尝试获取，如果获取不到并且对象在创建中，则尝试从`earlySingletonObjects` 中获取，如果还是获取不到并且允许从 `singletonFactories` 通过 `getObject()` 获取，则通过该方式获取。

如果获取到了，则移除对应的 `singletonFactory`，将单例对象放入到 `earlySingletonObjects`，其实就是将三级缓存提升到二级缓存。

Spring 在进行对象创建的时候，会依次从一级、二级、三级缓存中寻找对象，如果找到则直接返回。由于是初次创建，只能从三级缓存中找到（实例化阶段放进去的），创建完实例，然后将缓存放到第一级缓存中。下次循环依赖的再直接从一级缓存中就可以拿到实例对象了。



## 循环依赖

循环依赖：指 Spring 创建对象 A 的过程中，需要实例化它的依赖对象 B，但是它的依赖对象 B 最终还会依赖到 A，最终形成一个闭环。

循环依赖的类型：

1. 单例 Bean 循环依赖， setter 产生依赖（可以解决）

2. 单例 Bean 循环依赖，构造参数产生依赖（只能检测到但无法解决） 

3. 原型模式循环依赖（只能检测到但无法解决）



### 单例setter依赖

假设 A、B 是单例，setter 产生循环依赖：

 `getBean(A)`：

1. 对于单例 bean，调用 `getSingleton()` 方法
2. 在一级缓存中查询，因为当前对象未初始化过，所以一定获取不到
3. 调用 `beforeSingletonCreation()` 方法记录当前 beanName 到 `CurrentlyCreationSet`
4. 调用 `singletonFactory.getObject()` 方法，之后会进入 `createBean()` 方法进入创建 A 的流程
5. 通过 Class 拿到无参的 Constructor 构造器，进行反射创建 A 的对象
6. 反射创建完毕，此时得到一个 A 的早期对象
7. 执行 `MergeBeanDefnitionPostProcessor()` 操作，处理 `@Autowire` 和 `@Value` 等注解


10. 将早期 A 对象封装到 `ObjectFactorys` 内，即放入三级缓存
11. 执行用于依赖注入的 `populate()` 方法，发现了依赖类型 B 
12. 进行 `getBean(B)` 方法   -------> 
13. 完成依赖注入
14. 最终对象 A 加载完成，存放至一级缓存，并且将二三级缓存中有关 A 的数据清理掉



--------> `getBean(B)`：

1. 和 `getBean(A)` 的 1~5 相同，获得一个 B 的早期对象
2. 执行 `MegerBeanDefnitionPostProcessor` 操作，处理 `@Autowire` 和 `@Value` 等注解

3. 将早期 B 对象封装到 `ObjectFactorys` 内，即放入三级缓存
4. 执行用于依赖注入的 `populate ()` 方法

5. 发现了依赖类型 A ，进行 `getBean(A)` 方法
   - 获取 A 的合并后的 `BeanDefinition` 
   - 从一级缓存中获取 A ，获取不到
   - 检查 `CurrentlyCreationSet` 发现 A 正在创建中
   - 从三级缓存中获取到 A 的 `&ObjectFacotry` ，通过工厂得到早期 A 对象的实例
   - 将早期 A 对象提升到二级缓存
6. 从二级缓存中返回早期 A 对象，完成 B 对象的依赖注入。
7. 最终对象 B 加载完成，存放至一级缓存，并且将二三级缓存中有关 B 的数据清理掉



单例 setter 依赖中使用 “无参构造器” 创建了早期对象并放入缓存，这是能够解决循环依赖的关键。





### 单例构造方法依赖

假设 A、B 是单例，构造方法产生循环依赖：

 `getBean(A)`：

1. 对于单例 bean，调用 `getSingleton()` 方法
2. 在一级缓存尝试获取 A ，由于 A 尚未初始化，所以获取不到
3. 记录当前 BeanName 到 `CurrentlyCreationSet`，即将 A 放入  ----->  目前为止与情况一相同。
4. 通过 Class 拿到 Constructor 构造器，进行反射创建对象，发现构造器中有一个参数是类型 B，需要先实例化类型 B

 `getBean(B)`：

1. 对于单例 bean，调用 `getSingleton()` 方法
2. 在一级缓存尝试获取 B ，由于 B 尚未初始化，所以获取不到
3. 记录当前 BeanName 到 `CurrentlyCreationSet` ，即将 B 放入
4. 通过 Class 拿到 Constructor 构造器，进行反射创建对象，发现构造器中有一个参数是类型 A，需要先实例化类型 A

 `getBean(A)`：

1. 对于单例 bean，调用 `getSingleton()` 方法
2. 在一级缓存尝试获取 A ，由于 A 尚未初始化，所以获取不到
3. 检测到 `Creationset` 中已经存在 A 了，不能再将 A 放入，抛出异常，表示检测到了循环依赖





### 原型依赖

Spring 只会缓存单例 bean，不会缓存 “prototype” 作用域的 bean，所以这种情况下的循环依赖也无法解决。

假设 A 是原型 bean：

1. 前几步的流程与创建单例 bean 相同
2. 调用 `beforePrototypeCreation()` 方法，将当前的 beanName 加入 ThreadLocal 线程本地变量中保存起来
3. 调用 `createBean()` 方法创建对象，在调用 `populateBean()` 方法为属性赋值时，发现依赖类型 B，需要先实例化 B
4. 开始实例化 B，在为 B 的属性赋值时发现依赖对象 A，需要先实例化 A
5. 调用 `isPrototypeCurrentlyInCreation()` 发现 A 已经在 ThreadLocal 中了，抛出异常





# FAQ

## Spring采用了哪些设计模式？🍀

1. 简单工厂模式

   Spring 中的 BeanFactory 就是简单工厂模式的体现，根据传入一个唯一的标识来获得 bean 对象，但是否是在传入参数后创建还是传入参数前创建这个要根据具体情况来定。

2. 工厂方法模式

   通常由应用程序直接使用 new 创建新的对象，为了将对象的创建和使用相分离，采用工厂模式,即应用程序将对象的创建及初始化职责交给工厂对象。

3. 单例模式

   保证一个类仅有一个实例，并提供一个访问它的全局访问点。 

   在 Spring 中scope=singleton 的对象都是单例对象，Spring 中的单例模式完成了后半句话，即提供了全局的访问点BeanFactory。但没有从构造器级别去控制单例，这是因为 Spring 管理的是任意的 Java 对象。

4. 代理模式

   Spring 的 AOP 是基于代理模式




