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





# Spring IOC🍀

## IOC的概念

控制反转（Inversion of Control，IOC），是面向对象编程中的一种设计原则，可以用来减低计算机代码之间的耦合度。

通过控制反转，对象在被创建的时候，由一个调控系统内所有对象的外界实体将其所依赖的对象的引用传递给它。也可以说，依赖被注入到对象中。

使用 IOC 之前，创建对象的主动权和创建时机是由自己把控的，现在这种权力转移到第三方，比如转移交给了 IOC 容器。对象 A 获得依赖对象 B 的过程，由主动行为变为了被动行为，控制权颠倒过来了，这就是 “控制反转” 的由来，即获得依赖对象的过程反转了。

IOC 最常见的实现方式叫做依赖注入（Dependency Injection，DI）。

反转前：

<img src="http://store.secretcamp.cn/uPic/image-202103110902442812021031109024416154245645pDLYb5pDLYb.png" alt="image-20210311090244281" style="zoom: 50%;" />

反转后：

<img src="http://store.secretcamp.cn/uPic/image-20210311090309549202103110903091615424589tPAKOitPAKOi.png" alt="image-20210311090309549" style="zoom:47%;" />



## 核心容器

IOC 相关的代码位于 `org.springframework.beans` 以及 `org.springframework.context`

`BeanFactory` 是一个基本接口，提供了配置框架的基本功能，而 `ApplicationContext` 是其超集，提供了更多的功能。

`ApplicationContext` 代表了核心容器，并负责实例化、配置、装配各种 bean，通过读取配置的方式来完成以上的行为，读取配置的方式主要有三种：

1. XML
2. 注解
3. Java 编码





`BeanFactory` 是 Spring 容器中的顶层接口，`ApplicationContext` 是它的子接口。

`ApplicationContext` 有三个常用实现类

- `ClassPathXmlApplicationContext`：可以加载类路径下的配置文件，要求配置文件必须在类路径下。

- `FileSystemXmlApplicationContext`：可以加载磁盘任意路径下的配置文件（必须有访问权限） 

- `AnnotationConfigApplicationcontext`：使用注解配置容器对象时，需要使用此类来创建 Spring 容器，传递的参数是配置类，所以仅在纯注解配置的情况下使用。





## Bean定义

Spring Bean 就是被 Spring 容器所管理的 Java 对象

在基于 XML 的配置中， `bean.xml` 为 Spring 容器管理 bean 提供元数据



## 实例化Bean对象

### 默认构造函数创建

在 spring 的配置文件中使用 bean 标签，配置 id 和 class 属性之后，且没有其他属性和标签时，采用的就是默认构造函数创建 bean对象，此时如果类中没有默认构造函数，则对象无法创建。

```xml
<bean id="accountService01" class="kzq.spring.service.impl.AccountServiceImpl"></bean>
```





### 静态工厂方法

使用工厂中的静态方法创建对象，使用某个类中的静态方法创建对象，并存入spring容器

```xml
<bean id="accountService03" class="kzq.spring.factory.StaticFactory" factory-method="getAccountService"></bean>
```







### 实例工厂方法

使用普通工厂中的方法创建对象，使用某个类中的方法创建对象，并存入 spring 容器

```xml
<!-- 工厂-->
<bean id="instanceFactory" class="kzq.spring.factory.InstanceFactory"></bean>
<!-- 使用普通工厂中的方法创建对象 -->
<bean id="accountService02" factory-bean="instanceFactory" factory-method="getAccountService"></bean>
```





## 引入配置文件

可以使用 `<context:property-placeholder>` 标签引入其他配置文件，再使用 SpEL 表达式引用

```xml
<context:property-placeholder location="classpath:jdbcConfig.properties"/>
```



# Spring配置🍀

# XML依赖注入🍀

依赖注入（Dependency Injection，DI）是 Spring 框架核心 IOC 的具体实现。

Spring项目中，程序在编写时，通过控制反转，把对象的创建交给了Spring，但是代码中不可能出现没有依赖的情况。

IOC 解耦只是降低代码之间的的依赖关系，但不会消除。例如，业务层仍会调用持久层的方法，那这种业务层和持久层的依赖关系，在使用 Spring 之后，就交由 Spring 来维护。

