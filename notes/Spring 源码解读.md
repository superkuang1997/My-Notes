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

-  `ApplicationContext`：在启动的时候就把所有的 bean 全部实例化，也可以配置 `lazy-init` 实现延迟实例化； 





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

   3. 如果 bean 实现了 `InitializingBean` 接口，执行 `afterPropertiesSet()` 方法。

   4. 如果 bean 在配置文件中的定义包含 `init-method` 属性，执行指定的方法。

   5. 如果容器中有 `BeanPostProcessor` 的实现，执行 `postProcessAfterInitialization()` 方法逻辑。

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

14. `registerBeanDefinitions(doc, resource)` 方法，首先创建了一个`BeanDefinitionDocumentReader` 的对象，再调用其 `registerBeanDefinitions(doc, createReaderContext(resource))` 方法，将 `Document` 对象解析，并将解析后生成的 bean 对象注入到 IOC 容器中，最终该方法返回注入容器的 bean 的个数 (int)。

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





# Spring FAQ🍀

## Spring采用了哪些设计模式？

1. 简单工厂模式

   Spring 中的 BeanFactory 就是简单工厂模式的体现，根据传入一个唯一的标识来获得 bean 对象，但是否是在传入参数后创建还是传入参数前创建这个要根据具体情况来定。

2. 工厂方法模式

   通常由应用程序直接使用 new 创建新的对象，为了将对象的创建和使用相分离，采用工厂模式,即应用程序将对象的创建及初始化职责交给工厂对象。

3. 单例模式

   保证一个类仅有一个实例，并提供一个访问它的全局访问点。 

   在 Spring 中scope=singleton 的对象都是单例对象，Spring 中的单例模式完成了后半句话，即提供了全局的访问点BeanFactory。但没有从构造器级别去控制单例，这是因为 Spring 管理的是任意的 Java 对象。

4. 代理模式

   Spring 的 AOP 是基于代理模式







# Springboot自动配置原理🍀

## 核心思想

SpringBoot 定义了一套接口规范，这套规范规定：SpringBoot 在启动时会扫描外部引用 jar 包中的`META-INF/spring.factories`文件，将文件中配置的类型信息加载到 Spring 容器，并执行类中定义的各种操作。对于外部 jar 来说，只需要按照 SpringBoot 定义的标准，就能将自己的功能装置进 SpringBoot。



## 依赖管理

springboot 配置文件中的父项目是 `spring-boot-starter-parent`

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.4.1</version>
    <relativePath/>
</parent>
```



`spring-boot-starter-parent` 的父项目是 `spring-boot-dependencies`

```xml
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.4.1</version>
  </parent>
```

在 `spring-boot-dependencies` 中，几乎声明了所有开发中常用的依赖的版本号



### 自动版本仲裁

自动版本仲裁是指在引入依赖时，无需关心版本号。

对于 `spring-boot-dependencies` 中配置过的依赖，可以应用自动版本仲裁，但是如果没有配置过，则必须在引入依赖时声明相关的版本。

例如引入 mysql 相关的依赖，可以不声明版本号

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```

因为在 `spring-boot-dependencies` 的 `<properties>` 中已经声明过 mysql 的版本了

```xml
<mysql.version>8.0.22</mysql.version>
```

如果想要自定义 Springboot 已经声明过的版本，可以直接在 `pom.xml` 中设置相关属性，当然也可以直接在 `<dependency>` 中带上版本号。

```xml
<properties>
    <mysql.version>5.1.43</mysql.version>
</properties>
```



### 场景启动器

[starts](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-starter) 被称为场景启动器

官方的 starters 格式为 `spring-boot-starter-*` 

非官方的 starters 格式为 `thirdpartyproject-spring-boot-starter`





## @SpringBootApplication

