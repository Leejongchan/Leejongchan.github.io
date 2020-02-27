---
layout: post 
title: "전역 컨트롤러: @(Rest)ControllerAdvice"
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

**@ControllerAdvice**

예외 처리, 바인딩 설정, 모델 객체를 모든 컨트롤러 전반에 걸쳐 적용하고 싶은 경우에 사용한다.

* @ExceptionHandler
* @InitBinder
* @ModelAttribute



```java
@ControllerAdvice
public class BaseController {
  
  @ExceptionHandler
  public String eventErrorHandler(EventException exception, Model model) {
    model.addAttribute("message", "event error");
  }
  
  @InitBinder("event")
  public void initEventBinder(WebDataBinder webDataBinder) {
    webDataBinder.setDisallowedFileds("id");
  }
  
  @ModelAttribute
  public void categories(Model model) {
    model.addAttribute("categories", List.of("study", "seminar", "hobby", "social"));
  }
}
```





**적용할 범위를 지정할 수도 있다.**

* 특정 Annotation을 가지고 있는 컨트롤러에만 적용하기

* 특정 패키지 이하의 컨트롤러에만 적용하기

* 특정 클래스 타입에만 적용하기

  ```java
  @ControllerAdvice(assignableTypes = {EventController.class, EventApi.class})
  public class BaseController {
    
    @ExceptionHandler
    public String eventErrorHandler(EventException exception, Model model) {
      model.addAttribute("message", "event error");
    }
    
    @InitBinder("event")
    public void initEventBinder(WebDataBinder webDataBinder) {
      webDataBinder.setDisallowedFileds("id");
    }
    
    @ModelAttribute
    public void categories(Model model) {
      model.addAttribute("categories", List.of("study", "seminar", "hobby", "social"));
    }
  }
  ```



**참고**

* [https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-controller-advice](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-controller-advice)

