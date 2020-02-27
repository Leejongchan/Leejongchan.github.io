---
layout: post 
title: "핸들러 메소드 7부: 폼 서브밋(에러 처리)"
date: 2020-03-01
excerpt: "백기선 강의 - 스프링 웹 MVC"
tags: 
- Spring
- 백기선 강의
- 스프링 웹 MVC
comments: true 
---

>백기선님의 스프링 웹 MVC 강의 내용을 정리한 내용입니다.
---

**Controller**

```java
@Controller
public class SampleController {
  
  @GetMapping("/events/form")
  public String eventsForm(Model model) {
    Event newEvent = new Event();
    newEvent.setLimit(50);
    model.addAttribute("event", newEvent);
   	return "/events/form";
  }
  
  @PostMapping("/events")
  @ResponseBody
  public String createEvent(@Validated @ModelAttribute Event event, 
													BindingResult bindResult) {
    if(bindingReuslt.hasError()) {
      return "/events/form";
    }
    
   	List<Event> eventList = new ArrayList<>();
    eventList.add(event);
    model.addAttribute("eventList", eventList);
    // model.addAttribute(evecntList); 와 같은 코드
    
    return "/events/list";
  }
}
```



**Event POJO**

```java
public class Event {
  
  interface ValidateLimit {}
  interface ValidateName {}
  
  Integer id;
  
  @NoBlank(groups = ValidateName.class)
  String name;
  
  @Min(value = 0, groups = ValidateLimit.class)
  Integer limit;
  
  public Integer getId() {
    return id;
  }
  
  public void setId(Integer id) {
    this.id = id;
  }
  
  public String getName() {
    return name;
  }
  
  public void setName(String name) {
    this.name = name;
  }
  
  public Integer getLimit() {
    return limit;
  }
  
  public void setLimit(Integer limit) {
    this.limit = limit;
  }
}
```



**Thymeleaf Event Form**

```html
<!-- resources/templates/events/form.html -->
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
	<meta charset="UTF-8">
  <title>Create New Event</title>
</head>
</html>
<body>
<form action="#" th:action="@{/events}" method="post" th:object="${event}">
  <p th:if="${#fields.hasErrors('limit')}" th:errors="*{limit}">Incorrect date<p>
  <p th:if="${#fields.hasErrors('name')}" th:errors="*{name}">Incorrect date<p>
  <input type="text" title="name" th:field="*{name}"/>
  <input type="text" title="limit" th:field="*{limit}"/>
  <input type="submit" value="Create"/>
</form>
</body>
```



**Thymeleaf Event List**

```html
<!-- resources/templates/events/list.html -->
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
	<meta charset="UTF-8">
  <title>Event List</title>
</head>
</html>
<body>
<a th:href="@{/events/form}">Create New Event</a>
  <div th:unless="${#lists.isEmpty(eventList)}">
    <ul th:each="event: ${eventList}">
      <p th:text="${event.Name}">Event Name</p>
    </ul>
  </div>
</body>
```



**Test**

```java
@RunWith(SpringRunner.class)
@WebMvcTest
public class SampleContollerTest {
  
  @Autowired
  MockMvc mockMvc;
  
  @Test
  public void eventForm() throws Exception {
    mockMvc.perform(get("/events/form"))
      			.andDo(print())
      			.andExpect(view().name("/events/form"))
      			.andExpect(model().attributeExists("event"));
  }
  
  @Test
  public void eventForm() throws Exception {
    ResultActions result = mockMvc.perform(get("/events")
                .param("name", "keesun")
                .param("limit", "-10"))
      			.andDo(print())
      			.andExpect(status().isOk)
      			.andExpect(model().hasErrors());
    ModelAndView mav = result.andReturn().getModelAndView();
    Map<String, Object> model = mav.getModel();
  }
}
```



**Post / Redirect / Get 패턴**

* 요즘 브라우저는 Post 요청 이후에 브라우저를 리프래시하면 폼 서브밋을 다시할지 묻는 팝업 창이 나타난다.
* Post 요청 이후에 브라우저를 리프래시 하더라도 폼 서브밋이 발생하지 않도록 하는 패턴이다.

* View를 Return하는 것이 아니라 다른 API로 요청을 redirect한다.
* [https://en.wikipedia.org/wiki/Post/Redirect/Get](https://en.wikipedia.org/wiki/Post/Redirect/Get)

```java
@Controller
public class SampleController {
  
  @GetMapping("/events/form")
  public String eventsForm(Model model) {
    Event newEvent = new Event();
    newEvent.setLimit(50);
    model.addAttribute("event", newEvent);
   	return "/events/form";
  }
  
  @PostMapping("/events")
  public String createEvent(@Validated @ModelAttribute Event event, 
													BindingResult bindResult) {
    if(bindingReuslt.hasError()) {
      return "/events/form";
    }
    
    // Database에 Event 저장
    
    return "redirect:/events/list";
  }
  
  @GetMapping("/events/list")
  public String getEvents(Model model) {
   	List<Event> eventList = new ArrayList<>();
    
    // Database에서 event 읽어와 eventList에 저장
    
    model.addAttribute(eventList);
    
    return "/events/list";
  }
}
```

