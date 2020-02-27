---
layout: post 
title: "예외 처리 핸들러: @ExceptionHandler"
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

**@ExceptionHandler**

특정 예외가 발생한 요청을 처리하는 핸들러 정의



```java
public class EventException extends RuntimeException {
  
}
```

```java
@Controller
public class EventController {
  
  @ExceptionHandler
  public String eventErrorHandler(EventException exception, Model model) {
    // 사용할 수 있는 Argument는 글 하단 참고 링크에서 확인
    model.addAttribute("message", "event error");
    return "error"; // error.html return
  }
  
  @ExceptionHandler
  public String runtimeErrorHandler(RuntimeException exception Model model) {
    model.addAttribute("message", "runtime error");
    return "error";
  }
  
  @GetMapping("/events/form/name")
  public String eventsFormName(Model model) {
    throw new EventException();
  }
}
```

```java
@Controller
public class EventController {
  
  // 여러 Exception을 같이 처리할 경우
  @ExceptionHandler({EventException.class, RuntimeException.class})
  public String eventErrorHandler(RuntimeException exception, Model model) {
    // Exception Argument는 모두 처리할 수 있는 상위 타입의 Exception을 사용
    
    model.addAttribute("message", "runtime error");
    return "error";
  }
  
  @GetMapping("/events/form/name")
  public String eventsFormName(Model model) {
    throw new EventException();
  }
}
```



**REST API의 경우 응답 본문에 에러에 대한 정보를 담아주고, 상태 코드를 설정하려면 ResponseEntity를 주로 사용한다.**

```java
@Controller
@ReqeustMapping("/api/events")
public class EventApi{
  
  // 여러 Exception을 같이 처리할 경우
  @ExceptionHandler
  public ResponseEntity errorHandler() {
    return ResponseEntity.badRequest().body("can't create ...");
  }
  
  @PostMapping
  public ResponseEntity<Event> createEvent(@RequestBody @Valid Event event,
                                          BindingResult bindingResult) {
    if(bindingResult.hasErrors()) {
      return ResponseEntity.badRequest().build();
    }
    
    return ResponseEntity<Event>(event, HttpStatus.CREATED);
  }
}
```





**참고**

* https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-exceptionhandler