SpringBoot中可以配置的属性： [Application Properties](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-application-properties.html#common-application-properties)

`@SpringBootApplication` 的元注解：

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {

}
```





### @SpringBootConfiguration

`@SpringBootConfiguration` 使用了 `@Configuration` ，即表示当前类是一个配置类

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Configuration
public @interface SpringBootConfiguration {
    @AliasFor(
        annotation = Configuration.class
    )
    boolean proxyBeanMethods() default true;
}
```





### @EnableAutoConfiguration

`@EnableAutoConfiguration` 是实现自动装配的核心注解，它是 `@AutoConfigurationPackage` 和 `@Import` 的合成

自动装配核心功能的实现实际是通过`@Import` 导入的 `AutoConfigurationImportSelector`类。

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import({AutoConfigurationImportSelector.class})
public @interface EnableAutoConfiguration {
    String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";

    Class<?>[] exclude() default {};

    String[] excludeName() default {};
}
```



#### 用户开发组件的配置

`@AutoConfigurationPackage` 用于将指定包下的所有组件注入容器

使用了 `@Import({Registrar.class})` 将 `Registrar` 中的相关方法返回值注入容器

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@Import({Registrar.class})
public @interface AutoConfigurationPackage {
    String[] basePackages() default {};

    Class<?>[] basePackageClasses() default {};
}
```

`Registrar` 类中有一个 `registerBeanDefinitions()` 方法，该方法调用了 `register()` 方法，用于给容器批量注入组件。

metadata 表示被注解类的元信息，而这个被注解类就是主程序类 `DemoApplication.class` ，这里通过 metadata  获取了`DemoApplication.class` 所在包的包名并传入 `register()` 方法，即将 `DemoApplication.class` （启动类）所在包下的所有组件注册到容器之中，即将用户程序代码中自定义的组件注入容器。

```java
public void registerBeanDefinitions(AnnotationMetadata metadata, BeanDefinitionRegistry registry) {
    AutoConfigurationPackages.register(registry, (String[])(new AutoConfigurationPackages.PackageImports(metadata)).getPackageNames().toArray(new String[0]));
}
```



#### 约定组件的配置

`AutoConfigurationImportSelector` 中有一个 `selectImports()` 方法，该方法主要用于获取所有符合条件的类的全限定类名，将这些类被加载到 IOC 容器中。

该方法中的主要逻辑在于 `getAutoConfigurationEntry()` 方法，方法的值最终被转为 String[] 返回

```java
public String[] selectImports(AnnotationMetadata annotationMetadata) {
    if (!this.isEnabled(annotationMetadata)) {
        return NO_IMPORTS;
    } else {
        AutoConfigurationImportSelector.AutoConfigurationEntry autoConfigurationEntry = this.getAutoConfigurationEntry(annotationMetadata);
        return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
    }
}
```

`getAutoConfigurationEntry()` 方法的核心在于 `getCandidateConfigurations()` 方法，用于获取候选的组件，得到候选的组件后，经过一系列排除、过滤操作后得到最终应该注入容器的组件。

> Springboot 2.4.5 可以获取 133 个候选组件

```java
protected AutoConfigurationImportSelector.AutoConfigurationEntry getAutoConfigurationEntry(AnnotationMetadata annotationMetadata) {
    // 1. 判断自动状态有没有打开，可在application.properties中配置
    if (!this.isEnabled(annotationMetadata)) {
        return EMPTY_ENTRY;
    } else {
        AnnotationAttributes attributes = this.getAttributes(annotationMetadata);
        // 获取候选配置
        List<String> configurations = this.getCandidateConfigurations(annotationMetadata, attributes);
        configurations = this.removeDuplicates(configurations);
        Set<String> exclusions = this.getExclusions(annotationMetadata, attributes);
        this.checkExcludedClasses(configurations, exclusions);
        configurations.removeAll(exclusions);
        configurations = this.getConfigurationClassFilter().filter(configurations);
        this.fireAutoConfigurationImportEvents(configurations, exclusions);
        return new AutoConfigurationImportSelector.AutoConfigurationEntry(configurations, exclusions);
    }
}
```



`getCandidateConfigurations()` 方法的核心在于 `loadFactoryNames()` 方法 

```java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    List<String> configurations = SpringFactoriesLoader.loadFactoryNames(this.getSpringFactoriesLoaderFactoryClass(), this.getBeanClassLoader());
    Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you are using a custom packaging, make sure that file is correct.");
    return configurations;
}
```



`loadFactoryNames()` 方法的核心在于 `loadSpringFactories()` 方法

```java
public static List<String> loadFactoryNames(Class<?> factoryType, @Nullable ClassLoader classLoader) {
    ClassLoader classLoaderToUse = classLoader;
    if (classLoader == null) {
        classLoaderToUse = SpringFactoriesLoader.class.getClassLoader();
    }

    String factoryTypeName = factoryType.getName();
    return (List)loadSpringFactories(classLoaderToUse).getOrDefault(factoryTypeName, Collections.emptyList());
}
```



在 `loadSpringFactories()` 方法中，最终需要从各包的 `"META-INF/spring.factories"` 路径下加载资源

```java
Enumeration urls = classLoader.getResources("META-INF/spring.factories");
```



例如 `spring-boot-autoconfigure-2.4.5.jar` 中，就存在 `META-INF/spring.factories` 文件，Springboot 就是读取了这些文件中的配置信息，在该配置文件中所有写明的自动配置类，都是即将被导入 IOC 容器的候选类。

需要注意的是，不是每个包都有 `META-INF/spring.factories` 文件

<img src="http://store.secretcamp.cn/uPic/image-20210512095751721202105120957521620784672Y52uPoY52uPo.png" alt="image-20210512095751721" style="zoom:56%;" />

<img src="http://store.secretcamp.cn/uPic/image-20210512100027389202105121000271620784827lGFTxslGFTxs.png" alt="image-20210512100027389" style="zoom: 33%;" />







## XXXAutoConfiguration分析

取  `spring-boot-autoconfigure-2.4.5.jar`  中的几个类进行分析

### AopAutoConfiguration

以 AOP 的配置为例，`AopAutoConfiguration` 中有两个静态内部类，它们都是配置类：

- `ClassProxyingConfiguration`
- `AspectJAutoProxyingConfiguration`

```java
@Configuration(
    proxyBeanMethods = false
)
// 表示只有 spring.aop.auto=true 时，以下配置才生效
// matchIfMissing = true 表示即使不配置 spring.aop.auto=true ，也认为 spring.aop.auto=true
@ConditionalOnProperty(
    prefix = "spring.aop",
    name = {"auto"},
    havingValue = "true",
    matchIfMissing = true
)
public class AopAutoConfiguration {
    public AopAutoConfiguration() {
    }

    @Configuration(
        proxyBeanMethods = false
    )
    // 只有不存在 org.aspectj.weaver.Advice 类时，以下配置才生效
    // 意思是如果要用以下配置类，就不能导入aspectj的jar包
    @ConditionalOnMissingClass({"org.aspectj.weaver.Advice"})
  	// 表示只有 spring.aop.proxy-target-class=true 时，以下配置才生效
    // matchIfMissing 表示以上配置中的 true 是默认情况
    @ConditionalOnProperty(
        prefix = "spring.aop",
        name = {"proxy-target-class"},
        havingValue = "true",
        matchIfMissing = true
    )
    static class ClassProxyingConfiguration {
				// ...
    }

    @Configuration(
        proxyBeanMethods = false
    )
    // 只有存在 Advice 类时，以下配置才生效，而 Advice 是指 org.aspectj.weaver.Advice;
  	// 如果没有导入 aspectj 包，以下配置就不生效
    // 所以如果导入了 aspectj 包，就用下面的配置
    @ConditionalOnClass({Advice.class})  
    static class AspectJAutoProxyingConfiguration {
        // ...
    }
}

```



### DispatcherServletAutoConfiguration

```java
@AutoConfigureOrder(-2147483648)
@Configuration(
    proxyBeanMethods = false
)
@ConditionalOnWebApplication(
    type = Type.SERVLET
)
@ConditionalOnClass({DispatcherServlet.class})
@AutoConfigureAfter({ServletWebServerFactoryAutoConfiguration.class})
public class DispatcherServletAutoConfiguration {
    public static final String DEFAULT_DISPATCHER_SERVLET_BEAN_NAME = "dispatcherServlet";
    public static final String DEFAULT_DISPATCHER_SERVLET_REGISTRATION_BEAN_NAME = "dispatcherServletRegistration";

    public DispatcherServletAutoConfiguration() {
    }

    @Order(2147483637)
    private static class DispatcherServletRegistrationCondition extends SpringBootCondition {
				// ...
    }

    @Order(2147483637)
    private static class DefaultDispatcherServletCondition extends SpringBootCondition {
				// ...
    }

    @Configuration(
        proxyBeanMethods = false
    )
    @Conditional({DispatcherServletAutoConfiguration.DispatcherServletRegistrationCondition.class})
    @ConditionalOnClass({ServletRegistration.class})
    // 将 WebMvcProperties.class 与当前配置类绑定，同时注入容器
    @EnableConfigurationProperties({WebMvcProperties.class})
    @Import({DispatcherServletAutoConfiguration.DispatcherServletConfiguration.class})
    protected static class DispatcherServletRegistrationConfiguration {
				// ...
    }

    @Configuration(
        proxyBeanMethods = false
    )
    @Conditional({DispatcherServletAutoConfiguration.DefaultDispatcherServletCondition.class})
    @ConditionalOnClass({ServletRegistration.class})
    @EnableConfigurationProperties({WebMvcProperties.class})
    protected static class DispatcherServletConfiguration {
				// ...
    }
}
```





## XXXProperties分析

在自动配置类中，往往有 `@EnableConfigurationProperties` 注解，可以让自动配置类与一个 `XXXProperties` 属性类进行绑定。

例如，`WebMvcProperties` 就代表了 Springboot 文件中以 `spring.mvc` 为前缀的属性。

```java
@ConfigurationProperties(prefix = "spring.mvc")
public class WebMvcProperties {
		// ...
}
```

