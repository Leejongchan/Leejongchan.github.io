---
layout: post 
title: "IoC 컨테이너 6부: Environment 2부. 프로퍼티"
date: 2020-01-18
excerpt: ""
tags: 
- Spring
- 백기선 강의
- 스프링 프레임워크 핵심 기술
comments: true 
---

Property는 Application에 등록되는 여러가지 Key-value 쌍으로 제공되는 Property에 접근 할 수 있는 기능이다.

다양한 방법으로 값을 설정할 수 있으며 Environment의 역할은 Property 소스 설정 및 값 을 가져오는 것이다.

```java
@Component
public class AppRunner implements ApplicationRunner {
  
	@Autowired
  ApplicationContext ctx;
  
  @Autowired
  BookRepository bookRepository;
  
  @Override
  public void run(ApplicationArguments args) throws Exception {
    Environment environment = ctx.getEnvironment();
    System.out.println(environment.getProperty("app.name"));
  }
}
```


Property에는 우선순위가 있다.

* StandardServletEnvironment의 우선순의
  1. ServletConfig 매개변수
  2. ServletContext 매개변수
  3. JNDI (java:comp/env/)
  4. JVM 시스템 프로퍼티 (-Dkey="value")
  5. JVM 시스템 환경 변수 (운영 체제 환경 변수)


# @PropertySource

Environment를 통해 Property를 추가하는 방법이다.

```java
@SpringBootApplication
@PropertySource("classpath:/app.properties")
public class Demospring51Application {
  
  public static void main(String[] args) {
    SpringApplication.run(Demospring51Application.class, args);
  }
}
```

@PropertySources는 JVM 시스템 프로퍼티보다 우선순위가 낮다.


다양한 Properties File과 Hierarchical를 좀 더 쉽게 쓸 수 있도록 Spring Boot가 지원해주는 기능도 있다.



