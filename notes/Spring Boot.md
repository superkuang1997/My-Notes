# Springboot🍀

Spring Boot 是由 Pivotal 团队提供的全新框架，其设计目的是用来简化新 Spring 应用的初始搭建以及开发过程。该框架使用了特定的方式来进行配置，从而使开发人员不再需要定义样板化的配置。



## 参考文档

👉  [Springboot 官方文档](https://docs.spring.io/spring-boot/docs/current/reference/html/)

👉  [Springboot API Doc](https://docs.spring.io/spring-boot/docs/current/api/)



## Springboot的优点

1. 快速创建独立运行的 Spring 项目以及与主流框架集成

2. 使用嵌入式的 Servlet 容器，应用无需打成 WAR 包  

3. starters 自动依赖与版本控制

4. 大量的自动配置，简化开发，也可修改默认值

5. 无需配置 XML，无代码生成，开箱即用

6. 准生产环境的运行时应用监控

7. 与云计算的天然集成



## QuickStart

1. 使用 `Spring Initialize ` 构建一个新的项目

2. 选择依赖：web -> web spring

   选择依赖实际上是 `Spring Initialize ` 为我们自动导入了 maven 的相关依赖

   
   
3. 项目构建后，会自动生成一个主程序类

   ```java
   @SpringBootApplication  // 标注一个主程序类
   public class SpringbootHelloworldApplication {
   
       public static void main(String[] args) {
           // 启动spring
           SpringApplication.run(SpringbootHelloworldApplication.class, args);
       }
   
   }
   ```

4. 创建一个 Controller

   ```java
   @Controller
   public class HelloController {
       @ResponseBody
       @RequestMapping("/hello")
       public String hello() {
           return "Hello Spring Boot!";
       }
   }
   ```

   

5. 从主程序启动项目

   ```zsh
   mvn spring-boot:run
   ```

   

6. 将项目打成可执行jar包并部署

   ```shell
   mvn clean package
   java -jar target/springboot-helloworld-0.0.1-SNAPSHOT.jar
   ```

   必须要引入相关插件，否则运行 jar 包时会提示：jar中没有主清单属性

   ```xml
   <build>
       <plugins>
           <plugin>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-maven-plugin</artifactId>
           </plugin>
       </plugins>
   </build>
   ```

   



## pom.xml配置项

### 父项目

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.4.0</version>
    <relativePath/> 
</parent>
```



### 依赖

spring-boot-starter 指 springboot 场景启动器，帮我们导入了 web 模块正常运行所依赖的组件

Springboot将所有的功能场景都抽取出来，封装成 [starter](https://docs.spring.io/spring-boot/docs/current/reference/html/using.html#using.build-systems.starters) ，只需要在项目里面引入 starter，相关场景的所有依赖都会导入进来。

```xml
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
</dependencies>
```



## 自动配置



# SpringApplication🍀

## 启动

`SpringApplication` 类提供了一种方便的方式来引导从 `main()` 方法启动的 Spring 应用程序，可以委托给静态的`SpringApplication.run()` 方法。

```java
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

}
```

Spring 程序启动时，INFO 级别的日志会输出在控制台，也可以通过 `spring.main.log-startup-info` 关闭 Spring 启动时的日志输出



## 自定义banner

可以将 banner.txt 文件放置在 classpath 路径下，除此之外，还支持 jpg、png、gif 格式。

也可以在配置文件中指定 `spring.banner.image.location`





## CommandLineRunner

如果希望在 SpringApplication 启动时就运行一些代码，可以实现 `CommandLineRunner` 或者 `ApplicationRunner` 接口，这两个接口都提供一个  `run()`  方法，在 `SpringApplication.run(…)` 完成后被调用。





# 常用注解🍀

## 配置相关注解🌵

### @ConfigurationProperties

`@ConfigurationProperties`：告诉 Springboot 将本类中的所有属性和配置文件中相关的配置进行绑定

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface ConfigurationProperties {
    @AliasFor("prefix")
    String value() default "";

    @AliasFor("value")
    String prefix() default "";

    boolean ignoreInvalidFields() default false;

    boolean ignoreUnknownFields() default true;
}
```



默认从全局配置中注入数据，只有容器中的组件，才能享受到容器提供的 `@ConfigurationProperties` 功能

注解配置类：

```java
@Component
@Data
@ConfigurationProperties(prefix="database")
public class MyDatabase {
    private String name;
    private Integer port;
    private List<String> tables;
    private Map<String, Object> maps;
}
```

配置文件：

```yaml
database:
  name: mysql-dev
  port: 3306
  tables:
    - user
    - account
    - article
  maps:
    mirror: aliyun
    connect: 5
```

注入之后的 bean-id 为首字母小写的类名：myDatabase



对于有传递依赖的类也可以注入：

```java
@Data
public class MyDog {
    private String name;
    private Integer age;
}


@Component
@Data
@ConfigurationProperties(prefix = "person")
public class MyPerson {
    private Integer id;
    private String name;
    private Integer age;
    private MyDog dog;
    // ...
}
```

配置文件：

```yaml
person:
  id: ${random.int}
  name: Tom ${person.alias:TT}
  age: ${random.int(20,25)}
  dog:
    name: ${person.name}'s Rabbit
    age: 5
```





### @EnableConfigurationProperties

作用：令使用 `@ConfigurationProperties` 注解的类生效。

对于引入第三方依赖的类，无法修改其源码，自然无法在类上添加 `@Component` 之类的注解将其加入容器，此时就需要用到 `@EnableConfigurationProperties` 。





此时 `HelloServiceProperties` 并没有被注入容器，所以 `@EnableConfigurationProperties` 可以将 `HelloServiceProperties` 注入容器。

```java
@ConfigurationProperties(prefix = "service.properties")
@Data
public class HelloServiceProperties {
    private static final String SERVICE_NAME = "test-service";

    private String msg = SERVICE_NAME;

}


@Configuration
@EnableConfigurationProperties(HelloServiceProperties.class)
@ConditionalOnClass(HelloService.class)
@ConditionalOnProperty(prefix = "hello", value = "enable", matchIfMissing = true)
public class HelloServiceAutoConfiguration {

}

@RestController
public class ConfigurationPropertiesController {

    @Autowired
    private HelloServiceProperties helloServiceProperties;

    @RequestMapping("/getObjectProperties")
    public Object getObjectProperties () {
        System.out.println(helloServiceProperties.getMsg());
        return myConfigTest.getProperties();
    }
}
```



以下情况通过 `@Controller` 已经将 `HelloServiceProperties` 注入了容器，那么 `HelloServiceAutoConfiguration`  就不需要再加 `@EnableConfigurationProperties` 注解了。

```java
@ConfigurationProperties(prefix = "service.properties")
@Component
public class HelloServiceProperties {
    private static final String SERVICE_NAME = "test-service";

    private String msg = SERVICE_NAME;

    public String getMsg() {
        return msg;
    }

    public void setMsg(String msg) {
        this.msg = msg;
    }

}
```



### @EnableAutoConfiguration







### @SpringBootApplication

该注解有以下效果：

- `@Configuration`：标记当前类是一个配置类
- `@EnableAutoConfiguration`: Tells Spring Boot to start adding beans based on classpath settings, other beans, and various property settings. For example, if `spring-webmvc` is on the classpath, this annotation flags the application as a web application and activates key behaviors, such as setting up a `DispatcherServlet`.
- `@ComponentScan`: 允许 Spring 扫描并寻找其他组件



### @JsonIgnoreProperties

忽略任何未被绑定的属性，即不在 javabean 中没有的属性（自然也不应该有set、get方法）



## 测试相关注解🌵

### @SpringBootTest



## 条件相关注解🌵

### @ConditionalOnProperty

只有拥有某个属性，才会加载组件。

只有在 `application.properties` 或 `application.yml` 匹配到了 `spring.aop.auto=true` 才会加载被标注的组件，`matchIfMissing` 表示即使没有匹配到，也会加载被标注的组件，默认值是 false 。

```java
@ConditionalOnProperty(prefix = "spring.aop", name = "auto", havingValue = "true", matchIfMissing = true)
```



### @ConditionalOnClass

判断某个类是否存在于 classpath 中



### ConditionalOnMissingBean

某个 Bean 存在于应用上下文时才会加载，相反还有 `@ConditionalOnMissingBean`



### @ConditionalOnSingleCandidate

只有工厂类已经在容器中，且该工厂类只有单个产品实例时才会加载。

例如：`redisConnectionFactory` 在容器中，同时也只有一个产品 `RedisTemplate`  类型位于容器中。



# SpringBoot配置🍀

## 配置解析相关依赖

SpringBoot使用一个全局的配置文件，配置文件名是固定的；

- `application.properties`
- `application.yaml`

可以使用 properties，也可以使用 yaml 作为配置文件

配置文件的作用：修改 SpringBoot 自动配置的默认值；

1. 在 `pom.xml` 中导入配置文件处理器，配置文件进行绑定就会有提示

```xml
<!-- 生成配置文件的元数据信息，配置文件绑定时会有提示 -->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-configuration-processor</artifactId>
  <optional>true</optional>
</dependency>
```



2. 编写配置文件

   ```yaml
   # application.yaml
   
   database:
     name: mysql
     port: 3306
     tables:
       - user
       - account
       - article
     maps:
       mirror: aliyun
       connect: 5
   ```

   



## 配置文件占位符

1. 随机数

2. 获取之前配置的值
3. 获取之前配置的值，如果不存在则使用默认值

```yaml
person:
  id: ${random.int}
  name: Tom ${person.alias:TT}
  age: ${random.int(20,25)}
  dog:
    name: ${person.name}'s Rabbit
    age: 5
```



## 多环境配置文件

Profile是Spring对不同环境提供不同配置功能的支持，可以通过激活、指定参数等方式快速切换环境



### 多profile文件形式

格式： `application-{profile}.properties` 或 `application-{profile}.yaml`

优先级：

- 默认配置文件 `application.yaml` 任何时候都会被加载

- `spring.profiles.active` 指定当前环境，与默认配置文件同时生效
- 同名配置项，当前环境配置文件优先

```yaml
# application.yaml
spring:
  profiles:
    active: dev  # 当前激活的环境是dev
```

```yaml
# application.dev.yaml
database:
  name: mysql-dev
  port: 3306
  tables: { user, account, article }
  maps: { mirror: aliyun, connect:5 }
```

```yaml
# application.stage.yaml
database:
  name: mysql-stage
  port: 3307
  tables: { user, account, article }
  maps: { mirror: aliyun, connect:5 }
```



### 多profile文档块形式

```yaml
# application.yaml
spring:
  profiles:
    active: dev
    
---
server: 8080
spring:
  profiles:dev
  
---
server: 8081
spring:
  profiles:test
```



### 项目参数配置形式

- 命令行： `--spring profiles.active=dev` 

- 配置文件： `spring.profiles.active=dev` 

- jvm参数： `-Dspring.profiles.active=dev`



## 配置文件加载位置

springboot 启动会扫描以下位置的 `application.properties` 或 `application.yml` 文件作为 springboot 的默认配置文件

- classpath：项目目录
  - `/classpath`
  - `/classpath/config`

- current：当前目录

  - `/current`
  - `/current/config`

  

以上是按照 优先级从高到低 的顺序，所有位置的文件都会被加载，高优先级配置 内容会 覆盖低优先级配置 内容。

也可以在部署项目时手动指定配置路径，这时指定的配置中，与原配置相同信息的会覆盖，不同的信息会互补。

```sh
java -jar springboot-helloworld-0.0.1-SNAPSHOT.jar --spring.config.location=/path/to/profile
```



## 外部配置文件加载

SpringBoot也可以从以下位置加载配置，优先级从高到低；高优先级的配置覆盖低优先级的配置，所有配置会形成互补。

1. 默认配置

2. `@Configuration` 上的 `@PropertySource` 注解

3. 配置文件 `application.properties` 

   - jar包外部的 `application-{profile}.properties` 或 `application.yml` (带spring.profile) 配置文件

   - jar包内部的 `application-{profile}.properties` 或 `application.yml` (带spring.profile) 配置文件

   - jar包外部的 `application.properties` 或 `application.yml` (不带spring.profile)配置文件

   - jar包内部的 `application.properties` 或 `application.yml` (不带spring.profile)配置文件

4. `RandomValuePropertySource` 配置的 `random.*` 属性值

5. OS 环境变量

6. Java 系统属性 `System.getProperties()`

7. 来自 `java:comp/env` 的 JNDI 属性

8. 命令行参数

   ```sh
   java -jar target/springboot-helloworld-0.0.1-SNAPSHOT.jar --server.port=8088
   ```

   



# 日志🍀

## 日志格式

- Date
- Log Level
- Process ID
- Separator
- Thread name
- Logger name：通常是资源类名
- Log message

```
2019-03-05 10:57:51.112  INFO 45469 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/7.0.52
2019-03-05 10:57:51.253  INFO 45469 --- [ost-startStop-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2019-03-05 10:57:51.253  INFO 45469 --- [ost-startStop-1] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 1358 ms
2019-03-05 10:57:51.698  INFO 45469 --- [ost-startStop-1] o.s.b.c.e.ServletRegistrationBean        : Mapping servlet: 'dispatcherServlet' to [/]
2019-03-05 10:57:51.702  INFO 45469 --- [ost-startStop-1] o.s.b.c.embedded.FilterRegistrationBean  : Mapping filter: 'hiddenHttpMethodFilter' to: [/*]
```





## 日志输出

| `logging.file.name` | `logging.file.path` | Example    | Description                            |
| :------------------ | :------------------ | :--------- | :------------------------------------- |
| (none)              | *(none)*            |            | Console only logging.                  |
| Specific file       | *(none)*            | `my.log`   | 日志文件会保存在当前目录或一个相对路径 |
| (none)              | Specific directory  | `/var/log` | 日志文件会保存在指定目录               |



## 日志周转

| Name                                                   | Description                                |
| :----------------------------------------------------- | :----------------------------------------- |
| `logging.logback.rollingpolicy.file-name-pattern`      | 文件名的模式                               |
| `logging.logback.rollingpolicy.clean-history-on-start` | 应用启动时是否清除日志                     |
| `logging.logback.rollingpolicy.max-file-size`          | 存档前日志文件的最大大小                   |
| `logging.logback.rollingpolicy.total-size-cap`         | 日志归档文件在被删除之前所能容纳的最大大小 |
| `logging.logback.rollingpolicy.max-history`            | 日志保存的天数（默认7天）                  |



# Web开发🍀

## 静态资源处理

👉 [官方文档](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-mvc-static-content)

SpringBoot 对静态资源的自动配置在 `Webmvcautoconfiquration` 类中

寻找静态资源时，会从以下路径查找

```
"classpath:/META‐INF/resources/"
"classpath:/resources/"
"classpath:/static/"
"classpath:/public/"
```

访问：当前项目的根路径 + 静态资源路径



### 资源路径

可以自定义静态资源的路径，但默认的静态资源路径都将不可用。

```properties
# application.properties
spring.web.resources.static-locations=classpath:/test/
```



### 访问路径

由于 Springboot2 之后静态资源也经过拦截器，所以需要额外配置

所以可以自定义静态资源的访问路径，在拦截器中配置白名单，以避免静态资源被拦截，

现在所有的静态资源都要经过 `/res/` 路由

```properties
# application.properties
spring.mvc.static-path-pattern=/res/**
```





## webjars

[webjars](https://www.webjars.org/) 是将客户端资源（JavaScript，Css等）打成jar包文件，通过maven等构件工具对资源进行统一依赖管理。



所有 `/webjars/**` 路径下的请求 ，会在 `classpath:/META-INF/resources/webjars/` 寻找资源

```
http://localhost:8080/webjars/jquery/3.5.1/jquery.js
```





## 欢迎页面

👉 [官方文档](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-mvc-welcome-page)

配置了欢迎页面后，访问项目根路径会直接展示欢迎页面

- 欢迎页为所有静态文件路径下的 `index.html`
  - 不能配置 `spring.mvc.static-path-pattern` 否则欢迎页面会失效
- Controller 处理的 /index







## 自定义类型转换器

将字符串类型根据具体规则转变为 `Account` 类型

```java
@Configuration
public class WebConfig {
    @Bean
    public WebMvcConfigurer webMvcConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void addFormatters(FormatterRegistry registry) {
                registry.addConverter(new Converter<String, Account>() {

                    @Override
                    public Account convert(String source) {
                        Account account = new Account();
                        String[] split = source.split("|");
                        account.setCardNumber(split[0]);
                        account.setCardNumber(split[1]);
                        return account;
                    }
                });
            }
        };
    }
}
```



## 拦截器🍀



### 放行静态资源

1. 配置每个静态资源的位置

   ```java
   @Configuration
   public class MyMvcConfig implements WebMvcConfigurer {
       @Override
       public void addInterceptors(InterceptorRegistry registry) {
           registry.addInterceptor(new LoginInterceptor())
                   .addPathPatterns("/**")  // 所有请求都被拦截，包括静态资源
                   .excludePathPatterns("/login", "/css/**", "/fonts/**", "/images/**", "/js/**");
       }
   }
   ```

   

2. 设置访问前缀，配置静态资源文件夹

   先设置静态资源访问前缀，对应的，所有静态资源必须通过前缀访问

   ```properties
   # application.properties
   spring.mvc.static-path-pattern=/static
   ```

   设置拦截器排除路径

   ```java
   @Configuration
   public class MyMvcConfig implements WebMvcConfigurer {
       @Override
       public void addInterceptors(InterceptorRegistry registry) {
           registry.addInterceptor(new LoginInterceptor())
                   .addPathPatterns("/**")  // 所有请求都被拦截，包括静态资源
                   .excludePathPatterns("/login", "/static/**);
       }
   }
   ```

   



# 视图解析与模板引擎🍀

Springboot 默认打包方式是 jar 包，而 JSP 不支持在 jar 包内编译，所以 Springboot 默认不支持 JSP





# 开发效率工具🍀

## Lombok

1. 在 maven 中引入 lombok 相关依赖

   ```xml
   <dependency>
       <groupId>org.projectlombok</groupId>
       <artifactId>lombok</artifactId>
   </dependency>
   ```

   

2. 还需要下载 Idea 的 lombok 插件



### 简化JavaBean开发



### 简化日志开发



## Developer Tools

👉  [官方文档](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-devtools)

devtools 会监控所有 classpath 中的修改，并实现热重启（Hot Restart）

在 IDEA 中触发热重启： “构建” 项目

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
</dependency>
```



热重启的原理是 Spring 使用两个类加载器启动项目，第一个用来加载不会改变的 jar 包中的类（第三方），第二个用来加载开发者编写的类，当触发热重启时，会作废第二个类加载器，创建新的类加载器，但是第一个类加载器仍然复用，所以 “热重启” 比 “冷开始” 速度更快。

默认情况下，静态资源不会触发 “热重启”，但会触发 “热加载”。

可以修改配置文件，自定义触发热重启的范围：

```properties
# 以下路径修改不触发热重启
spring.devtools.restart.exclude=static/**,public/**

# 追加不触发热重启的范围
spring.devtools.restart.additional-exclude=hello/**

# 追加触发热重启的范围
spring.devtools.restart.additional-paths=world/**

# 禁用热重启
spring.devtools.restart.enabled=false
```



## JRebel

如果热重启的速度不能达到要求，可以使用 JRebel，它可以在重启时动态修改类，使得重启项目的速度更快，这被称为 “热加载”。





## Configuration Processor





 



## Spring Initailizr

使用 Spring Initailizr 创建后工程的项目结构如下：

- mvnw：maven wrapper script，它可以在没有安装 maven 或者 maven 版本不兼容的条件下运行 maven 的命令

<img src="http://store.secretcamp.cn/uPic/image-20210516214427624202105162144271621172667FHOtNLFHOtNL.png" alt="image-20210516214427624" style="zoom:50%;" />





