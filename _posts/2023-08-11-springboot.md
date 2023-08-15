---
title: SpringBoot
categories: []
tags: [backend]
date: 2023-08-10T15:08:32+800
last_modified_at: 
pin: false
---


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