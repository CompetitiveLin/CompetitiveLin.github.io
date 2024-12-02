---
title: SpringBoot
categories: []
tags: [backend]
date: 2023-08-10T15:08:32+800
last_modified_at: 
pin: true
---

Spring, SpringBoot, Spring MVC 区别：
- Spring框架(Framework)是最流行的Java应用程序开发框架。 Spring框架的主要功能是依赖项注入或控制反转(IoC)。
- Spring MVC是Spring的一个MVC框架，包含前端视图，文件配置等。XML和config配置比较复杂。
- Spring Boot 是为简化Spring配置的快速开发整合包，允许构建具有最少配置或零配置的独立应用程序。


## SpringBoot项目启动流程：
- 总体来说分两部分，先初始化 SpringApplication，再运行 SpringApplication。

运行 SpringApplication 又分为：
1、获取并启动监听器
2、根据监听器和参数来创建运行环境
3、准备 Banner 打印器
4、创建 Spring 容器
5、Spring 容器前置处理（将前几步生成的监听器，Banner打印器和环境等配置到容器中）
6、刷新容器
7、Spring 容器后置处理（空方法）
8、发出结束执行的事件通知
9、返回容器


Spring核心之控制反转（IOC）和依赖注入（DI）：
- **IOC是一种设计思想**，将设计好的Bean对象交给容器控制，而不是在传统的在对象内部直接控制。用@Configutation + @Bean的方式。用通俗的话解释就是在容器里创建Bean对象，在需要的时候取出使用即可。
- **DI是一种实现方式**，将应用程序依赖的对象注入到容器中。

### IoC

refresh() 的作用：在创建IoC容器前，如果已经有容器存在，则需要把已有的容器销毁和关闭，以保证在refresh之后使用的是新建立起来的IoC容器。refresh的作用类似于对IoC容器的重启，在新建立好的容器中对容器进行初始化，对Bean定义资源进行载入。


