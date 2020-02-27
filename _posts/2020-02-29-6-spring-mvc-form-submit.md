---
layout: post 
title: "핸들러 메소드 4부: 폼 서브밋"
date: 2020-02-29
excerpt: "백기선 강의 - 스프링 웹 MVC"
tags: 
- Spring
- 백기선 강의
- 스프링 웹 MVC
comments: true 
---

>백기선님의 스프링 웹 MVC 강의 내용을 정리한 내용입니다.
---

**폼을 보여줄 요청 처리**

```java
@Controller
public class SampleController {
  @GetMapping("/events/form")
  public String eventsForm(Model model) {
    Event newEvent = new Event();
    newEvent.setLimit(50);
    
    model.addAttribute("event", newEvent());
    return "events/form";
  }
  
  @PostMapping("/events")
  @ResponseBody
  public Event postEvent(@RequestParam String name, @RequestParam Integer limit) {
    Event event = new Event();
    event.setName(name);
    event.setLimit(limit);
    return event;
  }
}
```

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
  <input type="text" title="name" th:field="*{name}"/>
  <input type="text" title="limit" th:field="*{limit}"/>
  <input type="submit" value="Create"/>
</form>
</body>
```

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
      			.andExpect(model().attributeExists("events"));
  }
}
```



**Thymeleaf**

* @{}
  * URL 표현식
* ${}
  * variable 표현식
* *{}
  * selection 표현식



**참고**

* [https://www.thymeleaf.org/doc/articles/standarddialect5minutes.html](https://www.thymeleaf.org/doc/articles/standarddialect5minutes.html)
