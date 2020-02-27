---
layout: post 
title: "핸들러 메소드 11부: RedirectAttributes"
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

**Redirect할 때 기본적으로 Model에 들어있는 Primitive type 데이터는 URI 쿼리 매개변수에 추가된다.**

* 스프링 부트에서는 이 기능이 기본적으로 비활성화 되어 있다.

  * Spring MVC 에서는 기본적으로 활성화 되어 있다.

* Ignore-default-model-on-redirect 프로퍼티를 사용해서 활성화 할 수 있다.

  ```properties
  # application.properties
  spring.mvc.ignore-default-model-on-redirect = true
  ```



원하는 값만 Redirect를 할 때 전달하고 싶다면 RedirectAttributes에 명시적으로 추가할 수 있다.

데이터를 받는 곳에서는 @RequestParam 이용해서 하나씩 받아도 되고 @ModelAttribute를 이용해서 받을 수 있다.

@ModelAttribute를 이용할 때는 @SessionAttributes에서 사용하는 이름과 같은 이름의 변수를 사용할 경우 @ModelAttribute를 Session에서 데이터를 먼저 찾기 때문에 Session에 데이터가 있는지 확인해야 한다. 예를 들어 setComplete()를 사용해서 Session을 지운경우 에러가 발생한다.



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
  @ResponseBody
  public String eventsFormNameSubmit(@Validated @ModelAttribute Event event, 
													 BindingResult bindResult) {
    if(bindingReuslt.hasError()) {
      return "/events/form-name"
    }
    return "redirect:/events/form/limit";
  }
  
  @GetMapping("/events/form/limit")
  public String eventFromLimit(@ModelAttribute Event event, Model model) {
    model.addAttribute("event", event);
   	return "/events/form/limit";
  }
  
  @PostMapping("/events/form/limit")
  @ResponseBody
  public String eventsFormLimitSubmit(@Validated @ModelAttribute Event event, 
													 BindingResult bindResult,
                           SessionStatus sessionStatus,
                           RedirectAttributes attributes) {
    if(bindingReuslt.hasError()) {
      return "/events/form-limit"
    }
    
    sessionStatus.setComplete();
    
    // RedirectAttributes에 Attribute 추가
    attributes.addAttribute("name", event.getName());
    attributes.addAttribute("limit", event.getLimit());
    
    return "redirect:/events/list";
  }
  
  @GetMapping("events/list")
  public String getEvents(Model model) {
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