[Spring创建Bean的三步（由反射创建的）](https://www.cnblogs.com/three-fighter/p/16007800.html)：
- 实例化，`AbstractAutowireCapableBeanFactory` 的 `createBeanInstance` 方法
- 属性注入，`AbstractAutowireCapableBeanFactory` 的 `populateBean` 方法
- 初始化，`AbstractAutowireCapableBeanFactory` 的 `initializeBean` 方法
  - 调用Aware接口（如果实现了Aware接口）
  - postProcessBeforeInitialization
  - afterPropertiesSet
  - init-method
  - postProcessAfterInitialization

Spring启动时先扫描所有Bean信息，BeanDefinition存储日常给Spring Bean定义的元数据。然后存储BeanDefinition的BeanDefinitionMap，是根据字典序依次创建Bean对象。

## 两种IOC容器

IOC的实现原理是工厂模式加反射机制。

- BeanFactory，只提供了实例化对象和获取对象的功能，使用懒加载机制，不支持国际化和基于依赖的注解
- ApplicationContext，其拓展了BeanFactory接口，使用即时加载的机制，它是在Ioc启动时就一次性创建所有的Bean，支持国际化和基于依赖的注解，包括AnnotationConfigApplicationContext、ClassPathXmlApplicationContext、FileSystemXmlApplicationContext等


[三级缓存解决循环依赖](https://developer.aliyun.com/article/766880)：
- 前提，出现循环依赖的Bean必须是单例；依赖注入的方式不能全是构造器注入（通过setter方法进行依赖注入，全是构造器注入则无法解决循环依赖）。
- getSingleton(beanName)方法三级缓存
  1. singletonObjects，一级缓存，存储的是所有创建好了的**单例Bean对象**
  2. earlySingletonObjects，二级缓存，完成实例化，但是还未进行属性注入及初始化的**提前暴露的对象**
  3. singletonFactories，三级缓存，存放**生产对象的工厂**，并且每次从这个工厂中拿到的对象都是不一样的，二级缓存中存储的就是从这个工厂中获取到的对象，如果Bean存在AOP的话，返回的是AOP的**代理对象**，提前进行了代理，避免对后面重复创建代理对象。对象加入三级缓存的前提是执行了构造器，因此全是构造器注入的循环依赖无法解决。

![](https://raw.githubusercontent.com/CompetitiveLin/ImageHostingService/picgo/imgs/202305231628567.png)


为什么需要二级缓存：
如果出现循环依赖+aop时，多个地方注入这个动态代理对象需要保证都是同一个对象。如果只使用这两层缓存，在使用三级缓存中的工厂对象生成的动态代理对象都是新创建的，循环依赖的时候，注入到别的bean里面去的那个动态代理对象和最终这个bean在初始化后自己创建的bean地址值不一样。如果 Spring 选择二级缓存来解决循环依赖的话，那么就意味着所有 Bean 都需要在实例化完成之后就立马为其创建代理，而 Spring 的设计原则是在 Bean 初始化完成之后才为其创建代理。所以，Spring 选择了三级缓存。但是因为循环依赖的出现，导致了 Spring 不得不提前去创建代理，因为如果不提前创建代理对象，那么注入的就是原始对象，这样就会产生错误。


Prototype（原型）对象和单例对象的区别：
- 单例对象在Spring加载的时候就创建，创建完毕后放入一级缓存中
- 而原型对象在每次在需要的时候都会创建，并且也不会存入缓存中
- 单例有状态（有状态指有成员变量并会进行修改操作）Bean对象的劣势：线程不安全。解决方案：考虑ThreadLocal（使用完后记得调用 ThreadLocal 的 remove 方法）或者锁机制。

为什么Springboot默认创建单例Bean：
- 不需要多次创建实例
- 减少垃圾回收
- 缓存中可以快速获得


## 拦截器与过滤器

### 不同点
1. 过滤器基于函数回调，拦截器基于 Java 的反射机制
2. 过滤器依赖于 servlet 容器，拦截器是 spring 组件，并不依赖于 servlet，因此先执行过滤器，再执行拦截器
3. 过滤器几乎可以对所有进行容器的请求起作用，拦截器只会对 controller 中的请求起作用

### 使用场景
1. 过滤器：登陆校验、权限检查、性能监控
2. 拦截器：接口限流


[容器注册组件的四种方式](https://juejin.cn/post/6989944803874062366)：
1. @Configuration + @Bean，注意，使用这种注册方法 Spring 会通过 CGLIB 来增强这个类，会判断是否实例已经存在，多次调用只会生成一个实例，意味着在@Configuration中的@Bean生成的是单例；相比之下，在@Component类中使用@Bean注解时，并不会有这种增强的行为。也就是说，当你在@Component类中调用一个@Bean方法时，实际上就是直接调用这个方法，并且每次调用都会创建一个新的 Bean，也就是说是生成多实例。
2. @ComponentScan + @Component
3. @Import
4. 新建一个实现FactoryBean接口的类

[面向切面编程（AOP）](https://pdai.tech/md/spring/spring-x-framework-aop.html)也是一种设计思想，本质是为了解耦。其实现方式是动态织入，在运行时动态将要增强的代码织入到目标类中，借助动态代理技术完成的。并且接口与非接口的动态代理机制并不相同，如下所示：
- 接口使用**JDK代理**，通过反射类Proxy以及拦截器的InvocationHandler回调接口实现的，使用反射，效率会低，不提供子类代理。
- 非接口使用**CGlib代理**，没有接口，只有实现类，采用底层字节码增强技术ASM在运行时使用Enhancer类创建目标的子类，提供子类代理。


执行顺序：
1. doAround(ProceedingJoinPoint pjp)的 pjp.proceed()**前**的内容
2. doBefore()
3. doAfterReturning(String result) / doAfterThrowing(Exception e)
4. doAfter()
5. doAround(ProceedingJoinPoint pjp)的 pjp.proceed()**后**的内容（当且仅当非异常情况下才会执行）




注解：

- @Component修饰的类为组件类并为这个对象创建Bean，@Bean修饰的方法会生成一个对象Bean。

- @SpringBootApplication 包含三个注解：
  1. @SpringBootConfiguration, 继承@Configuration，标注当前类是配置类，并会将当前类内声明的一个或多个以@Bean注解标记的方法的实例注册到spring容器中，并且实例名就是方法名。
  2. [@EnableAutoConfiguration](https://www.cnblogs.com/kevin-yuan/p/13583269.html)，主要由 @AutoConfigurationPackage，@Import(EnableAutoConfigurationImportSelector.class)这两个注解组成的。
     1. @AutoConfigurationPackage 内部是 @({Registrar.class})，用于将启动类所在的包里面的所有组件注册到spring容器。扫描 @Entity, @Mapper 等第三方依赖注解。
     2. @Import(EnableAutoConfigurationImportSelector.class) 是将特定路径（META-INF/spring.factories）中所有符合自动配置条件（@ConditionalOnClass）的类加载到Ioc容器，例如mybatis-spring-boot-starter。`AutoConfigurationImportSelector.java` 中可以看到所有自动配置类的名称。[自动配置类原理](https://juejin.cn/post/7101477895331135495)
  3. @ComponentScan，自动扫描并加载被@Component或@Repository修饰的组件，最终将这些组件加载到容器中，默认路径是该注解所在类的package。


## 事务

事务的几个参数：rollbackFor，propagation，isolation

### 传播机制
共有七种传播机制，大致上分为三类：
1. 支持当前事务
   1. REQUIRED：如果存在当前事务，加入该事务，否则创建新事务执行
   2. SUPPORTS：如果存在当前事务，加入该事务，否则以非事务的方式执行
   3. MANDATORY：如果存在当前事务，加入该事务，否则抛出异常

2. 不支持当前事务
   1. REQUIRES_NEW：如果存在当前事务，暂时挂起该事务，并且创建新事物执行。**内层事务与外层事务就像两个独立的事务一样，一旦内层事务进行了提交后，外层事务不能对其进行回滚，两个事务互不影响。**
   2. NOT_SUPPORTED：如果存在当前事务，暂时挂起该事务，否则以非事务的方式执行
   3. NEVER：如果存在当前事务，抛出异常，否则以非事务的方式运行

3. 嵌套事务（NESTED）：如果存在当前事务，则创建新事物作为当前事务的嵌套事务执行，否则等价于REQUIRED。**外层事务的回滚可以引起内层事务的回滚。而内层事务的异常并不会导致外层事务的回滚。**

![](https://raw.githubusercontent.com/CompetitiveLin/ImageHostingService/picgo/imgs/202411111929924.png)

### 事务失效

1. 数据库引擎不支持（MyIsam）
2. 没有注册成Bean被Spring管理（没有使用@Service等注解）
3. 方法不是public或者被final修饰（无法生成代理类）
4. [方法内部调用](https://juejin.cn/post/6844903908892999687)(解决方法是引入自身Bean)
5. 异常被捕捉或抛出其他类型的异常（回滚的默认异常为RuntimeException）
6. 未开启事务

### 方法调用的事务回滚情况

前提：a方法调用b方法

|a|b|报错情况|同类调用的现象|不同类调用的现象|
|-|-|-|-|-|
|transactional注解|无注解|a方法报错，b方法不报错|均回滚|均回滚|
|transactional注解|无注解|a方法不报错，b方法报错|均回滚|均回滚|
|无注解|transactional注解|a方法报错，b方法不报错|均不回滚|均不回滚|
|无注解|transactional注解|a方法不报错，b方法报错|均不回滚|a不回滚，b回滚|

结论：
1. transactional注解修饰的方法会创建一个代理增强类，其他方法调用该注解修饰的方法也只是调用原类中的方法。
2. transactional修饰的方法内报错就一定会回滚。

# Spring 用到了哪些设计模式
1. 工厂模式：通过 BeanFactory 和 ApplicationContext 容器创建 Bean 对象
2. 代理模式：AOP 的实现
3. 单例模式：Bean 默认是单例
4. 模板方法：jdbcTemplate 等用到了模板方法
5. 观察者模式：Springboot 事件驱动，监听器等