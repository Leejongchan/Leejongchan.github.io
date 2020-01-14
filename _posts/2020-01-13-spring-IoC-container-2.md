---
layout: post
title:  "IoC 컨테이너 2부 : ApplicationContext와 다양한 빈 설정 방법"
date:   2020-01-13
excerpt: "IoC 컨테이너 2부 : ApplicationContext와 다양한 빈 설정 방법"
tag:
- Spring
- 백기선 강의
- 스프링 프레임워크 핵심 기술
comments: true
---

Spring IoC 컨테이너는 Bean 설정이 있어야 한다.

# XML File에 Bean 선언
```bash
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="bookService"
          class="me.jongchan.springstudy.BookService">
            <property name="bookRepository" ref="bookRepository"/>
            // name은 setter에서 refs는 XML file에서 가져온다.
    </bean>

    <bean id="bookRepository"
          class="me.jongchan.springstudy.BookRepository"/>

</beans>
```
- 하나하나 Bean으로 XML 파일에 등록하는 것이 번거롭다.
- ClassPathXmlApplicationContext class를 이용하여 Bean 사용 가능
```java
Application context = new ClassPathXmlApplicationContext("file.xml");
```

# Component-scan
  - XML File에 Component-scan을 선언하여 Component Annotation을 스캐닝해서 Bean을 등록한다. (Spring 2.5 version 이후)

# Java 설정 File
  - @Configuration을 붙여 Class File 생성하고 @Bean을 이용하여 Constructor 선언
  
  ```java
  @Configuration
  public class ApplicationConfig {
    
    @Bean
    public BookRepositry bookRespository() {
      return new BookRepository();
    }
    
    @Bean
    public BookService bookService() {
      BookService bookService = new BookService();
      bookService.setBookRepositry(bookRepository());
      // Parameter로 주입받아서 사용도 가능
      // bookRepositry를 이 곳에서 의존 관계를 엮지 않고 BookService classd에서 @Autowired를 사용하여 주입도 가능
      return bookService;
    }
    
  }
  ```
  - AnnotionConfigApplicationContext class를 이용하여 Bean 사용 가능
  ```java
  Application context = new AnnotionConfigApplicationContext(ApplicationConfig.class)
  ```

  - 결국 Java 설정도 Bean을 일일이 등록해야한다.
  
# @ComponentScan

```java
@Configuration
@ComponentScan(basePackageClasses = DemoApplication.class)
// class가 위치한 곳부터 ComponentScan을 해라
// basePackage로 class path를 써서 할 수 있지만 basePackageClasses가 더 type-safe하다. class 이름이 좋으면 자동완성도 가능.
public class ApplicationConfig {
      
}
```

# @SpringBootApplication (SpringBoot)
- 이미 Configuration과 ComponentScan이 들어있어 이 자체가 Bean 설정 File이다.
```java
@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```
