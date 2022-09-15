# 单元测试🌵

单元测试是编写测试代码，用以检测特定的、明确的功能。单元测试只针对功能点进行测试，不包括对业务流程正确性的测试。



# Junit🌵

## Junit4

### 概念

👉 [官方文档](https://github.com/junit-team/junit4/wiki)

### 相关注解

```java
@BeforeClass 全局只会执行一次，而且是第一个运行
@Before 在测试方法运行之前运行
@Test 测试方法
@ClassRule
@After 在测试方法运行之后允许
@AfterClass 全局只会执行一次，而且是最后一个运行
@Ignore 忽略此方法
@FixMethodOrder
```





## Junit5

### 概念

👉 [官方文档](https://junit.org/junit5/docs/current/user-guide/#overview-what-is-junit-5)

Spring Boot 2.2.0 版本开始引入 JUnit 5 作为单元测试默认库

作为最新版本的 Junit框架，Junit5 与之前版本的 Junit 框架有很大的不同。由三个不同子项目的几个不同模块组成。

- Junit Platform：Junit Platform是在JVM上启动测试框架的基础，不仅支持Junit自制的测试引擎，其他测试引擎也都可以接入。

- Junit Jupiter：Junit Jupiter提供了JUnit5的新的编程模型，是Junit5新特性的核心。内部 包含了一个**测试引擎**，用于在Junit Platform上运行。

- Junit Vintage：由于JUint已经发展多年，为了照顾老的项目，Junit Vintage 提供了兼容 JUnit4.x 、Junit3.x 的测试引擎。

<img src="http://store.secretcamp.cn/uPic/image-20210623202208657202106232022081624450928WmeLiQWmeLiQ.png" alt="image-20210623202208657" style="zoom:50%;" />



### 相关注解

```
@Test：表示方法是测试方法。但是与JUnit4的@Test不同，他的职责非常单一不能声明任何属性，拓展的测试将会由Jupiter提供额外测试
@ParameterizedTest：表示方法是参数化测试，下方会有详细介绍
@RepeatedTest：表示方法可重复执行，下方会有详细介绍
@DisplayName：为测试类或者测试方法设置展示名称
@BeforeEach：表示在每个单元测试之前执行
@AfterEach：表示在每个单元测试之后执行
@BeforeAll：表示在所有单元测试之前执行
@AfterAll：表示在所有单元测试之后执行
@Tag：表示单元测试类别，类似于JUnit4中的@Categories
@Disabled：表示测试类或测试方法不执行，类似于JUnit4中的@Ignore
@Timeout：表示测试方法运行如果超过了指定时间将会返回错误
@ExtendWith：为测试类或测试方法提供扩展类引用，类似于@RunWith
```



## 断言 Assert

断言（Assertions）是测试方法中的核心部分，用来对测试需要满足的条件进行验证。

断言方法都是 `org.junit.jupiter.api.Assertions` 的静态方法。

### 单值检测

| 方法            | 说明                                 |
| --------------- | ------------------------------------ |
| assertEquals    | 判断两个对象或两个原始类型是否相等   |
| assertNotEquals | 判断两个对象或两个原始类型是否不相等 |
| assertSame      | 判断两个对象引用是否指向同一个对象   |
| assertNotSame   | 判断两个对象引用是否指向不同的对象   |
| assertTrue      | 判断给定的布尔值是否为 true          |
| assertFalse     | 判断给定的布尔值是否为 false         |
| assertNull      | 判断给定的对象引用是否为 null        |
| assertNotNull   | 判断给定的对象引用是否不为 null      |



### 数组断言



### 组合断言



### 异常断言



### 超时断言



### 快速失败



## 假设 Assume

JUnit 5 中的假设类似于断言，不同之处在于不满足的断言会使得测试方法失败，而不满足的前置条件只会使得测试方法的执行终止。

前置条件可以看成是测试方法执行的前提，当该前提不满足时，就没有继续执行的必要。



## 参数化测试

参数化测试是 Junit5 很重要的一个新特性，它使得用不同的参数多次运行测试成为了可能，也为我们的单元测试带来许多便利。

利用 `@ValueSource` 等注解，指定入参，我们将可以使用不同的参数进行多次单元测试，而不需要每新增一个参数就新增一个单元测试，省去了很多冗余代码。

`@ParameterizedTest`：声明是一个参数化测试

`@ValueSource`：为参数化测试指定入参来源，支持八大基础类以及 String 类型、Class类型

`@NullSource`：表示为参数化测试提供一个 null 的入参

`@EnumSource`：表示为参数化测试提供一个枚举入参

`@CsvFileSource`：表示读取指定 csv 文件内容作为参数化测试入参

`@MethodSource`：表示读取指定方法的返回值作为参数化测试入参（方法返回需要是一个流）



# Spring-test🌵

## 问题分析

观察以下测试类：

```java
public class AccountTest {
  
    AccountService accountService;
  
    @Before
    public void init() {
        ApplicationContext ac = new AnnotationConfigApplicationContext(SpringConfig.class);
        accountService = ac.getBean("accountService", AccountService.class);
    }

    @Test
    public void testFindAll() {
        List<Account> accounts = accountService.findAllAccount();
        for (Account account： accounts) {
            System.out.println(account);
        }
    }

}
```

当运行单元测试时，每次都需要创建容器，事实上创建容器只是为了获得 accountService，如果能使 accountService 自动注入，就可以少写代码，提高单元测试的效率。

```java
public class AccountTest {
  	@Autowired
    AccountService accountService; 
		// ...
}
```



上述想法仅通过 junit 是无法实现的，因为 junit 自身不知道项目是否使用了 spring框架，所以 junit 运行时无法创建 spring 的ioc 容器，`@Autowired` 写了也白写，无法实现自动注入。



junit 中集成了一个 main 方法，该方法就会判断当前测试类中哪些方法有 `@Test` 注解，junit会让有 `@Test` 注解的方法执行。但是，spring-test 为 Spring 整合 junit 提供了一些方法。



解决方法：

1、导入 spring 整合 junit 的 maven 坐标

2、使用 junit 提供的  `@RunWith` 注解，将原有的 main 方法替换成 spring 提供的 main 方法



## 依赖配置

在 pom.xml 中引入依赖项

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.2.15.RELEASE</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
```

> 注意 spring-test 要和 spring-context 版本一致，且 junit 的版本大于等于4.1.2



## 简单配置

先使用 `@RunWith` 注解替换原有运行器，再使用 `@ContextConfiguration` 指定 ioc 配置文件

`@RunWith` 传入固定参数 `SpringJUnit4ClassRunner.class` 

`@ContextConfiguration` 属性：

- locations： 用于指定配置文件的位置，如果是类路径下，需要使用 `classpath:` 

- classes： 用于指定注解的类，当不使用xml配置时，传入注解类的字节码文件。

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations= {"classpath:bean.xml"})
public class AccountNewTest {
    @Autowired
    AccountService accountService;

}
```





## 测试框架的组成部分

- `TestContext`： 一个测试方法对应一个 `TestContext`，由 `TestContextManager` 管理。
- `TestExecutionListener`：测试方法或类上常会有一些注解，对其的解析操作交由对应的 `TestExecutionListener` 执行，比如事务管理、依赖注入等。
- `TestContextManager`：一个测试类对应一个 `TestContextManager`，用于管理 `TextContext` ，并触发注册的`TextExecutionListener` 去干活。
- `SmartContextLoader`：用于加载 `ApplicationContext` ，提供对 component classes、active bean definition profiles、test property sources、context hierarchies、WebApplicationContext 的支持
- `TestContextBootStrappers`：为 `TestContextManager` 加载各种 `TestExecutionListener` 的实现，并为当前测试方法创建`TestContext` 。



## Bootstrapper

`TestContextBootstrapper` 定义了一套 Spring 测试框架启动相关的 SPI ，`TestContextManager` 可以使用 `TestContextBootstrapper` 去加载 `TestExecutionListener` 形成每个单元测试的 `TestContext` 。

可以自定义 `TestContextBootstrapper` ，然后使用 `@BootstrapWith` 指定这个启动器。

Spring-Test 会自动注册所有默认的  `TestExecutionListener` ，配置文件在 `org.springframework.test` 的 `META-INF/spring.factories`



## 测试生命周期事件

Spring-test 5.2 之后引入了 `EventPublishingTestExecutionListener` ，可以识别以下注解

- `@BeforeTestClass`
- `@PrepareTestInstance`
- `@BeforeTestMethod`
- `@BeforeTestExecution`
- `@AfterTestExecution`
- `@AfterTestMethod`
- `@AfterTestClass`



## 事务管理

在 Spring-test 中，事务默认由 `TransactionalTestExecutionListener`  管理，

用 `@Transactional` 标记一个测试方法，会让这个方法运行在事务的上下文中，并在事务完成后自动回滚。

`@Transactional` 作用在方法上，但是不能作用在测试生命周期方法上（如被 `@BeforeTestClass` 修饰的方法）。



## 测试环境整合

### 整合Junit4

Spring 通过自定义的 Runner 提供与 Junit 框架整合的能力，提供了一个 `SpringJUnit4Runner` 。

```java
// @RunWith(SpringRunner.class) 也可以
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"classpath:bean.xml"})
public class SpringUnitTest01 {
		// ...
}
```



### 整合Junit5

Spring提供了一个 `SpringExtension`  ，可以实现基于 Junit Jupiter 的标准单元，并提供了与 Spring 整合的能力。

```java
@ExtendWith(SpringExtension.class)
@ContextConfiguration(locations = "classpath:bean.xml")
public class SpringTestBase {
		// ...
}
```



也可以使用整合后的注解 `@SpringJUnitConfig`

```java
@SpringJUnitConfig(locations = "classpath:bean.xml")
public class SpringTestBase {
		// ...
}
```





# Spring-test注解🌵

## 通用注解

### @BootstrapWith



### @ContextConfiguration

作用在类上，决定如何加载配置



### @WebAppConfiguration

作用在类上，声明加载的 `ApplicationContext` 是一个 `WebApplicationContext` ，web 应用的默认路径为 `file:src/main/webapp`





### @ActiveProfiles

作用在类上，决定应该加载哪一个 profile 

```java
@ContextConfiguration(locations = "classpath:bean.xml")
@ActiveProfiles({"dev", "stage"}) 
class DeveloperIntegrationTests {
    // class body...
}
```





### @TestPropertySource

作用在类上，用于指定测试配置文件

```java
@ContextConfiguration(locations = "classpath:bean.xml")
@TestPropertySource("/test.properties") 
class MyIntegrationTests {
    // class body...
}
```





### @DirtiesContext

`@DirtiesContext` 可以保证每个 test case 的执行上下文的独立性、隔离性





### @TestExecutionListeners

作用在类上，在测试类中，一些注解例如 `@Autowire` 是由 各种 `TestExecutionListener` 解析的，`@TestExecutionListeners` 的作用就是引入不同的 `TestExecutionListener` 。

该注解允许我们手动引入自定义的 `TestExecutionListener` ， 要注意的是手动引入只会引入指定过的，默认 `TestExecutionListener` 不会被自定引入，除非显示引入了。

```java
@ContextConfiguration
@TestExecutionListeners({
    MyCustomTestExecutionListener.class,
    ServletTestExecutionListener.class,
    DirtiesContextBeforeModesTestExecutionListener.class,
    DependencyInjectionTestExecutionListener.class,
    DirtiesContextTestExecutionListener.class,
    TransactionalTestExecutionListener.class,
    SqlScriptsTestExecutionListener.class
})
class MyTest {
    // class body...
}
```

显然这样很麻烦，更好的方式是设置 Merge 模式：

```java
@ContextConfiguration
@TestExecutionListeners(
    listeners = MyCustomTestExecutionListener.class,
    mergeMode = MERGE_WITH_DEFAULTS
)
class MyTest {
    // class body...
}
```



### @Commit

`@Commit` 表示应在测试方法完成后提交事务性测试方法的事务，作用等价于 `@Rollback(false)`



### @Rollback

`@Rollback(true)` 表示测试方法完成后事务应该被回滚。



### @Before/AfterTransaction



## Spring JUnit 4 注解

### @IfProfileValue

为特定的测试环境启动测试

```java
@IfProfileValue(name="java.vendor", value="Oracle Corporation") 
@Test
public void testProcessWhichRunsOnlyOnOracleJvm() {
    // ...
}
```



### @ProfileValueSourceConfiguration



### @Timed



### @Repeat



## Spring JUnit Jupiter注解

### @SpringJUnitConfig



### @SpringJUnitWebConfig





# MockMvc🌵

待学习



# Springboot-test🌵

## 引入依赖

Springboot 的单元测试支持由 `spring-boot-test` 以及  `spring-boot-test-autoconfigure` 支持，可以直接引入场景启动器 `spring-boot-starter-test` 。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
</dependency>
```



## 注解配置





## 基于Junit4

Springboot-test 默认依赖 Junit5，如果要使用 Junit4，有两种方法：

1. 引入 Junit-vintage

   SpringBoot 2.4 以上版本移除了默认对 Vintage 的依赖，如果需要兼容 junit4 则必须手动引入

   Junit-vintage 依赖了 Junit4

   ```xml
   <dependency>
       <groupId>org.junit.vintage</groupId>
       <artifactId>junit-vintage-engine</artifactId>
       <scope>test</scope>
       <exclusions>
           <exclusion>
               <groupId>org.hamcrest</groupId>
               <artifactId>hamcrest-core</artifactId>
           </exclusion>
       </exclusions>
   </dependency>
   ```

   



2. 直接引入 Junit4

   ```xml
   <dependency>
       <groupId>junit</groupId>
       <artifactId>junit</artifactId>
       <version>4.12</version>
       <scope>test</scope>
   </dependency>
   ```

   

   

3. 测试类：

   这里的 `@Test` 是 `org.junit.Test` ，且必须要配合 `@RunWith(SpringRunner.class)`

   ```java
   @SpringBootTest(classes = DemoApplicationOne.class)
   @RunWith(SpringRunner.class)
   public class Junit4Test {
       @Autowired
       ApplicationContext ctx;
   
       @Test
       public void test() {
           for (String bean : ctx.getBeanDefinitionNames()) {
               System.out.println(bean);
           }
       }
   }
   ```

   





## 基于Junit5

类上只需要标注 `@SpringBootTest` 即可，注意 `@Test` 是 `org.junit.jupiter.api.Test`

```java
@SpringBootTest
public class BeanTest {
    @Autowired
    ApplicationContext ac;

    @Test
    public void testAC() {
        for (String bean： ac.getBeanDefinitionNames()) {
            System.out.println(bean);
        }
    }
}
```









# 线上问题定位🌵

## top

先用 top 命令查看系统运行情况

- 使用交互命令数字 1 查看每个 CPU 的性能数据。
- 交互命令 H 查看每个线程的性能信息。



## jstat

jstat 可以查看 GC 情况

```sh
sudo /opt/java/bin/jstat -gcutil 31177 1000 5
```





## jstack

jstack 命令可以 dump 线程快照

```sh
sudo -u admin /opt/taobao/java/bin/jstack 31177 > /home/tengfei.fangtf/dump17
```





# 性能测试

性能测试指通过自动化的测试工具模拟多种正常、峰值以及异常负载条件来对系统的各项性能指标进行测试。性能测试是总称，通常细分为：

1. 基准测试： 在给系统施加较低压力时，查看系统的运行状况并记录相关数做为基础参考
2. 负载测试： 是指对系统不断地增加压力或增加一定压力下的持续时间，直到系统的某项或多项性能指标达到安全临界值，例如某种资源已经达到饱和状态等 。此时继续加压，系统处理能力会下降。
3. 压力测试： 超过安全负载情况下，不断施加压力（增加并发请求），直到系统崩溃或无法处理任何请求，依此获得系统最大压力承受能力。
4. 稳定性测试： 被测试系统在特定硬件、软件、网络环境下，加载一定业务压力（模拟生产环境不同时间点、不均匀请求，呈波浪特性）运行一段较长时间，以此检测系统是否稳定。





## 压力测试

假设系统的某个接口能够支持 2 万的 QPS，由于应用部署在多台机器上，必须先要知道该接口在单机上能支持多少 QPS，如果单机能支持 1K QPS，那么至少需要 20 台机器才能支持 2 万的 QPS。

