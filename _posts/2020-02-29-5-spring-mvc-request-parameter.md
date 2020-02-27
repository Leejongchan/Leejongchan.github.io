---
layout: post 
title: "핸들러 메소드 3부: 요청 매개변수 (단순 타입)"
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

# 핸들러 메소드 3부: 요청 매개변수 (단순 타입)

**@RequestParam**

* 요청 매개변수에 들어있는 단순 타입 데이터를 메소드 아규먼트로 받아올 수 있다.
* 값이 반드시 있어야 한다.
  * required=false 또는 Optional을 사용해서 부가적인 값으로 설정할 수도 있다.
  * @RequestParam(required = false, defaultValue = "keesun") String name
* String이 아닌 값들은 타입 컨버전을 지원한다.
* Map<String, String> 또는 MultiValueMap<String, String>에 사용해서 모든 요청 매개변수를 받아 올 수 있다.
* 이 Annotation을 생략할 수 있다.



**요청 매개 변수**

1. 쿼리 매개변수
2. 폼 데이터



```java
@Controller
public class SampleController {
  
  @GetMapping("/events")
  @ResponseBody
  public Event getEvent(@RequestParam String name) {
    Event event = new Event();
    event.setName(name);
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
  public void deleteEvents() throws Exception {
    mockMvc.perform(get("/events")
            	 .param("name", keesun))
      			.andDo(print())
      			.andExpect(status().isOk())
      			.andExpect(jsonPath("name").value("keesun"));
  }
}
```



**참고**
 * [https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-requestparam](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-requestparam)

