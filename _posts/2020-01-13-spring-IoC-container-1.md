---
layout: post
title:  "IoC 컨테이너 1부 : 스프링 IoC 컨테이너와 빈"
date:   2020-01-13
excerpt: "IoC 컨테이너 1부 : 스프링 IoC 컨테이너와 빈"
tag:
- Spring
- 백기선 강의
- 스프링 프레임워크 핵심 기술
comments: true
---

# IoC (Inversion of control)

클래스 안에서 인스턴스를 직접 만드는 것이 아니라 어떠한 장치를 사용해서( ex) 생성자 ) 주입을 받아 사용하는 방법

아래 코드 같이 직접 인스턴스를 만들어 주입하여 사용할 수 있지만 Spring이 제공하는 IoC 컨테이너를 사용하는 이유는 
여러 개발자들이 Spring 커뮤니티에서 논의해서 만들어낸 여러가지 DI(Dependency Injection) 방법과 Best practice 또 노하우가 쌓여있는 Framework이기 때문이다.
```java
public class BookServiceTest {
  @Test
  public void test() {
    BookRepository bookRepository = new BookRepository();
    BookService bookService = new bookService(bookRepositry);
  }
}
```

# Bean
- Spring IoC 컨테이너가 관리하는 객체
- Scope
  - Singleton : 하나의 객체를 사용
  - Prototype : 매번 다른 객체를 사용
- 라이프사이클 인터페이스가 제공된다.
  - Spring IoC 컨테이너에 등록된 Bean에만 국한된 내용
  - ex) Bean이 만들어졌을 때 추가적인 작업을 하고싶다.
  ```java
    // Class 내에 @PostConstruct 이용
    
    @PostConstruct
    public void postConstruct() {
      // 
    }
    ```
    
# Spring IoC Container
- [BeanFactory](https://docs.spring.io/spring-framework/docs/5.0.8.RELEASE/javadoc-api/org/springframework/beans/factory/BeanFactory.html)
- 애플리케이션 컴포넌트의 중앙 저장소
- 빈 설정 소스로부터 빈 정의를 읽어들이고, 빈을 구성하고 제공

오늘 날에는 Annotation을 사용하여 POJO 객체를 Bean으로 등록하고 Bean으로 등록된 객체를 손쉽게 주입 받아서 사용한다.


# BeanFactory (Interface)
- IoC의 가장 핵심적인 Class
- [ApplicationContext](https://docs.spring.io/spring-framework/docs/5.0.8.RELEASE/javadoc-api/org/springframework/context/ApplicationContext.html)
  - 실질적으로 가장 많이 사용할 BeanFactory
  - BeanFactory의 IoC 컨테이너 기능외에도 다음과 같은 기능들을 가진다.
    - MessageSource(메세지 소스 처리 기능(i18n))
    - ApplicationEventPublisher(이벤트 발행 기능)
    - ResourceLoader (리소스 로딩 기능)
    - ...