简单的说，就是等待 Spring 框架将持久层对象传入业务层，而不用我们自己去获取。



## XML配置

### 基本格式

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="..." class="...">  
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <bean id="..." class="...">
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions go here -->

</beans>
```



### 构造函数注入

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



优点：获取 bean 对象时，注入数据是必须的操作，否则对象无法创建成功。

缺点：改变了 bean 对象的实例化方式，使我们在创建对象时，必须要提供所有参数，如果用不到这些数据，也必须提供。



### set方法注入

使用 set 方法注入，必须要在实体类中先生成 set 方法

标签中的属性：

- name：与 set 方法的方法名有关，setXXX，则 name=XXX
- value：用于提供基本类型和string
- ref：用于指定其他的 bean 类型数据，指的是在 Spring 容器中存在的 bean 对象的 id



优点：创建对象时没有明确的限制，可以直接使用默认构造函数

缺点：如果有某个成员必须有值，set方法无法保证





## 注入类型

### 值类型

```xml
<bean id="birth" class="java.util.Date"></bean>

<bean id="accountService02" class="kzq.spring.service.implement.AccountServiceImpl02">
    <property name="name" value="zsh"></property>
    <property name="age" value="23"></property>
    <property name="birthday" ref="birth"></property>
</bean>
```



### 引用类型

```

```



### 集合类型

用于 List 结构集合注入的标签：list、array、set

用于 Map 结构集合注入的标签：map、props

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



### null类型

```xml
<bean class="ExampleBean">
    <property name="email">
        <null/>
    </property>
</bean>
```

等价于

```java
exampleBean.setEmail(null);
```







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
- `init-method`：指定类中的初始化方法名称。 
- `destroy-method`：指定类中销毁方法名称。
- `primary`：表示一个 bean 是一个主 bean，如果容器中有多个同类型组件，如果没有指定 id 注入则优先选择有 primary 属性的组件。
- `lazy-init` ：是否懒加载，对于原型 bean 该属性不起作用





### scope

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

- `request`：每一次 http 请求都会产生一个新的 bean，该 bean 仅在当前 http request 内有效
- `session`：每一次 http 请求都会产生一个新的bean，该bean仅在当前 http session 内有效。
- `global-session`：应用在集群环境，如果没有集群环境，那么就相当于 session



### depends-on

```xml
<bean id="beanOne" class="ExampleBean" depends-on="manager accountDao">
    <property name="manager" ref="manager" />
</bean>

<bean id="manager" class="ManagerBean" />
<bean id="accountDao" class="x.y.jdbc.JdbcAccountDao" />
```



### lazy-init



### autowired



### parent







# 容器扩展点🍀

## BeanPostProcessor

 `BeanPostProcessor` 接口定义了回调方法，可以实现这些回调方法来提供自己的实例化逻辑、依赖项解析逻辑等。

```java
@Component
public class ExampleBeanPostProcessor implements BeanPostProcessor {

    public Object postProcessBeforeInitialization(Object bean, String beanName) {
        System.out.println("before Bean '" + beanName + "' created : " + bean.toString());
        return bean;
    }

    public Object postProcessAfterInitialization(Object bean, String beanName) {
        System.out.println("after Bean '" + beanName + "' created : " + bean.toString());
        return bean;
    }
}
```





## BeanFactoryPostProcessor

`BeanFactoryPostProcessor`  可以对 bean 的配置元数据进行操作。

也就是说，Spring 容器允许 `BeanFactoryPostProcessor` 读取配置元数据，并在容器实例化 bean 实例之前可能更改 bean 实例的相关属性，例如可以把 bean 的 scope 从 singleton 改为 prototype

定义：

```xml
<bean id="student" class="kzq.yorua.demo.domain.Student" scope="prototype">
    <property name="id" value="100"/>
    <property name="name" value="张三"/>
</bean>
```

使用 BeanFactoryPostProcessor 修改 bean 的元数据：

```java
@Component
public class ExampleBeanFactoryPostProcessor implements BeanFactoryPostProcessor {

