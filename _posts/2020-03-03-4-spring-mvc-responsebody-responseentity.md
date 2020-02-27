---
layout: post 
title: "핸들러 메소드 16부: @ResponseBody & ResponseEntity"
date: 2020-03-03
excerpt: "백기선 강의 - 스프링 웹 MVC"
tags: 
- Spring
- 백기선 강의
- 스프링 웹 MVC
comments: true 
---

>백기선님의 스프링 웹 MVC 강의 내용을 정리한 내용입니다.
---

**@ResponseBody**

* 메소드에서 Return하는 데이터를 Http 응답 본문에 담아준다.
  * HttpMessageConverter를 사용해 Return하는 값을 응답 본문에 있는 메세지로 변환한다.
* @RestController 사용시 자동으로 모든 핸들러 메소드에 적용된다.
  * @ResponseBody Annotation을 Meta Annotation으로 사용하기 때문



**ResponseEntity**

응답 헤더 상태 코드 본문을 직접 다루고 싶은 경우에 사용한다.



```java
@Controller
@RequestMapping("/api/events")
public class EventApi {
  
  @PostMapping
  public ResponseEntity<Event> createEvent(@RequestBody @Vaild Event event, BindingResult bindingResult) {
    if(bindingResult.hasErrors()) {
      return ResponseEntity.badRequest().build();
    }
    
    return ResponseEntity.ok(event);
    // 다음과 같이 세밀한 응답도 가능
    // return new ResponseEntity<Event>(event, HttpStatus.CREATED);
  }
}
```







**참고**

* https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-responsebody
* https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-responseentity
