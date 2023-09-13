---
title: SpringBoot
categories: []
tags: [backend]
date: 2023-08-10T15:08:32+800
last_modified_at: 
pin: false
---

Spring核心之控制反转（IOC）和依赖注入（DI）：
- **IOC是一种设计思想**，将设计好的Bean对象交给容器控制，而不是在传统的在对象内部直接控制。用@Configutation + @Bean的方式。用通俗的话解释就是在容器里创建Bean对象，在需要的时候取出使用即可。
- **DI是一种实现方式**，将应用程序依赖的对象注入到容器中。


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



[三级缓存解决循环依赖](https://developer.aliyun.com/article/766880)：
- 前提，出现循环依赖的Bean必须是单例；依赖注入的方式不能全是构造器注入（全是构造器注入则无法解决循环依赖）。
- getSingleton(beanName)方法三级缓存
  1. singletonObjects，一级缓存，存储的是所有创建好了的**单例Bean对象**
  2. earlySingletonObjects，二级缓存，完成实例化，但是还未进行属性注入及初始化的**提前暴露的对象**
  3. singletonFactories，三级缓存，存放**生产对象的工厂**，并且每次从这个工厂中拿到的对象都是不一样的，二级缓存中存储的就是从这个工厂中获取到的对象，如果Bean存在AOP的话，返回的是AOP的**代理对象**，提前进行了代理，避免对后面重复创建代理对象。

![](https://raw.githubusercontent.com/CompetitiveLin/ImageHostingService/picgo/imgs/202305231628567.png)


为什么需要二级缓存：
如果出现循环依赖+aop时，多个地方注入这个动态代理对象需要保证都是同一个对象。如果只使用这两层缓存，在使用三级缓存中的工厂对象生成的动态代理对象都是新创建的，循环依赖的时候，注入到别的bean里面去的那个动态代理对象和最终这个bean在初始化后自己创建的bean地址值不一样。如果 Spring 选择二级缓存来解决循环依赖的话，那么就意味着所有 Bean 都需要在实例化完成之后就立马为其创建代理，而 Spring 的设计原则是在 Bean 初始化完成之后才为其创建代理。所以，Spring 选择了三级缓存。但是因为循环依赖的出现，导致了 Spring 不得不提前去创建代理，因为如果不提前创建代理对象，那么注入的就是原始对象，这样就会产生错误。


Prototype（原型）对象和单例对象的区别：
- 单例对象在Spring加载的时候就创建，创建完毕后放入一级缓存中
- 而原型对象在每次在需要的时候都会创建，并且也不会存入缓存中
- 单例Bean对象的劣势：线程不安全。解决方案：考虑ThreadLocal

为什么Springboot默认创建单例Bean：
- 不需要多次创建实例
- 减少垃圾回收
- 缓存中可以快速获得


[容器注册组件的四种方式](https://juejin.cn/post/6989944803874062366)：
1. @Configuration + @Bean，注意，使用这种注册方法 Spring 会通过 CGLIB 来增强这个类，会判断是否实例已经存在，多次调用只会生成一个实例，意味着在@Configuration中的@Bean生成的是单例；相比之下，在@Component类中使用@Bean注解时，并不会有这种增强的行为。也就是说，当你在@Component类中调用一个@Bean方法时，实际上就是直接调用这个方法，并且每次调用都会创建一个新的 Bean，也就是说是生成多实例。
2. @ComponentScan + @Component
3. @Import
4. 新建一个实现FactoryBean接口的类

[面向切面编程（AOP）](https://pdai.tech/md/spring/spring-x-framework-aop.html)也是一种设计思想，本质是为了解耦。其实现方式是动态织入，在运行时动态将要增强的代码织入到目标类中，借助动态代理技术完成的。并且接口与非接口的动态代理机制并不相同，如下所示：
- 接口使用JDK代理
- 非接口使用CGlib代理

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
  2. [@EnableAutoConfiguration](https://www.cnblogs.com/kevin-yuan/p/13583269.html)，继承了 @Import，将特定路径（org.springframework.boot.autoconfigure.EnableAutoConfiguration）中所有符合自动配置条件（@Configuration）的类加载到Ioc容器。`AutoConfigurationImportSelector.java` 中可以看到所有自动配置类的名称。[自动配置类原理](https://juejin.cn/post/7101477895331135495)
  3. @ComponentScan，自动扫描并加载被@Component或@Repository修饰的组件，最终将这些组件加载到容器中，默认路径是该注解所在类的package。