    @Override
    public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {
        BeanDefinition bd = beanFactory.getBeanDefinition("student");
        bd.setScope("singleton");
        MutablePropertyValues mpv = bd.getPropertyValues();
        if (mpv.contains("name")) {
            mpv.addPropertyValue("name", "李四");
        }
    }
}
```



## FactoryBean

如果创建 bean 的语句比较复杂，用 XML 来表示就过于冗长，此时使用 `FactoryBean`  就比较合适。

`FactoryBean` 是一个工厂 bean，可以生成某一个类型 bean 实例，它最大的作用就是可以自定义 bean 的创建过程。



```java
@Component
public class CustomFactoryBean implements FactoryBean<CustomBean> {
		// 控制bean的创建过程，返回创建后的bean
    @Override
    public CustomBean getObject() throws Exception {
        return new CustomBean();
    }
		// bean的类型
    @Override
    public Class<?> getObjectType() {
        return CustomBean.class;
    }
		// 是否单例
    @Override
    public boolean isSingleton() {
        return true;
    }
}

class CustomBean {

}
```





如果要获取 FactoryBean 的实例而不是 FactoryBean 创建的对象实例，则要在 id 之前加一个 `&`

```java
// 获得的是id为myBean的bean实例的FactoryBean
ctx.getBean("&myBean")
```





## Ordered

如果要自定义 BeanPostProcessor 的执行顺序，让 BeanPostProcessor 的实现类实现 `PriorityOrdered` 接口或者 `Ordered` 接口，并重写其 `getOrder()` 方法。

- `getOrder()`方法的返回值越小，优先级越高
- `PriorityOrdered` 的优先级大于 `Ordered`

```java
@Component
public class ExampleBeanPostProcessor implements BeanPostProcessor, Ordered {

    public Object postProcessBeforeInitialization(Object bean, String beanName) {
        System.out.println("1 before Bean '" + beanName + "' created : " + bean.toString());
        return bean;
    }

    public Object postProcessAfterInitialization(Object bean, String beanName) {
        System.out.println("1 after Bean '" + beanName + "' created : " + bean.toString());
        return bean;
    }

    @Override
    public int getOrder() {
        return 0;
    }
}
```





# Spring相关注解🍀

注解注入在 XML 注入之前进行



## 识别注解

`<context:annotation-config>`  用于激活那些已经在spring容器里注册过的 bean 上面的注解，具体逻辑是向 Spring 注册以下四个后处理器：

```
AutowiredAnnotationBeanPostProcessor     @Autowired
CommonAnnotationBeanPostProcessor				 @Resource 、@PostConstruct、@PreDestroy
PersistenceAnnotationBeanPostProcessor	 @PersistenceContext
RequiredAnnotationBeanPostProcessor			 @Required
```

基于以上 BeanPostProcessor ， Spring 就可以识别对应的注解了。



## 开启注解扫描

如果不用 `@Configuration` 配置注解类，还想用容器相关注解，则需要在 `bean.xml` 中开启注解扫描

注意，`<<context:component-scan>` 包含了  `<context:annotation-config>`   的功能，除了以上功能，还能将被具有  `@Component` 功能的注解修饰的类注入到容器中。

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









## 容器配置相关注解🌵

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

`@Component  ` 是通用注解，其他三个注解是这个注解的拓展，并且具有了特定的功能



### @Controller

用于表现层，除了与 `@Component` 相同的作用，还具有将请求进行转发，重定向的功能。



### @Service

用于业务层，实际上作用与 `@Component` 相同



### @Repository

用于持久层，除了与 `@Component` 相同的作用，还具有将数据库操作抛出的原生异常翻译转化为Spring的持久层异常的功能。





### @ComponentScan

用于通过注解指定 Spring 在创建容器时要扫描的包。创建一个配置类，在配置类上添加 `@ComponentScan` 注解。该注解默认会扫描该类所在的包下所有的配置类，相当于 XML 文件中的 `<context:component-scan>` 。

- value：用于指定创建容器时要扫描的包
- basePackages：和 value 相同
- includeFilters 和 excludeFilters：包含以及排除的选项，值为一个 `@Filter` 注解用于过滤

```java
@Configuration
@ComponentScan(basePackages = "org.example",
        includeFilters = @Filter(type = FilterType.REGEX, pattern = ".*Stub.*Repository"),
        excludeFilters = @Filter(annotation = Repository.class))
