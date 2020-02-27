---
layout: post 
title: "핸들러 메소드 5부: @ModelAttribute"
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


**@ModelAttribute**

* 여러 곳에 있는 단순 타입 데이터를 복합 타입 객체로 받아오거나 해당 객체를 새로 만들 때 사용할 수 있다.
* 생략가능



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
  public Event postEvent(@ModelAttribute Event event) {
    return event;
  }
}
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
  
  @Test
  public void postEvent() throws Exception {
    mockMvc.perform(post("/events")
                .param("name", "keesun")
                .param("limit", "20"))
            .andDo(print())
            .andExpect(status().isOk)
      		.andExpect(jsonPath("name").value("keesun"));
  }
}
```



**값을 바인딩 할 수 없는 경우**

* BindException 발생 (400 Error)
* Ex) Integer에 String 값을 전달 받는 경우



**바인딩 에러를 직접 다루고 싶은 경우**

* BindingResult 타입의 아규먼트를 바로 오른쪽에 추가한다.

  ```java
  @Controller
  public class SampleController {
    
    @PostMapping("/events")
    @ResponseBody
    public Event postEvent(@ModelAttribute Event event, BindingResult bindResult) {
      if(bindingReuslt.hasError()) {
        bindingResult.getAllErrors().forEach(c -> {
          System.out.println(c.toString());
        });
      }
      return event;
    }
  }
  ```



**바인딩 이후 검증 작업을 추가로 하고 싶은 경우**

@Valid 또는 @Validated Annotation을 사용한다. Validation에 관련한 Error도 BindingResult에 담긴다.

```java
@Controller
public class SampleController {
  
  @PostMapping("/events")
  @ResponseBody
  public Event postEvent(@Valid @ModelAttribute Event event, BindingResult bindResult) {
    if(bindingReuslt.hasError()) {
      bindingResult.getAllErrors().forEach(c -> {
        System.out.println(c.toString());
      });
    }
    return event;
  }
}
```

```java
public class Event {
  
  Integer id;
  
  String name;
  
  @Min(0)
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

