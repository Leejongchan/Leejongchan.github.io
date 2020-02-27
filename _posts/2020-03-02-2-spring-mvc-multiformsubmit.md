---
layout: post 
title: "핸들러 메소드 9부: 멀티 폼 서브밋"
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

**실행 흐름**

1. /events/form/name에서 name을 입력받는다.
2. /events/form/limit으로 redirect하여 limit을 입력받는다.
   * /events/form/name에서 Session에 등록된 Event를 @ModelAttribute를 통해 받는다.
3. Limit을 입력하여 Submit하면 Event List Page로 이동한다.


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



**Thymeleaf form-name**

```html
<!-- resources/templates/events/form-name.html -->
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
	<meta charset="UTF-8">
  <title>Create New Event</title>
</head>
</html>
<body>
<form action="#" th:action="@{/events/form/name}" method="post" th:object="${event}">
  <p th:if="${#fields.hasErrors('name')}" th:errors="*{name}">Incorrect date<p>
  Name: <input type="text" title="name" th:field="*{name}"/>
  <input type="submit" value="Create"/>
</form>
</body>
```



**Thymeleaf form-list**

```html
<!-- resources/templates/events/form-name.html -->
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
	<meta charset="UTF-8">
  <title>Create New Event</title>
</head>
</html>
<body>
<form action="#" th:action="@{/events/form/limit}" method="post" th:object="${event}">
  <p th:if="${#fields.hasErrors('limit')}" th:errors="*{limit}">Incorrect date<p>
  Limit: <input type="text" title="limit" th:field="*{limit}"/>
  <input type="submit" value="Create"/>
</form>
</body>
```

