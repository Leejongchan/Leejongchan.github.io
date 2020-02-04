---
layout: post 
title: "스프링 AOP: @AOP"
date: 2020-02-04
excerpt: "백기선 강의 - 스프링 프레임워크 핵심 기술"
tags: 
- Spring
- 백기선 강의
- 스프링 프레임워크 핵심 기술
comments: true 
---

>백기선님의 스프링 프레임워크 핵심 기술 강의 내용을 정리한 내용입니다.
---

Spring Annotation 기반의 AOP를 시작하기 위해서는 의존성 추가를 해야한다.

```xml
<!-- pom.xml -->

<dependency>
  <groupId>org.spring.framwork.boot</groupId>
  <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```



@Aspect를 통해 Aspect를 정의하고 Bean으로 등록해야 하기 때문에 Component scan을 사용하는 경우 @Component를 추가한다.

```java
/**
 * 이 애노테이션을 사용하면 성능을 로깅해 줍니다.
 */
@Component
@Aspect
public class PerfAspect {
  
  @Around("execution(* me.whiteship..*.EventService.*(..))")
  // me.whiteship로 시작하는 Package 밑에 있는 모든 클래스 중에 EventService 안에있는 모든 메소드에 이 행위를 적용해라라는 의미
  public Object logPerf(ProceedingJoinPoint pjp) throws Throwable {
    // ProceedingJoinPoint는 Advice가 적용되는 대상
    long begin = System.currentTimeMillis();
    Ojbect retVal = pjp.proceed();
    System.out.println(System.currentTimeMillis() - begin);
    return retVal;
  }
}
```

```java
// Real Subject
@Service
public class SimpleEventService implements EventService {
  
  @Override
  public void createEvent() {
    long begin = System.currentTimeMillis();
    
    try {
      Thread.sleep(1000);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
    System.out.println("Created an event");
    
    System.out.println(System.currentTimeMillis() - begin);
  }
  
  @Override
  public void publicEvent() {
    long begin = System.currentTimeMillis();
    
    try {
      Thread.sleep(2000);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
    System.out.println("Published an event");
    
    System.out.println(System.currentTimeMillis() - begin);
  }
  
  @Override
  public void deleteEvent() {
    System.out.println("Delete an event");
  }
}
```



위 코드의 경우 시간을 측정하고 싶지 않은 deleteEvent 메소드까지 시간을 측정한다.

Annotation을 이용해서도 Advice 적용 대상을 지정할 수 있다.



```java
// Retentiond은 이 Annotation 정보를 얼마나 유지할 것인가를 의미한다.
// CLASS file까지 Annotation 정보를 유지하겠다. 컴파일 후에 바이트코드(.class) 안에 이 Annotation 정보가 남아있다.
@Retention(RetentionPolicy.CLASS) 
public @interface PerLogging {
  
}
```

Default는 CLASS이며 RententionPolicy.SOURCE로 설정한 경우 컴파일 이후에 Annotation 정보가 사라진다.



```java
@Document
@Target(ElementType.METHOD)
public @interface PerLogging {
  
}
```

```java
// Real Subject
@Service
public class SimpleEventService implements EventService {
  
  @PerLogging
  @Override
  public void createEvent() {
    long begin = System.currentTimeMillis();
    
    try {
      Thread.sleep(1000);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
    System.out.println("Created an event");
    
    System.out.println(System.currentTimeMillis() - begin);
  }
  
  @PerLogging
  @Override
  public void publicEvent() {
    long begin = System.currentTimeMillis();
    
    try {
      Thread.sleep(2000);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
    System.out.println("Published an event");
    
    System.out.println(System.currentTimeMillis() - begin);
  }
  
  @Override
  public void deleteEvent() {
    System.out.println("Delete an event");
  }
}
```

```java
@Component
@Aspect
public class PerfAspect {
  
  @Around("@annotation(PerLogging)") // PerLogging Annotation이 적용된 곳에만 적용
  // @Around("bean(simpleEventService)") // Bean의 모든 Method에 적용된다.
  public Object logPerf(ProceedingJoinPoint pjp) throws Throwable {
    long begin = System.currentTimeMillis();
    Ojbect retVal = pjp.proceed();
    System.out.println(System.currentTimeMillis() - begin);
    return retVal;
  }
  
  @Before("bean(simpleEventService)") // simpleEventService의 모든 Method 실행 전에 hello method가 실행된다.
  public void hello() {
    System.out.println("hello");
  }
}
```



Advice가 Joinpoint와 결합하여 동작하는 시점에 따라 다음과 같은 Type으로 구분된다.

1. @Before
2. @AfterReturning
3. @AfterThrowing
4. @Around

