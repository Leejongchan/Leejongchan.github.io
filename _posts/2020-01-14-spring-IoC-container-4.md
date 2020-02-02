---
layout: post
title:  "IoC 컨테이너 4부: @Component와 컴포넌트 스캔"
date:   2020-01-14
excerpt: "백기선 강의 - 스프링 프레임워크 핵심 기술"
tag:
- Spring
- 백기선 강의
- 스프링 프레임워크 핵심 기술
comments: true
---

>백기선님의 스프링 프레임워크 핵심 기술 강의 내용을 정리한 내용입니다.
---


# @ComponentScan
@ComponentScan이 있는 Class부터 해당 패키지 이하의 모든 클래스를 Scan한다. 다른 패키지의 Class는 Scan하지 않는다.
```java
@SpringBootApplication
public class Demospring51Application {
  
  @Autowired
  MyService myService; // 다른 Package에 Class가 있는 경우 Bean을 찾을 수 없다.
  
  public static void main(String[] args) {
    SpringApplication.run(Demospring51Application.class, args);
  }
}
```

Filter Option을 이용하여 어떤 Annotation을 스캔 할지 또는 하지 않을지 결정할 수 있다.


# Function을 사용한 빈 등록
```java
class Main {
    public static void main(String[] args) {
      new SpringApplicationBuilder()
        .source(Demospring51Application.class)
        .initializers((ApplicationContextInitializer<GenericApplicationContext>) applicationContext -> {
          applicationContext.registerBean(MyBean.class);
          // 다른 Package의 Class를 Beand으로 등록 할 수 있다.
        })
        .run(args);
    }    
}
```

# 동작 원리
[BeanFactoryPostProcessor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/config/BeanFactoryPostProcessor.html)를 구현한 [ConfigurationClassPostProcessor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/ConfigurationClassPostProcessor.html)에 의해 처리된다.

BeanFactoryPostProcessor이 다른 모든 Bean들을 만들기(등록) 전에 ComponentScan을 해서 Bean으로 등록해 준다. 여기서 다른 모든 Bean은 @Bean 또는 우리가 직접 등록해주는 Bean을 말한다.