public class AppConfig {
    // ...
}
```





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



### @Bean

`@Bean` 是一个方法级的注解，作用等同于 XML 文件中的 `<beans/>` 标签

`@Bean` 用于把当前方法的返回值作为 bean 对象存入 Spring 的 ioc 容器中

jar 包中的类无法修改，所以不能加 `@Component` 之类的注解，一种有效的方法是在配置类中创建 `getXXX` 方法，再将 `XXX` 作为该方法的返回值，最后在方法上加上 `@Bean` 注解，则可以实现将 `XXX` 加入 ioc 容器中

注解的参数：

- name：用于指定 bean 的 id，如果为空，则默认值是当前方法的名称
- value：效果和 name 相同
- initMethod：初始化执行方法
- destroyMethod：销毁执行方法
- autowire（过期）
- autowireCandidate

```java
@Configuration
public class AppConfig {

    @Bean
    public MyService myService() {
        return new MyServiceImpl();
    }
}
```

以上代码等同于以下 XML：

```xml
<beans>
    <bean id="myService" class="com.acme.services.MyServiceImpl"/>
</beans>
```



当使用 ` @Bean` 注解配置到方法时，如果方法有参数，Spring 框架会去容器中査找是否有可用的 bean 对象，

查找的方式和 `@Autowired` 的方式是一样的

如果在 ioc 容器中存在多个同类型对象，则无法区分注入的参数具体是哪一个，则需要在方法参数上使用 `@Qualifier` 注解

```java
@Configuration
public class AppConfig {

    @Bean
    public MyService myService(@Qualifier("userConfig") Config config) {
        return new MyServiceImpl();
    }
}
```



@Bean 也可以和其他的注解配合使用，从而达成和 XML 文件中一样的效果

```java
@Configuration
public class AppConfig {

    @Bean
  	// bean范围
    @Scope("singleton")
    // bean描述
    @Description("Provides a basic example of a bean")
    public MyService myService(@Qualifier("userConfig") Config config) {
        return new MyServiceImpl();
    }
}
```





## 注入数据相关注解🌵

以下作用和在 xml 中 bean 标签中配置 `<property>` 标签的作用相同

集合类型的注入只能通过 xml 来实现。



### @Required

`@Required` 用于保证所对应的属性必须被设置，应用于 bean 属性的 setter 方法。

受影响的 bean 属性必须在 XML 中被配置，否则容器就会抛出一个 `BeanInitializationException` 异常



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

```java
public class MovieRecommender {

    private final CustomerPreferenceDao customerPreferenceDao;

    @Autowired
    public MovieRecommender(CustomerPreferenceDao customerPreferenceDao) {
        this.customerPreferenceDao = customerPreferenceDao;
    }

    // ...
}
```

如果类只有一个构造函数，那就不需要加 `@Autowired` ，Spring 会自动推断





### @Primary

等价于 bean 标签中的 primary 属性。

使用 `@AutoWired` 自动注入时可能会存在多个候选，可以对 bean 使用 `@Primary` 注解，那么在检测到同类型 bean 中存在被  `@Primary`  修饰的那个 bean 时，就会自动注入该 bean 。



```java
@Configuration
public class MovieConfiguration {

    @Bean
    @Primary
    public MovieCatalog firstMovieCatalog() { ... }

    @Bean
    public MovieCatalog secondMovieCatalog() { ... }

    // ...
}
```



### @Qualifier

等价于 bean 标签中内嵌的 qualifier 标签。

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

首先根据变量类型 AccountMapper 查找，发现有多个匹配，再根据 bean 的 id "accountMapper01" 确定唯一对象。

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

如果没提供 name ，则通过变量名或者 setter 方法名自动推断出一个 name 。

```java
@Service("accountService")
public class AccountServiceImpl implements AccountService {

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



## 生命周期相关注解🌵

 以及  的作用是相同的



### @scope

