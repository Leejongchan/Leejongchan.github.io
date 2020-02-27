---
layout: post 
title: "핸들러 메소드 2부: URI 패턴"
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

**@PathVariable**

* 요청 URI 패턴의 일부를 핸들러 메소드 아규먼트로 받는 방법
* 타입 변환 지원
  * ex) String으로 들어온 값을 Integer로 변환한다.
* (기본)값이 반드시 있어야 한다.
* Optional 지원
  * @PathVariable(require = false) 와 같다.



```java
@Controller
public class SampleController {
  
  @GetMapping("/events/{id}")
  @ResponseBody
  public Event getEvent(@PathVariable Integer id) {
    Event event = new Event();
    event.setId(id);
    
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
    mockMvc.perform(get("/events/1"))
      			.andDo(print())
      			.andExpect(status().isOk())
      			.andExpect(jsonPath("id").value(1));
  }
}
```



**@MatrixVariable**

* 요청 URI 패턴에서 키/값 쌍의 데이터를 메소드 아규먼트로 받는 방법
* 타입 변환 지원
* (기본) 값이 반드시 있어야 한다.
* Optional 지원
* 이 기능은 기본적으로 비활성화 되어 있어 아래와 같은 Configuration 설정이 필요하다.



```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
  
  @Override
  public void configurePathMatch(PathMatchConfigurer configurer) {
    UrlPathHelper urlPathHelper = new UrlPathHelper();
    urlPathHelper.setRemoveSemicolonContent(false);
    configurer.setUrlPathHelper(urlPathHelper);
  }
}
```

```java
@Controller
public class SampleController {
  
  @GetMapping("/events/{id}")
  @ResponseBody
  public Event getEvent(@PathVariable Integer id, @MatrixVariable String name) {
    Event event = new Event();
    event.setId(id);
    
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
    mockMvc.perform(get("/events/1;name=keesun"))
      			.andDo(print())
      			.andExpect(status().isOk())
      			.andExpect(jsonPath("id").value(1));
  }
}
```



**참고**

* [TypeConversion](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-typeconversion)
* [MatrixVariable](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-matrix-variables)
