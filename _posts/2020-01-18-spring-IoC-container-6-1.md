---
layout: post
title: "IoC 컨테이너 6부: Environment 1부. 프로파일"
date: 2020-01-18
excerpt: ""
tags: 
- Spring
- 백기선 강의
- 스프링 프레임워크 핵심 기술
comments: true
---

**[Environment](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/env/Environment.html)**는 현재 구동중인 Application 환경을 나타내는 Interface로 Profile과 Property를 다루는 Interface이다.
Environment의 역할은 활성화할 Profile 확인 및 설정이다.


**[EnvironmentCapable](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/env/EnvironmentCapable.html)**은 Environment reference를 포함하고 노출하는 구성요소를 나타내는 Interface이다.
ApplicationContext가 EnvironmentCapable을 상속하고 있다.



# Profile

Profile은 빈들의 묶음이며 각각의 환경에 따라 다른 Bean을 사용해야 하는 경우, 특정한 환경에서만 어떠한 Bean을 등록해야하는 요구사항을 충족시키기 위해 Profile이라는 기능이 생겼다. 그리고 이 기능은 Spring ApplicationContext의 Environment라는 interface를 통해 사용 할 수 있다.



```java
@Component
public class AppRunner implements ApplicationRunner {
  
  @Autowired
  ApplicationContext ctx;
  
  @Override
  public void run(ApplicationArguments args) throws Exception {
    Environment environment = ctx.getEnvironment(); // Environment에 있는 Method
    System.out.println(Array.toString(environment.getActiveProfiles()));
    System.out.println(Array.toString(environment.getDefaultProfiles()));
  }
}
```



Profile Annotation은 다음과 같이 사용할 수 있다.

```java
@Configuration
@Profile("test")
// test라는 Profile로 Application을 실행하기 전까지는 Bean 설정이 적용되지 않는다.
public class TestConfiguration {
  
  @Bean
  // 아래와 같이 사용 가능하다.
  //@Profile("test")
  public BookRepository bookRepository() {
    return new TestBookRepository();
  }
}
```



Bean 설정 File에 하나하나 등록하는 것은 어렵기 때문에 다음과 같이 사용할 수 있다.

ComponentScan에 등록되는 Bean도 Profile을 사용할 수 있다.

```java
@Repository
@Profile("test")
public class TestBookRepository implements BookRepository {
  
}
```



### Profile 표현식
* ! (not)
* & (and)
* \| (or)



### Profile 지정 방법

1. Edit configuration - Active profiles - Profile명 입력
2. Edit configuration - VM options - -Dspring.profile.active="Test,A,B..." 입력
3. @ActiveProfiles
   - Test할 때 Profile을 지정한다.