 等同于 bean 标签中的 scope ，用于指定 bean 的作用范围。

- singleton：单例
- prototype：多例

```java
@Bean(name = "runner")
@Scope("prototype")
public QueryRunner createQueryRunner() {
    return new QueryRunner();
}
```



### @PostConstruct

Java 层面提供的注解，等同于 bean 标签中的 init-method，用于指定初始化方法。



### @PreDestroy

Java 层面提供的注解，等同于 bean 标签中的 destroy-method ，用于指定销毁方法。



### @Order

`@Order` 的作用是定义 Spring 容器加载 bean 的顺序。



## 配置文件相关注解🌵

### @Import

`@Impor`t 用于导入其他的配置类，参数为其他配置类的字节码文件，作用与 XML 文件中的 `<import/>` 相同。

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

在注解配置中导入 XML 的配置文件

例如，创建一个 Spring 的配置文件 `bean.xml`

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

![image-20211110160002637](../../../Library/Application%20Support/typora-user-images/image-20211110160002637.png)





### @Profile

`@Profile` 注解表明某些组件注入到容器的条件

```java
@Configuration
public class AppConfig {

    @Bean("dataSource")
    @Profile("development") 
    public DataSource standaloneDataSource() {
        return new EmbeddedDatabaseBuilder()
            .setType(EmbeddedDatabaseType.HSQL)
            .addScript("classpath:com/bank/config/sql/schema.sql")
            .addScript("classpath:com/bank/config/sql/test-data.sql")
            .build();
    }

    @Bean("dataSource")
    @Profile("production") 
    public DataSource jndiDataSource() throws Exception {
        Context ctx = new InitialContext();
        return (DataSource) ctx.lookup("java:comp/env/jdbc/datasource");
    }
}
```





### @PropertySource

用于指定 properties 文件的位置，作用类似于以下标签

```xml
<context:property-placeholder location="classpath:jdbc.properties" />
```

- value：指定文件的名称和路径。关键字：classpath，表示类路径下



示例配置文件如下：

```properties
# jdbc.properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/spring_learn
jdbc.user=root
jdbc.password=kzqkzq
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



👉  配合  `@ConfigurationProperties` 使用（Springboot）

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





## 缓存相关注解🌵

### @EnableCaching



## 空值相关注解🌵

### @Nullable 

作用在参数或返回值上，声明可能是 null



### @NonNull

作用在方法、参数或返回值上，声明不能是 null



### @NonNullApi

作用在包上，声明相关参数和返回值既不接受 null，也不返回 null



### @NonNullFields

作用在包上，声明所有属性不为 null





# Resource🍀

## 各种Resource



## ResourceLoader



## ResourcePatternResolver



## ResourceLoaderAware







# Spring AOP🍀

面向切面编程（Aspect Oriented Programming，AOP）是一种编程思想，是面向对象编程（OOP）的一种补充。

简单的说是将程序中重复的代码抽取出来，在需要执行的时候，使用动态代理的技术，在不修改源码的基础上，对已有的方法进行增强。



## 动态代理

代理模式给某一个对象提供一个代理对象，并由代理对象控制对原对象的引用，不修改对象源码的基础上对方法增强。

- 静态代理：需要手动的去实现目标对象的代理类，字节码一开始就创建好，并完成加载。

- 动态代理：在程序运行期间，使用反射机制动态的生成代理类。

特点：字节码随用随创建，随用随加载



### 基于接口的动态代理（jdk）

涉及的类：`Proxy` 

提供者：JDK 官方

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

如何创建代理对象：使用 `Enhancer` 类中的 `create#` 方法

要求：被代理类不能是 final 修饰的最终类  



`create()` 方法的参数：

- Class：字节码，用于指定被代理对象的字节码
- Callback：接口类型，用于提供增强的代码，一般是传递一个该接口的实现类，通常情况下都是匿名内部类，一般写的都是 Callback 接口的实现类 `MethodInterceptor`

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

- 如果要被代理的对象是个实现类（实现了一个接口），那么 Spring 会使用 JDK 动态代理来完成操作（Spring 默认采用 JDK 动态代理）

- 如果要被代理的对象不是个实现类，那么 Spring 会使用 cglib 来实现动态代理



## AOP术语

- Aspect（切面）

  是 PointCut 和 Advice 的结合

- Joinpoint（连接点）

