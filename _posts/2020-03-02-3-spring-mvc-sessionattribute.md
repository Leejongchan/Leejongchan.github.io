---
layout: post 
title: "핸들러 메소드 10부: @SessionAttribute"
date: 2020-03-02
excerpt: "백기선 강의 - 스프링 웹 MVC"
tags: 
- Spring
- 백기선 강의
- 스프링 웹 MVC
comments: true 
---

>백기선님의 스프링 웹 MVC 강의 내용을 정리한 내용입니다.
---


**@SessionAttribute**

* HttpSession을 사용할 때 비해 타입 컨버전을 자동으로 지원하기 때문에 조금 편리하다.
* HTTP 세션에 데이터를 넣고 빼고 싶은 경우에는 HttpSession을 사용할 것



**@SessionAttributes**

* @SessionAttributes는 해당 컨트롤러 내에서만 동작
  * 즉, 해당 컨트롤러 안에서 다루는 특정 모델 객체를 세션에 넣고 공유할 때만 사용
* @SessionAttribute는 컨트롤러 밖(인터셉터 또는 필터 등)에서 만들어 준 세션 데이터에 접근할 때 사용한다.



**Interceptor**

```java
public class VisitTimeInterceptor implements HandlerInterceptor {
  
  @Override
  public boolean preHandle(HttpServletRequest request, HttpServletResponse response) {
      HttpSession httpSession = request.getSession();
      if (httpSession.getAttribute("visitTime") == null) {
        httpSession.setAttribute("visitTime", LocalDateTime.now());
      }
      return true;    
  }
}
```



**WebConfig에 Interceptor 등록**

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
  
  @Override
  public void addInterceptor(InterceptorRegistry registry) {
    registry.addInterceptor(new VisitTImeInterceptor());
  }
}
```



**Controller**

```java
@Controller
@SessionAttributes("event")
public class SampleController {
  
  @GetMapping("/events/form/name")
  public String eventsForm(Model model) {
    Event newEvent = new Event();
    newEvent.setLimit(50);
    model.addAttribute("event", newEvent);
   	return "/events/form/name";
  }
  
  @PostMapping("/events/form/name")
  public String eventsFormNameSubmit(@Validated @ModelAttribute Event event, 
													 BindingResult bindResult) {
    if(bindingReuslt.hasError()) {
      return "/events/form-name";
    }
    return "redirect:/events/form/limit";
  }
  
  @GetMapping("/events/form/limit")
  public String eventFromLimit(@ModelAttribute Event event, Model model) {
    model.addAttribute("event", event);
   	return "/events/form/limit";
  }
  
  @PostMapping("/events/form/limit")
  public String eventsFormLimitSubmit(@Validated @ModelAttribute Event event, 
													 BindingResult bindResult,
                           SessionStatus sessionStatus) {
    if(bindingReuslt.hasError()) {
      return "/events/form-limit";
    }
    
    sessionStatus.setComplete();
    return "redirect:/events/list";
  }
  
  @GetMapping("events/list")
  public String getEvents(Model model, @SessionAttribute LocalDateTime visitTime) {
    // visitTime 출력
    System.out.println(visitTime);
    
    Event event = new Event();
    event.setName("spring");
    event.setLimit(10);
    
    List<Event> eventList = new ArrayList<>();
    eventList.add(event);
    
    model.addAttribute(eventList);
    
    return "/events/list";
  }
}
```



