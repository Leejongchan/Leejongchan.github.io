---
layout: post 
title: "HTTP 요청 맵핑하기 7부: 연습문제"
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

**다음 요청을 처리할 수 있는 핸들러 메소드를 맵핑하는 @RequestMapping (또는 @GetMapping, @PostMagging 등)을 정의하세요.**

1. GET /events
2. GET /events/1
   GET /events/2
   GET /events/3,
   ...
3. POST /events, CONTENT-TYPE: application/json, ACCEPT: application/json
4. DELETE /events/1,
   DELETE /events/2,
   DELETE /events/3,
   ...
5. PUT /events/1, CONTENT-TYPE: application/json, ACCEPT: application/json
   PUT /events/2, CONTENT-TYPE: application/json, ACCEPT: application/json



**1번 문제**

```java
public class SampleController {
  
  @GetMapping("/events")
  @ResponseBody
  public void events() {
    return "events";
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
  public void getEvents() throws Exception {
    mockMvc.perform(get("/events"))
      			.andExpect(status().isOk());
  }
}
```



**2번 문제**

```java
public class SampleController {
  
  @GetMapping("/events/{id}")
  @ResponseBody
  public void events(@PathVariable int id) {
    return "events";
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
  public void getEventsWithId() throws Exception {
    mockMvc.perform(get("/events/1"))
      			.andExpect(status().isOk());
  }
}
```



**3번 문제**

```java
public class SampleController {
  
  @PostMapping(
    value = "/events",
  	produces = MediaType.APPLICATION_JSON_UTF8_VALUE,
  	consumes = MediaType.APPLICATION_JSON_UTF8_VALUE
  )
  @ResponseBody
  public void events() {
    return "events";
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
  public void createEvents() throws Exception {
    mockMvc.perform(post("/events")
      				.contentType(MediaType.APPLICATION_JSON_UTF8)
              .accept(MediaType.APPLICATION_JSON))
      			.andExpect(status().isOk());
  }
}
```



**4번 문제**

```java
public class SampleController {
  
  @DeleteMapping("/events/{id}")
  @ResponseBody
  public void events() {
    return "events";
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
    mockMvc.perform(delete("/events/1"))
      			.andExpect(status().isOk());
  }
}
```



**5번 문제**

```java
@RequestMapping(
  produces = MediaType.APPLICATION_JSON_UTF8_VALUE, 
  consumes = MediaType.APPLICATION_JSON_UTF8_VALUE
)
public class SampleController {
  
  @PutMapping(value = "/events/{id}")
  @ResponseBody
  public void events() {
    return "events";
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
  public void createEvents() throws Exception {
    mockMvc.perform(put("/events/1")
      				.contentType(MediaType.APPLICATION_JSON_UTF8)
              .accept(MediaType.APPLICATION_JSON))
      			.andExpect(status().isOk());
  }
}
```