  连接点是指被拦截到的点，在Spring中，这些点指的是「方法」，因为 Spring 只支持方法类型的连接点，一般是指 Service 层接口中的方法。

- Pointcut（切入点）

  切入点是指要对哪些 Joinpoint 进行拦截的定义，一般指业务层接口中被增强的方法，Pointcut 是 Joinpoint 的子集。

- Advice（通知）：拦截到 Joinpoint 之后所要做的事情就是通知。

  通知的类型

  - 前置通知：业务层代码之前的语句

  - 后置通知：业务层代码之后的语句

  - 异常通知：catch 中的语句

  - 最终通知：finally 中的语句

  - 环绕通知：整个 invoke 方法语句

    <img src="http://store.secretcamp.cn/uPic/image-202012122053258572020121220532616077776068OGvvA8OGvvA.png" alt="image-20201212205325857" style="zoom:47%;" />



- Introduction（引介）

  引介是一种特殊的通知，在不修改类代码的前提下，Introduction 可以在运行期动态地为类添加一些方法或 Field。

- Target Object（目标对象）

  代理的目标对象，即被代理对象

- Weaving（织入）

  是指把增强应用到目标对象来创建新的代理对象的过程，例如，使用动态代理对象提供事务支持的过程，就叫做 “织入”。

  Spring 采用动态代理织入，而 AspectJ 采用编译期织入和类加载期织入。

- AOP Proxy（代理）

  一个类被 AOP 织入增强后，就产生一个结果代理类。




## AOP的工作原理

AOP 是发生在 Bean 的生命周期过程中：

1. Spring 生成 bean 对象时，先实例化出来一个对象，也就是 Target 对象 
2. 再对 Target 对象进行属性填充

3. 在初始化后步骤中，会判断 Target 对象有没有对应的切面 
4. 如果有切面，就表示当前 Target 对象需要进行 AOP

5. 通过 cglib 或 jdk 的动态代理机制生成一个代理对象，作为最终的 bean 对象



## AOP的依赖jar包

Spring AOP 功能依赖 aspectj ，需要先在 maven 中引入：

```xml
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.8.7</version>
</dependency>
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





## XML配置AOP

### 整体流程

1. 配置 aspectj 依赖



2. 将通知 bean 交给 Spring 来管理，即注入容器

3. 使用 `<aop: config>` 标签，表明开始 AOP 的配置 

4. 使用 `<aop: aspect>` 标签，表明配置切面

5. 在 `<aop: aspect>` 标签的内部使用对应标签来配通知的类型



### aop:config

`<aop:config/>` 下有三种类型的标签：

1. `<aop:pointcut>`

2. `<aop:aspect>`

3. `<aop:advisor>`



#### aop:aspect

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



#### aop:pointcut

`<aop:pointcut>` 属性：

- `id`：切入点的唯一标识
- `expression`：切入点表达式

```xml
<aop:pointcut id="servicePos" expression="execution(* kzq.yorua.demo.service..*(..))"/>
```

然后在通知方法的标签中，可以用 pointcut-ref 属性引用该切入点



#### aop:advisor

进行事务管理时，通常会使用 `<aop:advisor>`  ，用于定义通知器，在 `<aop:advisor>` 中引用的通知时，通知必须实现 `Advice` 接口。

`<aop:advisor>` 属性：

- id

- advice-ref
- pointcut
- pointcut-ref
- order



### Example

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

通知方法同时最多只能执行三个，因为异常通知、后置通知不能同时触发，此外，环绕通知只能耽误

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





## 声明式AOP

### 开启对注解的支持

#### XML方式

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
    <context:component-scan base-package="kzq.yorua.demo"/>
  
    <!-- 开启AOP注解配置-->
    <aop:aspectj-autoproxy/>
</beans>
```



#### 注解方式

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





# Spring事件机制🍀

Spring 通过 `ApplicationEvent` 以及 `ApplicationEvent` 实现事件控制



## 事件

定义事件

```java
public class TxEvent extends ApplicationEvent {

    private final String customMessage;

    public TxEvent(Object source, String customMessage) {
        super(source);
        this.customMessage = customMessage;
    }

    public String getCustomMessage() {
        return customMessage;
    }
}
```



## 事件监听器

定义事件监听器

### 继承ApplicationListener

创建一个类继承 `ApplicationListener<TxEvent>` ，泛型类型是

```java
@Component
public class TxListener1 implements ApplicationListener<TxEvent> {

    @Override
    public void onApplicationEvent(TxEvent event) {
        String customMessage = event.getCustomMessage();
        System.out.println("事件： " + customMessage);
    }
}
```



### @EventListener

也可以使用 `@EventListener` 创建事件监听器，此方法不需要继承 `ApplicationListener`

```java
@Component
public class TxListener2 {
    @EventListener
    public void handler(TxEvent event) {
        String customMessage = event.getCustomMessage();
        System.out.println("事件B： " + customMessage);
    }
}
```

也可以不在方法签名中写对应事件，而是在注解的 classes 属性中表明对应事件：

这样无法获取事件的属性，适合做一些和事件无关的操作。

```java
@Component
public class TxListener3 {
    @EventListener(classes = {TxEvent.class})
    public void handler() {
        System.out.println("事件C");
    }
}
```





## 事件发布

定义事件发布

```java
@Component
public class TxEventPublisher {
    @Autowired
    private ApplicationEventPublisher applicationEventPublisher;

    public void publish(String message) {
        System.out.println(applicationEventPublisher);
        TxEvent event = new TxEvent(this, message);
        applicationEventPublisher.publishEvent(event);
    }
}
```

当调用事件发布的 `publish#` 方法时，会创建一个 `TxEvent` 事件并发布，事件监听器会监听到这个事件，从而执行事件监听器的 `onApplicationEvent#` 方法。





## 异步监听器





## 顺序监听



# Spring事务🍀

## 全局事务与本地事务

- 本地事务

