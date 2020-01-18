---
layout: post 
title: "IoC 컨테이너 8부: ApplicationEventPublisher"
date: 2020-01-18
excerpt: ""
tags: 
- Spring
- 백기선 강의
- 스프링 프레임워크 핵심 기술
comments: true
---

[ApplicationEventPublisher](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ApplicationEventPublisher.html)는 이벤트 프로그래밍에 필요한 인터페이스를 제공하며 옵저버 패턴의 구현체이다. ApplicationContext는 ApplicationEventPublisher를 상속한다.


# Event 만들기

```java
// Spring 4.2이전에는 항상 ApplicationEvent Class를 상속받아야 한다.
public class MyEvent extends ApplicationEvent {
  // MyEvent는 Bean으로 등록되는 것이 아니라 원하는 데이터를 담아 전송 할 수 있는 Event가 될 수 있다.
  
  private int data;
  
  public MyEvent(Object source) {
    super(source);
  }
  
  public MyEvent(Object source, int data) {
    super(source);
    this.data = data;
  }
  
  public int getData() {
    return data;
  }
}
```



# Event 발생 시키는 방법

ApplicationEventPublisher.publishEvent() Method를 이용한다.

```java
@Component
public class AppRunner implements ApplicationRuneer {
  
  @Autowired
  ApplicationEventPublisher publishEvent;
  
  @Override
  public void run(ApplicationArguments args) throws Exception {
    publicEvent.publicEvent(new MyEvent(this, 100));
  }
}
```



# Event 처리하는 방법

ApplicationListener<이벤트>를 구현한 클래스를 만들어 빈으로 등록한다. 

Spring 4.2 부터는 @EventListener를 사용하여 빈의 메소드에서 사용할 수 있다.

```java
// Event Handler는 Bean으로 등록되야 한다.
// 4.2 이전에는 특정한 Interface를 구현했어야 한다.
@Component
public class MyEventHandler implements ApplicationListener<MyEvent> {
  
  @Override
  public void onApplicationEvent(MyEvent event) {
    System.out.println("이벤트 받았다. 데이터는 " + event.getData());
  }
}
```

SpringBoot App이 구동되고 AppRunner가 실행되면서 Event를 발생시키고 등록되어 있는 Bean 중에서 MyEventHandler가 Event를 받아서 실행한다. 이 것이 Event 기반 프로그래밍이다. 



**4.2 이상에서는 이러한 제약사항이 사라지고 Event가 ApplicationEvent를 상속받을 필요가 없어졌다.**

```java
public class MyEvent {
  
  private int data;
  
  private Object source;
  
  public MyEvent(Object source, int data) {
    this.source = source
    this.data = data;
  }
  
  public Object getSource() {
    return source;
  }
  
  public int getData() {
    return data;
  }
}
```

위와 같은 코드가 SpringFramework가 추구하는 철학이다. 비침투성으로 이 코드에는 Spring Package가 전혀 들어와 있지 않아 가장 깔끔한 POJO이다. 테스트하기가 용이하고 유지보수가 편해진다는 장점이 있다.



EventHandler도 특정한 Interface를 구현할 필요가 없다.

```java
// Event Handler는 Bean으로 등록되야 한다.
@Component
public class MyEventHandler {
  
  // EventListener Annotation을 추가하고 Method 명은 개발자가 명명할 수 있다.
  @EventListener
  public void handle(MyEvent event) {
    System.out.println(Thread.currentThread().toString());
    System.out.println("이벤트 받았다. 데이터는 " + event.getData());
  }
}
```



EventHandler가 여러개가 있는 경우 같은 쓰레드에서 순차적으로 실행된다.

```java
@Component
public class AnotherHandler {
  
  @EventListener
  public void handle(MyEvent event) {
    System.out.println(Thread.currentThread().toString());
    System.out.println("이벤트 받았다. 데이터는 " + event.getData());
  }
}
```



실행되는 순서는 보장되지 않는데 @Order를 이용하여 순서를 정해줄 수 있다

```java
@Component
public class MyEventHandler {
  
  @EventListener
  @Order(Ordered.HIGHEST_PRECEDENCE)
  public void handle(MyEvent event) {
    System.out.println(Thread.currentThread().toString());
    System.out.println("이벤트 받았다. 데이터는 " + event.getData());
  }
}
```

```java
@Component
public class AnotherHandler {
  
  @EventListener
  @Order(Ordered.HIGHEST_PRECEDENCE + 2)
  public void handle(MyEvent event) {
    System.out.println(Thread.currentThread().toString());
    System.out.println("이벤트 받았다. 데이터는 " + event.getData());
  }
}
```



@Async를 이용하여 비동기로 실행 할 수 있다. 비동기적으로 실행하는 경우에는 각각의 쓰레드풀에서 따로 돌고 쓰레드 스케줄링에 따라 순서가 달라지기 때문에 @Order는 더 이상 의미가 없다.

```java
@SpringBootApplication
@EnableAsync
public class Demospring51Application {
  
  public static void main(String[] args) {
    SpirngApplication.run(Demospring51Application.class, args);
  }
}
```

```java
@Component
public class MyEventHandler {
  
  @EventListener
  @Async
  public void handle(MyEvent event) {
    System.out.println(Thread.currentThread().toString());
    System.out.println("이벤트 받았다. 데이터는 " + event.getData());
  }
}
```

```java
@Component
public class AnotherHandler {
  
  @EventListener
  @Async
  public void handle(MyEvent event) {
    System.out.println(Thread.currentThread().toString());
    System.out.println("이벤트 받았다. 데이터는 " + event.getData());
  }
}
```



# 스프링이 제공하는 기본 이벤트

스프링은 다음과 같은 이벤트를 제공한다.

1. ContextRefreshedEvent
   * ApplicationContext를 초기화 했거나 Refresh했을 때 발생
2. ContextStartedEvent
   * ApplicationContext를 start()하여 라이프사이클 빈들이 시작신호를 받은 시점에 발생
3. ContextStoppedEvent
   * ApplicationContext를 stop()하여 라이프사이클 빈들이 정지신호를 받은 시점에 발생
4. ContextClosedEvent
   * ApplicationContext를 close()하여 싱글톤 빈 소멸되는 시점에 발생
5. RequestHandledEvent
   * HTTP 요청을 처리했을 때 발생

```java
@Component
public class MyEventHandler {
  
  @EventListener
  @Async
  public void handle(MyEvent event) {
    System.out.println(Thread.currentThread().toString());
    System.out.println("이벤트 받았다. 데이터는 " + event.getData());
  }
  
  @EventListener
  @Async
  public void handle(ContextRefreshedEvent event) {
    System.out.println(Thread.currentThread().toString());
    System.out.println("ContextRefreshedEvent");
  }
  
  @EventListener
  @Async
  public void handle(ContextClosedEvent event) {
    System.out.println(Thread.currentThread().toString());
    System.out.println("ContextClosedEvent");
  }
}
```

