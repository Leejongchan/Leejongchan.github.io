---
layout: post 
title: "Validation 추상화"
date: 2020-01-20
excerpt: ""
tags: 
- Spring
- 백기선 강의
- 스프링 프레임워크 핵심 기술
comments: true 
---

>백기선님의 스프링 프레임워크 핵심 기술 강의 내용을 정리한 내용입니다.
---

Application에서 사용하는 객체들을 검증할 때 사용하는 Interface [Validator](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/validation/Validator.html)를 제공한다.

주로 Spring MVC에서 사용하지만 Web 계층 전용 Validator는 아니다.


Validation은 DataBinder에 들어가 바인딩 할 때 같이 사용되기도 한다. 


Validator Interface를 구현하는 Class는 두 가지 Method를 구현해야 한다.

1. support
   * 검증해야하는 Instance의 class가 Validator가 검증할 수 있는 Class인지 검사
2. Validate
   * 실질적으로 검증 작업이 이루어지는 Method

```java
public class Event {
  
  Integer id;
  
  String title;
  
  public Integer getId() {
    return id;
  }
  
  public void setId(Integer id) {
    this.id = id;
  }
  
  public String getTitle() {
    return title;
  }
  
  public void setTitle(String title) {
    this.title = title;
  }
}
```

```java
public class EventValidator implements Validator {
  @Override
  public boolean support(Class<?> clazz) {
    return Event.class.euqals(clazz);
    // Event Class Type의 Instance를 검증할 것이기 때문에 Parameter로 전달되는 class가 Event인지 확인
  }
  
  @Override
  public void validate(Object target, Errors error) {
    ValidationUtils.rejectIfEmptyOrWhitespace(errors, "title", "notempty", "Empty type is now allowed");
    // Field가 Empty 거나 WhiteSpace일 경우 errors에 정보를 담는다.
    // title : 필드명
    // ㅇnoteempty : 에러코드
    // Empty type is not allowed : 디폴트 메세지
    
    // ValidationUtils 뿐만 아니라 아래와 같이 조건을 이용하여 error를 처리할 수 있다.
    Event event = (Event)target;
    if(event.getTitle() == null) {
      // 상황에 따라 각각의 파라미터를 채워 Error 처리
      // errors.reject();
      //
      // 특정한 Field 값에 해당하는 Error를 처리하는 경우 rejectValue Method를 사용
      // errors.rejectValue();
     
    }
  }
}
```

```java
@Component
public class AppRunner implements ApplicationRunner {
  
  @Override
  public void run(ApplicationArguments args) throws Exception {
    Event event = new Event();
    EventValidator eventValidator = new EventValidator();
    Errors errors = new BeanPropertyBindingResult(event, "event");
    // 어떤 객체를 검사할 것 인지, 이름을 매개변수로 전달
    // BeanPropertyBindingResult은 Spring MVC에서 자동으로 파라미터로 생성해서 전달해주기 때문에 실질적으로 이 Class를 사용할 일은 없다.
    
    eventValidator.validate(event, errors);
    
    System.out.println(errors.hasErrors()); // true
    
    errors.getAllErrors().forEach(e -> {
      System.out.println("===== error code =====");
      Arrays.stream(e.getCodes()).forEach(System.out::println);
      // notempty.event.title
      // notempty.java.lang.String
      // notempty
      System.out.println(e.getDefaultMessage());
      // Empty title is now allowed
    });
  }
}
```



최근에는 위와 같이 Validator를 직접 사용하는 것이 아니라 특히 SpringBoot를 사용한다면 Spring이 제공해주는 [LocalValidatorFactoryBean](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/validation/beanvalidation/LocalValidatorFactoryBean.html)을 자동으로 Bean으로 등록한다. (SpringBoot 2.0.5 이상 버전)



LocalValidatorFactoryBean은 Validation의 구현체 중 하나로 Bean Validation Annotation을 지원하는 Validator이다. JSR-303(Bean Validation 1.0)과 JSR-349(Bean Validation 1.1)을 지원한다.

```java
public class Event {
  
  Integer id;
  
  @NotEmpty
  String title;
 
  @NotNull @Min(0) // 최소한 0 이상
  Intenger limit;
  
  @Email
  String email;
  
  public Integer getId() {
    return id;
  }
  
  public void setId(Integer id) {
    this.id = id;
  }
  
  public String getTitle() {
    return title;
  }
  
  public void setTitle(String title) {
    this.title = title;
  }
  
  public Integer getLimit() {
    return limit;
  }
  
  public void setLimit(Integer limit) {
    this.limit = limit;
  }
  
  public void getEmail() {
    return email;
  }
  
  public void setEmail(String email) {
    this.email = email;
  }
}
```

```java
@Component
public class AppRunner implements ApplicationRunner {
  
  @Autowired
  Validator validator;
  
  @Override
  public void run(ApplicationArguments args) throws Exception {
    System.out.println(validator.getClass());
    
    Event event = new Event();
    events.setLimit(-1);
    event.setEmail("aaa2");
    
    EventValidator eventValidator = new EventValidator();
    Errors errors = new BeanPropertyBindingResult(event, "event");
    
    eventValidator.validate(event, errors);
    
    System.out.println(errors.hasErrors()); // true
    
    errors.getAllErrors().forEach(e -> {
      System.out.println("===== error code =====");
      Arrays.stream(e.getCodes()).forEach(System.out::println);
      System.out.println(e.getDefaultMessage());
    });
  }
}
```


JSR-380(Bean Validation 2.0.1) 구현체로는 [hibernate-validator]([https://beanvalidation.org](https://beanvalidation.org/))를 사용했다.