  不用事务的编程框架来管理事务，直接使用资源管理器来控制事务。典型的就是 JDBC 事务。

- 全局事务

  一种标准的分布式事务解决方案，解决了本地事务无法满足分布式场景中数据的 ACID 的要求。

  常用的实现协议有 2PC、3PC、Paxios



## Java的事务类型

### JDBC事务

JDBC中是通过 `Connection` 对象进行事务管理，常用的和事务相关的方法是： `setAutoCommit`、`commit`、`rollback` 等。

缺点：一个 JDBC 事务不能跨越多个数据库，对分布式场景无能为力

### JTA事务

JTA（Java Transaction API）事务提供了分布式事务的解决方案，可以实现严格的 ACID。

缺点：

1. 实现复杂，通常情况下，JTA UserTransaction 需要从 JNDI 获取，这意味如果我们使用 JTA，就需要同时使用 JTA 和 JNDI 。
2. JTA 本身就是个笨重的API



### 容器事务







## 配置事务管理器

无论是使用编程式事务管理还是声明式事务管理，都需要配置 `TransactionManager` 

```xml
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
    <property name="driverClassName" value="${jdbc.driverClassName}" />
    <property name="url" value="${jdbc.url}" />
    <property name="username" value="${jdbc.username}" />
    <property name="password" value="${jdbc.password}" />
</bean>

<bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"/>
</bean>
```



## 配置方式

Spring 支持编程式事务管理以及声明式事务管理两种方式。



### 编程式事务管理

如果程序中只有一小部分代码用到事务，则推荐使用编程式事务管理，同时，也只有编程式事务可以显式地设置事务名称。

编程式事务管理是侵入性事务管理，使用 `TransactionTemplate` 或者直接使用 `PlatformTransactionManager`，对于编程式事务管理，Spring 推荐使用 `TransactionTemplate` 。



### 声明式事务管理

声明式事务管理建立在 AOP 之上，其本质是对方法前后进行拦截，然后在目标方法开始之前创建或者加入一个事务，执行完目标方法之后根据执行的情况提交或者回滚。
编程式事务每次实现都要单独实现，但业务量大功能复杂时，使用编程式事务无疑是痛苦的，而声明式事务不同，声明式事务属于无侵入式，不会影响业务逻辑的实现，只需要在配置文件中做相关的事务规则声明或者通过注解的方式，便可以将事务规则应用到业务逻辑中。
显然声明式事务管理要优于编程式事务管理，这正是 Spring 倡导的非侵入式的编程方式。唯一不足的地方就是声明式事务管理的粒度是方法级别，而编程式事务管理是可以到代码块的，但是可以通过提取方法的方式完成声明式事务管理的配置。





## 事务的传播机制

事务的传播性一般用在事务嵌套的场景，比如一个事务方法里面调用了另外一个事务方法，那么两个方法是各自作为独立的方法提交还是内层的事务合并到外层的事务一起提交，这就是需要事务传播机制的配置来确定怎么样执行。

常用的事务传播机制如下：

- `PROPAGATION_REQUIRED`

