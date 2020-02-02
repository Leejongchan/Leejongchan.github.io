---
layout: post
title:  "IoC 컨테이너 3부: @Autowire"
date:   2020-01-13
excerpt: "백기선 강의 - 스프링 프레임워크 핵심 기술"
tag:
- Spring
- 백기선 강의
- 스프링 프레임워크 핵심 기술
comments: true
---

>백기선님의 스프링 프레임워크 핵심 기술 강의 내용을 정리한 내용입니다.
---


# Autowired를 이용한 의존성 주입
- Field Injection  

  ```java
  @Service
  public class BookService {
    
      @Autowired
      BookRepository bookRepository;
  }
  ```
  
  ```java
  @Repository
  public class bookRepositry {
  
  }
  ```

- Construct Injection
  ```java
  @Service
  public class BookService {
    
      BookRepository bookRepository;
    
      @Autowired
      public BookService(BookRepository bookRepository) {
          this.bookRepository = bookRepository;
      }
    
      @Autowired
      public void setBookRepository(BookRepository bookRepository) {
         this.bookRepository = bookRepository;
      }
  }
  ```
  ```java
  @Repository
  public class bookRepositry {
  
  }
  ```

- Setter Injection
  ```java
  @Service
  public class BookService {
    
    BookRepository bookRepository;
   
    @Autowired(required = false)
    // required의 default는 true이다. required = false option를 set한 경우 매칭되는 Bean이 없는 경우 에러가 아닌 의존성 주입을 하지 않는다.
    public void setBookRepository(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }
  
  }
  ```
  ```java
  // @Repository
  public class bookRepositry {
  
  }
  ```

# 여러 Class가 implements한 Interface를 의존성 주입할 경우
@Primary를 이용하여 어떠한 Class가 의존성 주입될지 결정한다.
```java
public interface BookRepository {

}
```
```java
@Repository @Primary
public class MyBookRepository {
  
}
```
```java
@Repository
public class YourBookRepository {
  
}
```
```java
@Service 
class BookService {
  
    @Autowired
    BookRepository bookRepository;
}
```

@Autowired를 선언하는 부분에 @Qualifier("Class name")을 이용하여 의존성 주입도 가능하지만 @Primary가 더 Type-safe하다.

List를 활용하여 여러개의 Bean을 모두 받을 수 있다.
```java
@Service class BookService {
  
  @Autowired
  List<BookRepository> bookRepositories;
}
```

# 동작 원리
[BeanPostProcessor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/config/BeanPostProcessor.html)라는 라이프사이클 인터페이스의 구현체에 의해 동작한다.
BeanFactory(ApplicationContext)가 자기안에 등록되어 있는 BeanPostProcessor Type의 Bean을 찾고 일반적인 Bean들에게 BeanPostProcessor를 적용한다. (Annotation을 처리하는 Logic을 Bean에게 적용하는 것) 

BeanPostProcessor는 Bean initialize(Bean의 Instance를 생성) 라이프사이클 이전, 이후에 부가적인 작업을 할 수 있는 라이프 사이클 콜백이다. 즉, 새로 만든 빈 인스턴스를 수정할 수 있는 라이프 사이클 인터페이스이다.

라이프 사이클 콜백의 예로 Bean이 만들어진 다음 수행되는 @PostConstructor가 있다.
```java
@Service
public class BookService {
  
  @Autowired
  BookRepository bookRepository;
 
  @PostConstuctor
  public void printBookRepository() {
      //
  }
}
```

@Autowired를 동작 원리의 예로 설명하자면 BeanFactory가 자기 안에 등록되어 있는 BeanPostProcessor Type의 Bean(AutowiredAnnotationBeanPostProcessor)을 찾고 @Autowired Annotation을 처리하는 Logic을 해당 Bean에게 적용하는 것이다.

[AutowiredAnnotationBeanPostProcessor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/annotation/AutowiredAnnotationBeanPostProcessor.html)는 BeanPostProcessor Interface를 구현한 Class로
 스프링이 제공하는 @Autowired와 @Value Annotation 그리고 JSR-330의 @Inject Annotation을 지원하는 Annotation 처리기이다.


