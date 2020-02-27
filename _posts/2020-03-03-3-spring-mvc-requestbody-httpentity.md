---
layout: post 
title: "핸들러 메소드 15부: @RequestBody & HttpEntity"
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

**@RequestBody**

* 요청 본문(body)에 들어있는 데이터를 HttpMessageConverter를 통해 변환한 객체로 받아올 수 있다.
* @Vaild 또는 @Validated를 사용해서 값을 검증 할 수 있다.
* BindingResult 아규먼트를 사용해 코드로 바인딩 또는 검증 에러를 확인할 수 있다.



Bean으로 등록된 MessageConverter들은 HandlerAdapter가 Argument를 Resolving할 때 Converter를 선택해서 사용한다.



**HttpMessageConverter**

* 스프링 MVC 설정(WebMvcConfigurer)에서 설정할 수 있다.
* configureMessageConverters
  * 기본 메세지 컨버터 대체
* extendMessageConverters
  * 메세지 컨버터에 추가
* 기본 컨버터
  * WebMvcConfigurationSupport.addDefaultHttpMessageConverters



**Controller**

```java
@RestController
@RequestMapping("/api/events")
public class EventApi {
  
  @PostMapping
  public Event createEvent(@RequestBody @Valid Event event, BindingResult bindingResult) {
    //save event
    if(bindingResult.hasErrors()) {
      bindingResult.getAllErrors().forEach(error -> {
        System.out.println(error);
      });
    }
    
    return event;
  }
}
```



**Test**

```java
@RunWith(SpringRunner.class)
@SpringBootTest
@AutoConfigureMockMvc
public class EventApiTest {
  
  @Autowired
  ObjectMapper objectMapper;
  
  @Autowired
  MockMvc mockMvc;
  
  @Test
  public void createEvent() throws Exception {
    Event event = new Event();
    event.setName("keesun");
    event.setLimit(20);
    
    String json = objectMapper.writeValueAsString(event);
    
    mockMvc.perform(post("/api/events")
              .contentType(MediaType.APPLICATION_JSON_UTF8)
              .content(json))
      			.andDo(print())
      			.andExpect(status().isOk)
      			.andExpect(jsonPath("name").value("keesun"))
      			.andExpect(jsonPath("limit").value(20));
  }
}
```



**HttpEntity**

@RequestBody와 비슷하지만 추가적으로 요청 헤더 정보를 사용할 수 있다.

```java
@RestController
@RequestMapping("/api/events")
public class EventApi {
  
  @PostMapping
  public Event createEvent(HttpEntity<Event> event) {
    //save event
    MediaType mediaType = event.getHeaders().getContentType();
    System.out.println(contentType);
    
    return request.getBody();
  }
}
```

