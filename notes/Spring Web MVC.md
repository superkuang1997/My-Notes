# Spring Web MVC🍀

Spring Web MVC 是一个基于 Servlet API 的 Web 框架，即众所周知的 SpringMVC 。 



## 参考文档

👉  [Spring - MVC](https://docs.spring.io/spring-framework/docs/5.3.6/reference/html/web.html#spring-web)

👉  [Springboot - MVC](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.developing-web-applications)



## MVC

MVC（Model、View、Controller），是模型－视图－控制器的缩写，是一种用于设计创建 Web 应用程序表现层的模式。

MVC 是在应用程序（BS结构）的表现层划分出来的不同功能的几个模块。

- Model：数据模型，JavaBean，一般情况下用于封装数据。 
- View：通常指的是 jsp 或者 html，作用一般就是展示数据的，通常视图是依据模型数据创建的。
- Controller：是应用程序中处理用户交互的部分，作用一般就是处理程序逻辑的。



## 三层架构

- 表现层： 也就是我们常说的 web 层。它负责接收客户端请求，向客户端响应结果，通常客户端使用 http 协议请求 web 层，web 需要接收 http 请求，完成 http 响应。

  表现层包括展示层和控制层：控制层负责接收请求，展示层负责结果的展示。表现层依赖业务层，接收到客户端请求一般会调用业务层进行业务处理，并将处理结果响应给客户端。表现层的设计一般都使用 MVC 模型。（MVC是表现层的设计模型，和其他层没有关系） 

- 业务层： 也就是常说的 service 层。它负责业务逻辑处理。web 层依赖业务层，但是业务层不依赖 web 层。业务层在业务处理时可能会依赖持久层，如果要对数据持久化需要保证事务一致性。（事务应该放到业务层来控制）

- 持久层： 也就是是 dao 层，负责数据持久化，包括数据层即数据库和数据访问层，数据库是对数据进行持久化的载体，数据访问层是业务层和持久层交互的接口，业务层需要通过数据访问层将数据持久化到数据库中。通俗的讲，持久层就是和数据库交互，对数据库表进行增删改查的。 



## 相关依赖

### spring-web

spring-web 提供了核心的 HTTP 集成，包括Servlet过滤器、Spring HTTP 调用器、与其他 web 框架和 HTTP 技术集成的基础设施。

### spring-webmvc

spring-webmvc 是对 SpringMVC 的具体实现，spring-webmvc 依赖于 spring-web 。

如果不想使用 SpringMVC 而是使用其他 web 技术，可以只导入 spring-web



### maven依赖

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <spring.version>5.2.15.RELEASE</spring.version>
</properties>
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>${spring.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>${spring.version}</version>
    </dependency>
	  <!-- 如果用到原生 Servlet 功能-->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>servlet-api</artifactId>
        <version>2.5</version>
        <scope>provided</scope>
    </dependency>
		<!-- 如果用到 JSP -->
    <dependency>
        <groupId>javax.servlet.jsp</groupId>
        <artifactId>jsp-api</artifactId>
        <version>2.0</version>
        <scope>provided</scope>
    </dependency>
    <!-- 如果 response 需要返回 json 数据 -->
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.12.3</version>
    </dependency>  
</dependencies>
```





## WebMvc配置

### 开启MVC配置

可以使用 `@EnableWebMvc` 去允许 SpringMVC 进行配置

```java
@Configuration
@EnableWebMvc
public class WebConfig {
}
```



也可以使用 XML 的方式：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:mvc="http://www.springframework.org/schema/mvc"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc
        https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <mvc:annotation-driven/>

</beans>
```





### XML配置





### Java编码配置

```java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    // 通过实现方法来自定义组件
}
```



### 进阶配置方法

 `@EnableWebMvc`  注解会在容器中导入一个 `DelegatingWebMvcConfiguration` ，它会向 Spring 容器中注入一些默认的组件，同时也会检测到容器中实现了  `WebMvcConfigurer`  的配置类，从而实现自定义 MVC 配置。

一种进阶的使用方法是不使用  `@EnableWebMvc` 和实现 `WebMvcConfigurer` ，而是直接继承类 `DelegatingWebMvcConfiguration` ，这样，SpringMVC 的一切配置将由开发者直接定义。

```java
@Configuration
public class WebConfig extends DelegatingWebMvcConfiguration {

    // ...
}
```



# SpringMVC相关注解🍀

## @RequestMapping

建立请求URL和处理方法之间的对应关系

`@RequestMapping` 的属性：

- path：指定请求路径的url

- value：与path属性是一样的

- method：指定该方法的请求方式

- params：指定限制请求参数的条件

- headers：发送的请求中必须包含的请求头

`@RequestMapping ` 可以作用在方法和类上

- 作用在类上：第一级的访问目录

- 作用在方法上：第二级的访问目录

```java
@RequestMapping("/second")
public Map<String, Object> testRequestMapping() {
    Map<String, Object> map = new HashMap<>();
    map.put("id", 128);
    map.put("username", "Jack Ma");
    return map;
}
```



## @PathVariable

将占位符中的参数绑定到方法的参数中

属性：

- value：指定 url 中的占位符名称

```java
@RequestMapping("/second/{id}/{username}")
public Map<String, Object> testPathVariable(@PathVariable("id") int id,
                                            @PathVariable("username") String username,
                                            @PathVariable Map<String, String> kv) {
    Map<String, Object> map = new HashMap<>();
    map.put("id", id);
    map.put("username", username);
    map.put("kv", kv);
    return map;
}
```





## @RequestParam

把请求中的指定名称的参数传递给控制器中的形参赋值

属性：

- value：请求参数中的名称

- required：请求参数中是否必须提供此参数，默认值为 true

```java
@RequestMapping("/params")
public Map<String, Object> getParams(@RequestParam("name") String name,
                                     @RequestParam("animal") List<String> animal,
                                     @RequestParam Map<String, String> params) {
    Map<String, Object> map = new HashMap<>();
    map.put("name", name);
    map.put("animal", animal);
    map.put("params", params);
    return map;
}
```



## @RequestHeader

获取指定请求头的值

属性：

- value：请求头的名称

```java
@RequestMapping("/header")
public Map<String, Object> getHeaders(@RequestHeader("user-agent") String userAgent,
                                      @RequestHeader Map<String, String> headers) {
    Map<String, Object> map = new HashMap<>();
    map.put("user-agent", userAgent);
    map.put("headers", headers);
    return map;
}
```



## @RequestBody

`@RequestBody` 是将 HTTP 请求正文插入方法中，使用适合的 `HttpMessageConverter` 将请求体写入某个对象。

1. 该注解用于读取 request 请求的 body 部分数据，使用系统默认配置的 HttpMessageConverter 进行解析，然后把相应的数据绑定 到要返回的对象上
2. 再把 HttpMessageConverter 返回的对象数据绑定到 Controller 中方法的参数上。

属性：

- required：是否必须有请求体，默认值为 true

```java
@PostMapping("/body")
public Map<String, Object> getRequestBody(@RequestBody String body) {
    Map<String, Object> map = new HashMap<>();
    map.put("body", body);
    return map;
}
```







## @CookieValue

用于获取指定 cookie 的名称的值

属性：

- value：cookie的名称
- required：是否必须有此cookie

参数除了 `String` ，也可以接收 `javax.servlet.http.Cookie` 类型

```java
@RequestMapping("/cookie")
public Map<String, Object> getCookie(@CookieValue("Idea-107809e8") String idea,
                                     @CookieValue("Idea-107809e8") Cookie cookie,
                                     @CookieValue("username-localhost-8888") String info) {
    Map<String, Object> map = new HashMap<>();
    map.put("idea", idea);
    map.put("cookie", cookie);
    map.put("info", info);
    return map;
}
```





## @RequestAttribute

`@RequestAttribute` 配置在方法参数上，作用是从 request 域中取对应的值

```java
@Controller
public class AttributeController {
    @GetMapping("/goto")
    public String gotoPage(HttpServletRequest request) {
        request.setAttribute("msg", "ok");
        request.setAttribute("code", "200");
        return "forward:/success";
    }

    @ResponseBody
    @GetMapping("/success")
    public Map<String, Object> doSuccess(@RequestAttribute("msg") String msg,
                                         @RequestAttribute("code") String code,
                                         HttpServletRequest request) {
        Map<String, Object> map = new HashMap<>();
        map.put("msg", msg);
        map.put("code", code);
        System.out.println(request.getAttribute("msg"));
        System.out.println(request.getAttribute("code"));
        return map;
    }
}
```



## @ModelAttribute

配置在方法上：表示当前方法会在控制器方法执行前先执行

配置在参数上：获取指定的数据给参数赋值

属性：

- value：用于获取数据的key

```java
@ModelAttribute
public void testModelAttribute01(Map<String, User> map) {
    System.out.println("execute before other action");
    User user = new User();
    user.setId(66);
    user.setUsername("yorua");
    map.put("key", user);
}

@RequestMapping("/ModelAttribute")
public String testModelAttribute02(@ModelAttribute("key") User user) {
    System.out.println(user);
    return "success";
}
```







## @SessionAttributes

只用作用在类上，作用是将指定的Model的键值对保存在Session中，用于多次执行控制器方法间的参数共享。

属性：

- value：指定存入属性的名称

- type：用于指定存入的数据类型



先将数据存储在Model中

```java
@RequestMapping("/setSessionAttributes")
public String testSessionAttributes01(Model model) {
    // Model中的数据底层会存储到request域中
    model.addAttribute("msg", "hello world");
    return "success";
}


@RequestMapping("/getSessionAttributes")
public String testSessionAttributes02(ModelMap modelMap) {
  	// 从request域中获取数据
    String msg = (String) modelMap.getAttribute("msg");
    System.out.println(msg);
    return "success";
}
```

再将Model中的数据存储在Session域中

```java
@Controller
@RequestMapping("/anno")
@SessionAttributes(value = {"msg"})  
public class AnnoController {
		// ...
}
```



## @MatrixVariable





## @ResponseBody

将 Controller 的方法返回的对象通过适当的 HttpMessageConverter 转换为指定的格式之后，写入到 Response 对象的 body 之中，通常是转为 json 格式。

使用此注解之后不会再走视图解析器，而是直接将数据写入到输入流中，他的效果等同于通过 Response 对象输出指定格式的数据。



## @RestController

将当前类作为 Controller 加入容器，同时该类的所有方法都以 json 形式返回

即同时拥有 `@Controller` 以及 `@ResponseBody` 的效果







# SpringMVC组件🍀

基本组件：

1. 前端控制器（DispatcherServlet）

2. 处理器映射器（HandlerMapping）

3. 处理器（Handler）

4. 处理器适配器（HandlAdapter）

5. 视图解析器（View Resolver）

6. 视图（View）



## 表现层请求流程

1. 用户发出请求，请求到达 SpringMVC 的前端控制器（DispatcherServlet），

2. DispatcherServlet 根据用户的 url 请求处理器映射器（HandlerMapping）查找匹配该 url 的 handler，并返回处理器的执行链
3. DispatcherServlet 再请求处理器适配器（HandlerAdapter）调用相应的 handler 进行处理并返回给 DispatcherServlet 一个逻辑视图（modelAndView）
4. DispatcherServlet 再请求视图解析器（ViewResolver）对返回的逻辑视图进行解析，返回一个视图对象（View）
5. DispatcherServlet 将返回的视图对象进行渲染并把数据装入到 request 域，返回给用户。



## 开启注解配置

```xml
<!-- 开启Spring注解扫描 -->
<context:component-scan base-package="kzq.springmvc"/>

<!-- 开启 SpringMVC 对注解配置的支持 -->
<mvc:annotation-driven/>
```

`<mvc:annotation-driven/>` 会自动注册 `RequestMappingHandlerMapping` 与 `RequestMappingHandlerAdapter` 两个 bean，这是 Spring MVC 为 `@Controller` 分发请求所必需的，并且提供了数据绑定支持，简单地说，就是让 `@RequestMapping` 之类的注解生效

可以用 `<mvc:annotation-driven/>` 替代 HandlerMapping 和 HandlerAdapter 的配置



## DispatcherServlet 前端控制器 

前端控制器（DispatcherServlet），负责接收用户的请求并根据用户的请求返回相应的视图给用户，DispatcherServlet 是整个流程控制的中心，由它调用其它组件处理用户的请求。

### 配置方式

可以在 `web.xml` 中配置，并在 `<init-param>` 中指定了 spring 的配置文件

url-pattern：

- `/`：拦截所有请求，不拦截 jsp 页面
- `/*`：包含 `/` ，拦截所有请求，也拦截 jsp 页面
- `*.xxx`：拦截固定结尾的 url 请求，常见的有 `*.do` 、`*.json`、`*.action`

```xml
<!-- 配置前端控制器 -->
<servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring.xml</param-value>
    </init-param>
    <!-- 启动服务器即创建Servlet -->
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <url-pattern>/app/*</url-pattern>
</servlet-mapping>
```



也可以使用 Java 编码的方式配置：

```java
@Controller
public class MyWebApplicationInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext servletContext) {

        // Load Spring web application configuration
        AnnotationConfigWebApplicationContext context = new AnnotationConfigWebApplicationContext();
        context.register(AppConfig.class);

        // Create and register the DispatcherServlet
        DispatcherServlet servlet = new DispatcherServlet(context);
        ServletRegistration.Dynamic registration = servletContext.addServlet("dispatcherServlet", servlet);
        registration.setLoadOnStartup(1);
        registration.addMapping("/app/*");
    }
}
```



### 初始化参数

| 参数                           | 说明                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| contextClass                   | 默认是 `XmlWebApplicationContext`                            |
| contextConfigLocation          | String，Spring Context 的路径                                |
| namespace                      | `WebApplicationContext` 的名称空间，默认是 `[servlet-name]-servlet` |
| throwExceptionIfNoHandlerFound | boolean，未发现是否抛出异常                                  |



## HandlerMapping 处理器映射器

处理器映射器（HandlerMapping）负责根据用户请求 url 找到 Handler 即处理器，SpringMVC 提供了不同的映射器实现不同的映射方式，例如：配置文件方式，实现接口方式，注解方式等。

```xml
<!--配置处理器映射器 选择其中一个 -->
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping"/>
<bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
<bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping"/>
```



##  Handler 处理器

Handler 是后端控制器，DispatcherServlet 将用户请求通过 HandlerMapping 转发到 Handler， 对具体的用户请求进行处理。

Controller 指的是类，而 Handler 指的是类中的一个方法。





## HandlerAdapter 处理器适配器

通过 HandlerAdapter 对 Hander 进行执行，不管具体执行的过程。这是适配器模式的应用，通过扩展适配器可以对更多类型的处理器进行执行。

```xml
<!-- 配置处理器适配器 选择其中一个 -->
<bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter"/>
<bean class="org.springframework.web.servlet.mvc.HttpRequestHandlerAdapter"/>
<bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
```

在 `HandlerAdapter` 接口中有一个 `support()` 方法，意思只有这个 Handler 是 Controller 对象时这个适配器才可以执行这个Handler

```java
// SimpleControllerHandlerAdapter 中的实现
public boolean supports(Object handler) {
    return handler instanceof Controller;
}
```

如果 `support(...)` 返回 true，则可以用  `handler(...)` 方法进行处理，返回一个 `ModelAndView`



## ViewResolver 视图解析器

视图解析器（ViewResolver）负责将处理结果生成视图（View）。

ViewResolver 首先根据逻辑视图名解析成物理视图名，即具体的页面地址，再生成 View 视图对象，最后对 View 进行渲染将处理结果通过页面展示给用户。

`/WEB-INF/pages/` 目录下的页面，可以在 handler 处理之后返回时被解析，但是不可以通过 url 直接请求

```xml
<!-- 配置视图解析器 -->
<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/pages/"/>
    <property name="suffix" value=".jsp"/>
</bean>
```

此时的 `InternalResourceViewResolver` 解析组件，默认将解析出  `org.springframework.web.servlet.view.InternalResourceView` 视图。

如果添加相应的 jstl 依赖，则会解析出 `org.springframework.web.servlet.view.JstlView` 视图

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
    <version>1.1.2</version>
</dependency>
```





## View 视图

SpringMVC 提供了很多的视图类型的支持，包括：jstlView、freemarkerView、pdfView 等，最常用的视图是 jsp 。





## Filter 过滤器 

过滤器（Filter）依赖于 Servlet 容器，在实现上基于函数回调，可以对几乎所有请求进行过滤，但是缺点是一个过滤器实例只能在容器初始化时调用一次。

在 `web.xml` 中配置过滤器

```xml
<!--  配置过滤器，该过滤器解决中文乱码问题 -->
<filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```



[继承 HttpServletRequestWrapper 以实现在 Filter 中修改 HttpServletRequest 的参数](https://www.zifangsky.cn/677.html)

[使用 Filter 过滤容易引发XSS的危险字符](https://www.zifangsky.cn/683.html)



## Interceptor 拦截器 

SpringMVC 的处理器拦截器类似于 Servlet 开发中的过滤器 Filter，用于对处理器进行预处理和后处理。

1. 可以定义拦截器链，拦截器链就是将拦截器按着一定的顺序结成一条链，在访问被拦截的方法时，拦截器链中的拦截器会按着定义的顺序执行。

2. 拦截器也是 AOP 思想的一种实现方式

3. 想要自定义拦截器，需要实现 `HandlerInterceptor` 接口。



拦截器和过滤器的功能比较类似，但有区别：

1. 过滤器是 Servlet 规范的一部分，任何框架都可以使用过滤器技术。
2. 拦截器是 SpringMVC 框架独有的
3. 过滤器如果配置了 `/*` ，可以拦截任何资源
4. 拦截器只会对 Controller 中的方法进行拦截，不会拦截静态资源和 jsp



### 自定义拦截器

```java
public class FirstInterceptor implements HandlerInterceptor {
  	
    // preHandle方法是controller方法执行前拦截的方法
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("Interceptor worked ... before");
        return true;
    }
  
		// postHandle是controller方法执行后执行的方法，在JSP视图执行前。
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("Interceptor worked ... after");
    }
  
		// afterCompletion方法是在JSP执行后执行
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("Interceptor worked ... last");
    }
}
```



### 配置拦截器

Java 编码配置：

```java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LocaleChangeInterceptor());
        registry.addInterceptor(new ThemeChangeInterceptor())
          .addPathPatterns("/**")
          .excludePathPatterns("/admin/**");
        registry.addInterceptor(new SecurityInterceptor()).addPathPatterns("/secure/*");
    }
}
```



XML配置：

`<mvc:mapping>` 的 path 表示需要拦截的 Controller

也可以使用 `<mvc:exclude-mapping>` 标签，表示除了设定的 Controller 之外全都拦截

```xml
<mvc:interceptors>
    <mvc:interceptor>
        <mvc:mapping path="/user/**"/>
        <bean class="kzq.springmvc.interceptor.FirstInterceptor"/>
    </mvc:interceptor>
</mvc:interceptors>
```

如果要配置多个拦截器，则使用多个 `</mvc:interceptor>` 标签





## ExceptionResolver 异常处理器 

异常处理器（ExceptionResolver）：服务器接收请求时，Controller调用业务层Service，业务层调用持久层，如果在持久层发生了异常，则会按照相反方向将异常层层抛出，最终由 DispatcherServlet 调用异常处理器进行异常处理。



### 默认的异常处理器

```xml
<!--  异常处理器，选择其中一个 -->
<bean class="org.springframework.web.servlet.mvc.method.annotation.ExceptionHandlerExceptionResolver"/>
<bean class="org.springframework.web.servlet.mvc.annotation.ResponseStatusExceptionResolver"/>
<bean class="org.springframework.web.servlet.mvc.support.DefaultHandlerExceptionResolver"/>
```



### 自定义异常类

```java
public class SystemException extends Exception {
    private String message;

    public SystemException(String message) {
        this.message = message;
    }

    @Override
    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }
}
```



### 自定义异常处理器

```java
public class SystemExceptionResolver implements HandlerExceptionResolver {
    @Override
    public ModelAndView resolveException(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) {
        SystemException systemException = null;
        if (e instanceof SystemException) {
            e = (SystemException) e;
        } else {
            e = new SystemException("a fatal error in server");
        }
        ModelAndView mv = new ModelAndView();
        mv.addObject("errorMsg", e.getMessage());
        mv.setViewName("error");
        return mv;
    }
}
```



### 配置异常处理器

在下方配置自定义的异常处理器

```xml
<!-- 配置异常处理器 该类必须要实现 HandlerExceptionResolver-->
<bean id="systemExceptionResolver" class="kzq.springmvc.exception.SystemExceptionResolver"/>
```



## Converter 类型转换器 

### 默认类型转换器

`org.springframework.core.convert.support` 包下，有以下默认类型转换器

```
java.lang.Boolean -> java.lang.String : ObjectToStringConverter
java.lang.Character -> java.lang.Number : CharacterToNumberFactory
java.lang.Character -> java.lang.String : ObjectToStringConverter
java.lang.Enum -> java.lang.String : EnumToStringConverter
java.lang.Number -> java.lang.Character : NumberToCharacterConverter
java.lang.Number -> java.lang.Number : NumberToNumberConverterFactory
java.lang.Number -> java.lang.String : ObjectToStringConverter
java.lang.String -> java.lang.Boolean : StringToBooleanConverter
java.lang.String -> java.lang.Character : StringToCharacterConverter
java.lang.String -> java.lang.Enum : StringToEnumConverterFactory
java.lang.String -> java.lang.Number : StringToNumberConverterFactory
java.lang.String -> java.util.Locale : StringToLocaleConverter
java.lang.String -> java.util.Properties : StringToPropertiesConverter
java.lang.String -> java.util.UUID : StringToUUIDConverter
java.util.Locale -> java.lang.String : ObjectToStringConverter
java.util.Properties -> java.lang.String : PropertiesToStringConverter
java.util.UUID -> java.lang.String : ObjectToStringConverter
```



### 自定义类型转换器

1. 创建自定义类型转换器

   ```java
   public class StringToDateConverter implements Converter<String, Date> {
       @Override
       public Date convert(String s) {
           if (s == null) {
               throw new RuntimeException("没有传递参数");
           }
           DateFormat df = new SimpleDateFormat("yyyy-MM-dd");
           try {
               return df.parse(s);
           } catch (ParseException e) {
               throw new RuntimeException("格式不匹配");
           }
       }
   }
   ```

   

2. 在 `spring.xml` 中配置自定义类型转换器

   ```xml
   <!-- 配置conversion-service -->
   <bean id="conversion" class="org.springframework.context.support.ConversionServiceFactoryBean">
       <property name="converters">
           <set>
               <bean class="kzq.springmvc.utils.StringToDateConverter"/>
           </set>
       </property>
   </bean>
   ```

   

3. 最后，在 `<mvc:annotation-driven/>` 中注册

   ```xml
   <mvc:annotation-driven conversion-service="conversion"/>
   ```

   

# 请求参数绑定🍀

请求参数绑定：将请求参数串中的 value 值获取到之后，再进行类型转换，然后将转换后的值赋给 Controller 类中方法的形参，这个过程就是参数绑定。

请求参数格式：默认是 key/value 格式



## 简单数据类型

简单类型指的就是八种基本类型数据以及它们的包装类，还有 String 类型。

必要条件：Http 请求参数的 key 和 Controller 中方法的形参名称一致

Handler：

```java
@RequestMapping("/test")
public String bindParam01(String name, Float money) {
    System.out.println(name);
    System.out.println(money);
    return "success";
}
```

请求：

```sh
GET /test?name=root&money=100
```



## POJO类型

Handler：

```java
@ResponseBody
@PostMapping("/user/saveUser")
public User testPojo(User user) {
    System.out.println(user);
    return user;
}
```



1. 实体类

   ```sh
   POST http://localhost:8080/user/saveUser
   params = {
       "id": 2,
       "userName": "kiana",
       "age": 15,
       "address": "脚盆村",
   }
   ```

   

2. 关联的实体类

   此时使用  "对象.属性" 表示参数

   ```sh
   POST http://localhost:8080/user/saveUser
   params = {
       "id": 2,
       "userName": "kiana",
       "age": 15,
       "address": "脚盆村",
   }
   ```

   

## 数组和集合类

在表单中使用 [index] 的格式确定存入集合中的索引位置

```html
<form action="param/two" method="post">
    <h3> 基本数据类型封装 </h3>
    id：<input type="text" name="id"> <br>
    姓名：<input type="text" name="name"> <br>
    金额：<input type="text" name="money"> <br>
    <h3> list封装 </h3>
    用户id：<input type="text" name="list[0].id"> <br>
    用户名：<input type="text" name="list[0].username"> <br>

    用户id：<input type="text" name="list[1].id"> <br>
    用户名：<input type="text" name="list[1].username"> <br>
    <h3> map封装 </h3>
    用户id：<input type="text" name="map['key'].id"> <br>
    用户名：<input type="text" name="map['key'].username"> <br>
    <input type="submit" value="提交">
</form>
```



## 默认类型绑定

SpringMVC 中，有支持的默认类型的绑定，直接在 Controller 方法形参上定义默认类型的对象，就可以使用这些对象。

- `HttpServletRequest` 对象

- `HttpServletResponse` 对象

- `HttpSession` 对象

- `Model` / `ModelMap` 对象

```java
@RequestMapping("/http")
public String getServletAPI(HttpServletRequest request, HttpServletResponse response) {
    System.out.println(request);
    System.out.println(response);
    return "success";
}
```



### Servlet API

SpringMVC 支持使用原生 Servlet API 对象作为控制器方法的参数，这必须要引入 `servlet-api` 相关依赖。

```java
@RequestMapping(path = "/servlet")
public String testServlet(HttpServletRequest request, HttpServletResponse response, HttpSession session) {
    // ...
    return "success";
}
```





### Model/ModelMap

如果方法参数处使用 Model，返回值则使用 String 类型表示返回逻辑视图

```java
@GetMapping("/example")
public String test01(Model model) {
    User user = new User(1, 15, "kiana", "池水沟子", null);
    model.addAttribute("user", user);  
    model.addAttribute("msg", "百度");
    model.addAttribute("url", "https://baidu.com");
    return "example";
}
```



# 响应数据🍀

## 响应类型

### 返回void

如果控制器的方法返回值编写成 void ，执行程序报 404 的异常，默认查找 jsp 页面没有找到



### 返回字符串

Controller方法返回字符串可以指定逻辑视图的名称，再经过视图解析器解析为物理视图的地址。

```java
@RequestMapping(path = "/hello")
public String sayHello() {
    return "success";
}
```



### 返回ModelAndView

ModelAndView 对象是 Spring 提供的一个对象，可以用来调整具体视图

以下两种写法效果相同：

```java
@RequestMapping("/example")
public ModelAndView test() {
    User user = new User(1, 15, "kiana", "池水沟子", null);
    // 创建 ModelAndView 对象
    ModelAndView mv = new ModelAndView();
    // 数据存储到mv对象中，实际上底层存入到了request域中
    mv.addObject("theUser", user);
  	// 指定返回的逻辑视图名
    mv.setViewName("success");
    return mv;
}

@RequestMapping("/example")
public ModelAndView test(ModelAndView mv) {
    User user = new User(1, 15, "kiana", "池水沟子", null);
    // 数据存储到mv对象中，实际上底层存入到了request域中
    mv.addObject("theUser", user);
  	// 指定返回的逻辑视图名
    mv.setViewName("success");
    return mv;
}
```





### 返回json

1. 解析json数据需要在maven中引入相关依赖

   ```xml
   <dependency>
     <groupId>com.fasterxml.jackson.core</groupId>
     <artifactId>jackson-databind</artifactId>
     <version>2.11.3</version>
   </dependency>
   ```

   

2. 使用 `@RequestBody` 获取json格式的请求内容，并用 `@ResponseBody` 自动封装为json格式并响应

   ```java
   @RequestMapping("/testAjax")
   @ResponseBody
   public User testAjax(@RequestBody String body){
       System.out.println(body);
       User user = new User();
       user.setId(10);
       user.setUsername("Yorua");
       user.setAge(18);
       return user;
   }
   ```
   
   





## 请求转发

使用 `forward` 关键字进行请求转发，`forward:转发的页面路径"` ，不经过视图解析器，所以需要编写完整的路径

```java
@RequestMapping("/forward01")
public void testForward01(HttpServletRequest request, HttpServletResponse response) 
  throws Exception {
  
    request.getRequestDispatcher("/WEB-INF/pages/dispatch.jsp").forward(request, response);
}


@RequestMapping("/forward02")
public String testForward02(){
  
    return "forward:/WEB-INF/pages/success.jsp";
}
```



## 请求重定向

使用 `redirect` 关键字进行重定向：`redirect:重定向路径"`

```java
@RequestMapping("/redirect01")
public void testRedirect01(HttpServletRequest request, HttpServletResponse response) throws Exception {
    String contextPath = request.getContextPath();
    System.out.println(contextPath);
    response.sendRedirect(contextPath + "/index.jsp");
}

@RequestMapping("/redirect02")
public String testRedirect02(){
    System.out.println("redirect");
    return "redirect:/index.jsp";
}    
```



## 内容协商

Java 编码配置：

```java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void configureContentNegotiation(ContentNegotiationConfigurer configurer) {
        configurer.mediaType("json", MediaType.APPLICATION_JSON);
        configurer.mediaType("xml", MediaType.APPLICATION_XML);
    }
}
```



XML配置：

```xml
<mvc:annotation-driven content-negotiation-manager="contentNegotiationManager"/>

<bean id="contentNegotiationManager" class="org.springframework.web.accept.ContentNegotiationManagerFactoryBean">
    <property name="mediaTypes">
        <value>
            json=application/json
            xml=application/xml
        </value>
    </property>
</bean>
```



## 静态资源处理

由于配置了 `<url-pattern>/</url-pattern>` ，静态资源会来到 DispatchServlet ，例如 index.html ，看哪个方法的RequestMapping 是 index.html，但并没有这样的映射，这会导致所有的静态资源（img、css、js）都会被拦截，从而不能被使用。

需要配置静态资源不进行拦截，即在配置文件添加如下配置：

- location：表示 webapp 目录下的包下的所有文件
- mapping：表示以 static 开头的所有请求路径

```xml
<!-- 前端控制器放行静态资源配置 -->
<mvc:resources mapping="/js/**" location="/js/"/>
<mvc:resources mapping="/css/**" location="css/"/>
<mvc:resources mapping="/images/**" location="images/"/>
```



> 为什么 jsp 可以被访问？
>
> 因为没有覆盖服务器中的 JspServlet 的配置







# 文件上传🍀

## 相关配置

文件上传需要在 maven 中引入相关依赖

```xml
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.1</version>
</dependency>
```



## 传统方式

```java
@RequestMapping("/upload")
public String fileUpload(HttpServletRequest request) throws Exception {
    String path = request.getSession().getServletContext().getRealPath("/uploads/");
    File file = new File(path);
    if (!file.exists()) {
        file.mkdirs();
    }
    DiskFileItemFactory factory = new DiskFileItemFactory();
    ServletFileUpload servletFileUpload = new ServletFileUpload(factory);
    List<FileItem> items = servletFileUpload.parseRequest(request);
    for (FileItem item : items) {
        System.out.println(item);  
        if (item.isFormField()) {
            // do nothing
        } else {
            String filename = item.getName();
            System.out.println(filename);
            String uuid = UUID.randomUUID().toString().replace("-", "");
            filename = uuid + "_" + filename;
            item.write(new File(path, filename));
            item.delete();
        }
    }
    return "success";
}
```



其中，FileItem对象的打印结果：

```
name=IMG_4993.jpeg, StoreLocation=/Users/yorua/Project/IdeaProject/repository/Tomcat/apache-tomcat-8.5.59/temp/upload_544601ad_8610_454b_9364_19ed3e6f11f0_00000000.tmp, size=330098 bytes, isFormField=false, FieldName=upload      	 	
```





## SpringMVC提供的方式

SpringMVC框架提供了 MultipartFile 对象，该对象表示上传的文件

在 `spring.xml` 中配置文件解析器

```xml
<!-- 配置文件解析器对象，要求bean-id必须是multipartResolver -->
<bean id="multipartResolver"
      class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    <property name="maxUploadSize" value="10485760"/>
</bean>
```



```java
@RequestMapping("/upload")
public String fileUpload(HttpServletRequest request, MultipartFile upload) throws Exception{
    String path = request.getSession().getServletContext().getRealPath("/uploads/");
    File file = new File(path);
    if (!file.exists()) {
        file.mkdirs();
    }
    String filename = upload.getOriginalFilename();
    String uuid = UUID.randomUUID().toString().replace("-", "");
    filename = uuid + "_" + filename;
    upload.transferTo(new File(path, filename));
    return "success";
}
```

注意： MultipartFile 对象的变量名  `upload`  必须和表单中的  `name` 属性的值一致

```html
<form action="file/upload" method="post" enctype="multipart/form-data">
    选择文件：<input type="file" name="upload"> <br>
    <input type="submit" value="上传">
</form>
```



## 跨服务器上传

在 maven 中引入相关依赖

```xml
<dependency>
    <groupId>com.sun.jersey</groupId>
    <artifactId>jersey-client</artifactId>
    <version>1.18.1</version>
</dependency>
```





# Springboot自动配置MVC🍀

## 扩展自动配置

如果想要保持 Springboot 对 MVC 模块的自动配置，同时增加更多的配置（拦截器、格式化转化器等），可以创建自己的配置类

使用 `@Configuration` 并实现 `WebMvcConfigurer` ，但不要使用 `@EnableWebMvc`







## 全面接管 MVC 配置

使用 `@Configuration` 并实现 `WebMvcConfigurer` ，同时标注 `@EnableWebMvc` ，就会禁用 Springboot 关于 MVC 的自动配置



## Springboot中的配置类

### Properties类

#### WebProperties

`WebProperties` 是一个配置类，和配置文件中前缀为 `spring.web` 的字段绑定

```java
@ConfigurationProperties("spring.web")
public class WebProperties {
		// ...
}
```



#### Resources

`Resources` 是 WebProperties 中的一个静态内部类

```java
@ConfigurationProperties("spring.web")
public class WebProperties {

		// ...
  
	public static class Resources {
    
				// ...    
  }  
}
```



#### ResourceProperties

该类目前已经过时 `@Deprecated`

`ResourceProperties ` 是一个配置类，和配置文件中前缀为 `spring.resources` 的字段绑定

该类继承了 `Resources`，它是 `WebProperties` 中的一个 public 的静态内部类。

```java
@Deprecated
@ConfigurationProperties(prefix = "spring.resources", ignoreUnknownFields = false)
public class ResourceProperties extends Resources {
		// ...
}
```



#### WebMvcProperties

`WebMvcProperties` 是一个配置类，和配置文件中前缀为 `spring.mvc` 的字段绑定

```java
@ConfigurationProperties(prefix = "spring.mvc")
public class WebMvcProperties {
		// ...
}
```



### 自动配置类

#### WebMvcAutoConfiguration

```java
package org.springframework.boot.autoconfigure.web.servlet;

@Configuration(proxyBeanMethods = false)
@ConditionalOnWebApplication(type = Type.SERVLET)
@ConditionalOnClass({ Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class })
@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)
@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE + 10)
@AutoConfigureAfter({ DispatcherServletAutoConfiguration.class, TaskExecutionAutoConfiguration.class,
		ValidationAutoConfiguration.class })
public class WebMvcAutoConfiguration {
		// ...
}
```





#### EnableWebMvcConfiguration

`EnableWebMvcConfiguration`  是 `WebMvcAutoConfiguration` 中的一个 public 的静态内部类

负责自动配置组件

```java
@Configuration(proxyBeanMethods = false)
@EnableConfigurationProperties(WebProperties.class)
public static class EnableWebMvcConfiguration extends DelegatingWebMvcConfiguration implements ResourceLoaderAware {
		// ...
}
```



#### WebMvcConfigurationAdapter

`WebMvcConfigurationAdapter` 是 `WebMvcAutoConfiguration`中的一个 public 的静态内部类，实现了`WebMvcConfigurer` 接口。

负责自动配置组件，包含了 `EnableWebMvcConfiguration` 中自动配置的内容。

```java
@Configuration(proxyBeanMethods = false)
@Import(EnableWebMvcConfiguration.class)
@EnableConfigurationProperties({ WebMvcProperties.class,
    org.springframework.boot.autoconfigure.web.ResourceProperties.class, WebProperties.class })
@Order(0)
public static class WebMvcAutoConfigurationAdapter implements WebMvcConfigurer {
		// ...
}
```



## SpringMVC中的配置类



### @EnableWebMvc

使用 `@EnableWebMvc` 表示完全接管 SpringMVC 的配置，而不使用 Springboot 自动配置，即所有配置都需要自己重写。

使用该注解后 `WebMvcAutoConfiguration` 会失效，因为 `WebMvcAutoConfiguration` 生效的条件之一是 `@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)`

该注解就是为了引入一个 `DelegatingWebMvcConfiguration.class` 配置类，该配置类继承自 `WebMvcConfigurationSupport` 

```java
package org.springframework.web.servlet.config.annotation;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Documented
@Import(DelegatingWebMvcConfiguration.class)
public @interface EnableWebMvc {
}
```





### WebMvcConfigurer

`WebMvcConfigurer` 是一个接口，所有的方法都被 `default` 修饰，所以并不强制它的实现类实现所有的方法。

```java
package org.springframework.web.servlet.config.annotation;


public interface WebMvcConfigurer {

	default void configurePathMatch(PathMatchConfigurer configurer) {
	}

	// 内容协商
	default void configureContentNegotiation(ContentNegotiationConfigurer configurer) {
	}

	default void configureAsyncSupport(AsyncSupportConfigurer configurer) {
	}

	default void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
	}

  // 转换器
	default void addFormatters(FormatterRegistry registry) {
	}

	// 拦截器
	default void addInterceptors(InterceptorRegistry registry) {
	}

	// 静态资源映射
	default void addResourceHandlers(ResourceHandlerRegistry registry) {
	}

	// 跨域
	default void addCorsMappings(CorsRegistry registry) {
	}

  // 页面跳转
	default void addViewControllers(ViewControllerRegistry registry) {
	}

	// 视图解析器
	default void configureViewResolvers(ViewResolverRegistry registry) {
	}

  // 参数解析器
	default void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
	}

  // 返回值解析器
	default void addReturnValueHandlers(List<HandlerMethodReturnValueHandler> handlers) {
	}

	// 信息转换器
	default void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
	}

	default void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
	}

	default void configureHandlerExceptionResolvers(List<HandlerExceptionResolver> resolvers) {
	}

	default void extendHandlerExceptionResolvers(List<HandlerExceptionResolver> resolvers) {
	}

	@Nullable
	default Validator getValidator() {
		return null;
	}

	@Nullable
	default MessageCodesResolver getMessageCodesResolver() {
		return null;
	}
}
```





### WebMvcConfigurerAdapter

❗目前已经被弃用 `@Deprecated`

`WebMvcConfigurerAdapter` 是一个抽象类，实现了 `WebMvcConfigurer` ，有许多重写后的空实现。

由于 `WebMvcConfigurer` 中的方法都已经被 `default` 修饰，那么 `WebMvcConfigurerAdapter` 也就不再有用了。

```java
@Deprecated
public abstract class WebMvcConfigurerAdapter implements WebMvcConfigurer {

	@Override
	public void addInterceptors(InterceptorRegistry registry) {
	}

	@Override
	public void addResourceHandlers(ResourceHandlerRegistry registry) {
	}
  
  // ...
}
```





### WebMvcConfigurationSupport

该类用于导入 SpringMVC 的配置，具体见其子类 `DelegatingWebMvcConfiguration`

如果有配置类被标注了 `@EnableWebMvc` ，那么 `WebMvcConfigurationSupport` 就会被注入到容器中。

因为 `@EnableWebMvc` 会导入 `WebMvcConfigurationSupport`  的子类 `DelegatingWebMvcConfiguration`，于是 

`WebMvcAutoConfiguration` 就不会生效。

一个更直接的方法是创建一个子类直接继承 `WebMvcConfigurationSupport` ，标注 `@Configuration` ，再重写相关的方法，这样就不会走 `DelegatingWebMvcConfiguration` 的逻辑了。

```java
package org.springframework.web.servlet.config.annotation;

public class WebMvcConfigurationSupport implements ApplicationContextAware, ServletContextAware {
		
		// ...

}
```



### DelegatingWebMvcConfiguration

`DelegatingWebMvcConfiguration` 是 `WebMvcConfigurationSupport` 的子类

其中有一个 `setConfigurers()` 方法，`@Autowired` 表示从 ioc 容器中获取所有的 `WebMvcConfigurer` 类型作为该方法的参数，而扩展 SpringMVC 时，我们的自定义配置类需要实现 `WebMvcConfigurer` ，并用 `@Configuration` 加入容器。所以该方法就是获取容器中所有关于 SpringMVC 的相关配置类。

之后会调用所有配置类的相关方法，例如调用 `addInterceptors` 方法，添加自定义的拦截器。

由于 `EnableWebMvcConfiguration` 继承了 `DelegatingWebMvcConfiguration` ，那么自然可以达成在保持自动配置的情况下，再启用用户的各种自定义配置。



```java
package org.springframework.web.servlet.config.annotation;

@Configuration(proxyBeanMethods = false)
public class DelegatingWebMvcConfiguration extends WebMvcConfigurationSupport {
    private final WebMvcConfigurerComposite configurers = new WebMvcConfigurerComposite();


    @Autowired(required = false)
    public void setConfigurers(List<WebMvcConfigurer> configurers) {
      if (!CollectionUtils.isEmpty(configurers)) {
        this.configurers.addWebMvcConfigurers(configurers);
      }
    }
  
    @Override
    protected void addInterceptors(InterceptorRegistry registry) {
      this.configurers.addInterceptors(registry);
    }  
    
    // ...
}
```





# MVC流程原理🍀

## DispatcherServlet 处理请求

*service -> doGet -> processRequest -> doService -> doDispatch*

`org.springframework.web.servlet.DispatcherServlet`  的 `doDispatch()` 方法

Servlet 接收到 request 请求，最终由 `DispatcherServlet` 的 `doDispatch()` 方法进行处理。





## 获取 Handler

```java
// doDispatch() 方法中
mappedHandler = this.getHandler(processedRequest);
```

从 HandlerMapping 中查找能够处理请求的 Handler

```java
@Nullable
protected HandlerExecutionChain getHandler(HttpServletRequest request) throws Exception {
  if (this.handlerMappings != null) {
    for (HandlerMapping mapping : this.handlerMappings) {
      HandlerExecutionChain handler = mapping.getHandler(request);
      if (handler != null) {
        return handler;
      }
    }
  }
  return null;
}
```

一共有 5 种 HandlerMapping，这里根据对应的 HandlerMapping 获取具体的 Handler

例如，如果是 `@RequestMapping` 注解标注的方法，则由 `RequestMappingHandlerMappping` 处理。

<img src="http://store.secretcamp.cn/uPic/image-20210527093228828202105270932291622079149BA2pEMBA2pEM.png" alt="image-20210527093228828" style="zoom:50%;" />

完成后 Handler 会被封装成 `HanlderMethod`

<img src="http://store.secretcamp.cn/uPic/image-20210527103922169202105271039221622083162DnmEoHDnmEoH.png" alt="image-20210527103922169" style="zoom:50%;" />





## 获取  HandlerAdapter

为当前的 Handler 寻找一个 HandlerAdapter

```java
// doDispatch() 方法中
HandlerAdapter ha = this.getHandlerAdapter(mappedHandler.getHandler());
```



创建一个迭代器，从所有的 HandlerAdapter 查找一个能够支持当前 Handler 的 HandlerAdapter。

```java
protected HandlerAdapter getHandlerAdapter(Object handler) throws ServletException {
    if (this.handlerAdapters != null) {
        Iterator var2 = this.handlerAdapters.iterator();

        while(var2.hasNext()) {
            HandlerAdapter adapter = (HandlerAdapter)var2.next();
            if (adapter.supports(handler)) {
                return adapter;
            }
        }
    }

    throw new ServletException("No adapter for handler [" + handler + "]: The DispatcherServlet configuration needs to include a HandlerAdapter that supports this handler");
}
```



HandlerAdapter 的类型一共有 4 种

<img src="http://store.secretcamp.cn/uPic/image-20210527094545903202105270945461622079946hqN5qIhqN5qI.png" alt="image-20210527094545903" style="zoom:50%;" />



## 执行 Handler 中的方法

`handle()` 是最外层的方法，它的核心目标是执行 Handler 中的方法，为了完成这个目标，`handle()`  内部进行了层层封装

参数方面主要用到了 request、response、handler

```java
// 执行 handler 方法，并返回一个 ModelAndView
mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
```

`handler()` 方法直接返回 `handleInternal()` 方法

```java
@Override
@Nullable
public final ModelAndView handle(HttpServletRequest request, HttpServletResponse response, Object handler)
    throws Exception {

  return handleInternal(request, response, (HandlerMethod) handler);
}
```



在  `handleInternal()` 执行了一个重要方法 `invokeHandlerMethod()` ，由该方法进一步执行核心目标

```java
mav = invokeHandlerMethod(request, response, handlerMethod);
```

事实上，`invokeHandlerMethod()` 是 HandlerAdapter 中定义的方法，所以 "执行 Handler 中的方法" 这件事实际上是由 HandlerAdapter 来完成的，例如这里就是由 `RequestMappingHandlerAdapter` 中的 `invokeHandlerMethod()` 方法来完成。



## HandlerAdapter 执行逻辑

HandlerAdapter 首先会执行  `invokeHandlerMethod()` ，这是一个入口方法

### 获取 invocableMethod 

invocableMethod 封装了 handler 的元信息

```java
ServletInvocableHandlerMethod invocableMethod = createInvocableHandlerMethod(handlerMethod);
```



### 获取参数解析器列表

确定将要执行的目标方法的每一个参数值，必须要有参数解析器，这里会将参数解析器列表传入 invocableMethod 的 resolver 属性中。



```java
if (this.argumentResolvers != null) {
  invocableMethod.setHandlerMethodArgumentResolvers(this.argumentResolvers);
}
```

后续处理中根据不同的参数类型，会选择对应的参数解析器

参数解析器一共有 27 个

<img src="http://store.secretcamp.cn/uPic/image-20210527095728676202105270957281622080648RKGCMuRKGCMu.png" alt="image-20210527095728676" style="zoom: 50%;" />

根据 Controller 函数签名中参数的不同，会选择不同的参数解析器。

例如，在后续执行 Handler 方法的流程中：

1. 参数标注了 `@RequestParam` ，会选择 `RequestParamMethodArgumentResolver` 解析器。

2. 参数使用了原生 Servlet API ，会使用 `ServletRequestMethodArgumentResolver` 解析器

3. 参数使用了 Map，会使用 `MapMethodProcessor` 解析器。
4. 参数使用了自定义 pojo 类型，会使用 `ServletModelAttributeMethodProcessor` 解析器



### 获取返回值处理器列表

确定将要执行的目标方法的返回值，必须要有返回值处理器，这里会将返回值处理器列传入 invocableMethod 的 returnValueHandlers 属性中。



```java
if (this.argumentResolvers != null) {
  invocableMethod.setHandlerMethodArgumentResolvers(this.argumentResolvers);
}
```

后续处理会根据不同的返回值类型，会选择对应的返回值处理器

返回值处理器一共有 15 个

<img src="http://store.secretcamp.cn/uPic/image-202105271000246632021052710002416220808249VMH7s9VMH7s.png" alt="image-20210527100024663" style="zoom:50%;" />





### 执行目标方法

再由 `invokeAndHandle()` 来处理执行 Hanlder 中方法的更深一层逻辑

```java
invocableMethod.invokeAndHandle(webRequest, mavContainer);
```



`invokeForRequest()` 方法真正的执行了 Controller 中的定义的方法，并获取一个方法处理后的返回值

```java
Object returnValue = invokeForRequest(webRequest, mavContainer, providedArgs);
```



### invokeForRequest 

1. 获取方法的各种参数，这里则是根据参数的元信息，调用之前传入的参数解析器列表，查找对应的参数解析器，然后再解析出具体的参数。

   ```java
   Object[] args = this.getMethodArgumentValues(request, mavContainer, providedArgs);
   ```

   解析完所有参数后，参数被保存在 args 中，再执行 `doInvoke` 

2.  `doInvoke()` 方法中的 `method.invoke()` 方法使用反射，真正的执行了 Controller 中用户自定义的方法 

   ```java
   @Nullable
   protected Object doInvoke(Object... args) throws Exception {
       Method method = this.getBridgedMethod();
       ReflectionUtils.makeAccessible(method);
       try {
           return KotlinDetector.isSuspendingFunction(method) ? CoroutinesUtils.invokeSuspendingFunction(method, this.getBean(), args) : method.invoke(this.getBean(), args);   //  <----
       } catch 
         // ...
       }
   }
   
   ```





```
mappedHandler.applyPostHandle(processedRequest, response, mv);
```



### 处理拦截器

```java
void applyPostHandle(HttpServletRequest request, HttpServletResponse response, @Nullable ModelAndView mv)
    throws Exception {

  for (int i = this.interceptorList.size() - 1; i >= 0; i--) {
    HandlerInterceptor interceptor = this.interceptorList.get(i);
    interceptor.postHandle(request, response, this.handler, mv);
  }
}
```





```
processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);
```

