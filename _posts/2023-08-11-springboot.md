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



注解：

- @Component修饰的类为组件类并为这个对象创建Bean，@Bean修饰的方法会生成一个对象Bean。

- @SpringBootApplication 包含三个注解：
  1. @SpringBootConfiguration, 继承@Configuration，标注当前类是配置类，并会将当前类内声明的一个或多个以@Bean注解标记的方法的实例注册到spring容器中，并且实例名就是方法名。
  2. @EnableAutoConfiguration，借助@Import的帮助，将特定路径中所有符合自动配置条件的bean定义加载到Ioc容器。
  3. @ComponentScan，自动扫描并加载被@Component或@Repository修饰的组件，最终将这些组件加载到容器中，默认路径是该注解所在类的package。