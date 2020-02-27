---
layout: post 
title: "데이터 바인더: @InitBinder"
date: 2020-03-04
excerpt: "백기선 강의 - 스프링 웹 MVC"
tags: 
- Spring
- 백기선 강의
- 스프링 웹 MVC
comments: true 
---

>백기선님의 스프링 웹 MVC 강의 내용을 정리한 내용입니다.
---

**@InitBinder**

특정 컨트롤러에서 바인딩 또는 검증 설정을 변경하고 싶을 때 사용한다.

모든 요청 전에 @InitBInder Annotation이 부여된 Method가 실행된다.



**바인딩 설정**

* webDataBinder.setDisallowedFields();



```java
@Controller
public class EventController {
  
  @InitBinder
  public void initEventBinder(WebDataBinder webDataBinder) {
    webDataBinder.setDisallowedFileds("id"); 
    // 받고 싶지 않은 field 설정 (blacklist)
    // 해당 field를 입력 받더라도 바인딩하지 않는다.
    // setAllowedFields (whitelist)
  }
}
```



**포메터 설정**

* webDataBinder.addCustomFormatter();
  
  


```java
public class Event {
  
  @DateTimeFormat(iso = DateTimeFormat.iso.DATE)
  private LocalDate startDate;
  // 이러한 변환이 이루어지는 이유는 해당 역할 할 수 있는 formatter 기본적으로 들어있기 때문이다.
  
  public LocalDate getStartDate() {
    return startDate;
  }
  
  public void setStartDate(LocalDate startDate) {
    this.startDate = startDate;
  }
}
```



**Validator 설정**

* webDataBinder.addValidators();
  
  


```java
public class EventValidator implements Validator {
  
  @Override
  public boolean support(Class<?> clazz) {
    return Event.class.isAssignableForm(clazz);
  }
  
  @Override
  public void validate(Object target, Errors errors) {
    Event event = (Event)target;
    if(event.getName().equalsIgnoreCase("aaa")) {
      errors.rejectValue("name", "wrongValue", "the value is not allowed");
    }
  }
}
```

```java
@Controller
public class EventController {
  
  @InitBinder
  public void initEventBinder(WebDataBinder webDataBinder) {
    webDataBinder.addValidators(new EventValidator());
  }
}
```



Validator 자체를 Bean으로 등록하고 주입을 받아서 사용할 수 있다.

```java
@Component
public class EventValidator {
  
  @Override
  public void validate(Object target, Errors errors) {
    if(event.getName().equalsIgnoreCase("aaa")) {
      errors.rejectValue("name", "wrongValue", "the value is not allowed");
    }
  }
}
```

```java
@Controller
public class EventController {
  
  @Autowired
  EventValidator eventValidator;
  
  @PostMapping("/events/form/name")
  public String eventsFormNameSubmit(@Validated @ModelAttribute Event event,
                                    	BindingResult bindingResult) {
    if(bindingResult.hasErrors()) {
      return "/events/form-name";
    }
    
    eventValidator.validate(event, bindingResult);
    // Errors는 BindingResult의 상위 타입이므로 인자로 넘길 수 있다.
    
    return "redirect:/events/form/limit";
  }
}
```



**특정 모델 객체에만 바인딩 또는 Validator 설정을 적용하고 싶은 경우**

* @InitBinder("event");



**참고**

* [https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-initbinder](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-initbinder)
* [https://github.com/spring-projects/spring-petclinic/blob/master/src/main/java/org/springframework/samples/petclinic/owner/PetController.java](https://github.com/spring-projects/spring-petclinic/blob/master/src/main/java/org/springframework/samples/petclinic/owner/PetController.java)