  Spring 默认的传播机制，能满足绝大部分业务需求，如果外层有事务，则当前事务加入到外层事务，一块提交，一块回滚。如果外层没有事务，新建一个事务执行

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



# 编程式事务管理🍀

Spring 提供了两种方式进行编程式事务管理

-  `TransactionTemplate` 或者 `TransactionalOperator`
- `TransactionManager` 的直接实现类



## TransactionTemplate

`TransactionTemplate` 的实例是线程安全的，不维护任何会话状态。

同时，`TransactionTemplate` 的实例会维护配置状态，即如果有一个类需要用到特别的配置，意味着需要多个 `TransactionTemplate` 实例。

1. 定义

   ```xml
   <bean id="transactionTemplate" class="org.springframework.transaction.support.TransactionTemplate">
       <property name="transactionManager" ref="txManager"/>
       <property name="isolationLevelName" value="ISOLATION_READ_UNCOMMITTED"/>
       <property name="timeout" value="30"/>
   </bean>
   ```



2. 使用

   ```java
   @Autowired
   AccountMapper accountMapper;
   @Autowired
   TransactionTemplate transactionTemplate;
   
   @Override
   public void transfer(String from, String to, double money) {
       transactionTemplate.execute(new TransactionCallbackWithoutResult() {
           @Override
           protected void doInTransactionWithoutResult(TransactionStatus transactionStatus) {
               try {
                   System.out.println("转账开始");
                   Account account1 = accountMapper.queryAccount(from);
                   Account account2 = accountMapper.queryAccount(to);
                   account1.setBalance(account1.getBalance() - money);
                   account2.setBalance(account2.getBalance() + money);
                   accountMapper.updateAccount(account1);
                   accountMapper.updateAccount(account2);
                   System.out.println("转账完成");
               }
               catch (Exception e) {
                   System.out.println("error");
                   e.printStackTrace();
               }
   
           }
       });
   }
   ```

   



## TransactionOperator

略



## TransactionManager

可以使用  `TransactionManager` 手动管理事务。

1. 定义

   ```xml
   <bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
       <property name="dataSource" ref="dataSource"/>
   </bean>
   ```

   

2. 使用

   ```java
   public void transferExample3(String from, String to, double money) {
       DefaultTransactionDefinition def = new DefaultTransactionDefinition();
       def.setName("SomeTxName");
       def.setPropagationBehavior(TransactionDefinition.PROPAGATION_REQUIRED);
       TransactionStatus status = txManager.getTransaction(def);
       try {
           System.out.println("转账开始");
           Account account1 = accountMapper.queryAccount(from);
           Account account2 = accountMapper.queryAccount(to);
           account1.setBalance(account1.getBalance() - money);
           account2.setBalance(account2.getBalance() + money);
           accountMapper.updateAccount(account1);
           accountMapper.updateAccount(account2);
           System.out.println("转账完成");
       }
       catch (Exception ex) {
           txManager.rollback(status);
           throw ex;
       }
       txManager.commit(status);
   }
   ```

   



# 声明式事务管理🍀

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



## 开启注解配置

### XML方式开启

```xml
<!-- 开启Spring对注解事务的支持 -->
<tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>
```



### 注解方式开启

对于纯注解配置，则需要在配置类上添加 `@EnableTransactionManagement` 注解，表示开启声明式事务

```java
@Configuration
@ComponentScan("kzq.yorua.demo")
@Import({JdbcConfig.class, TransactionConfig.class})
@PropertySource("jdbc.properties")
@EnableTransactionManagement
public class ApplicationConfig {
  
}
```



## @Transactional

使用这种方式，无需配置 `<tx:advice>` 和 `<aop:config>`

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









