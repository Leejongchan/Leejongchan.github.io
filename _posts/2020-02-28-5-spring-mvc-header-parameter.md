---
layout: post 
title: "요청 맵핑하기 4부: 헤더와 매개변수"
date: 2020-02-28
excerpt: "백기선 강의 - 스프링 웹 MVC"
tags: 
- Spring
- 백기선 강의
- 스프링 웹 MVC
comments: true 
---

>백기선님의 스프링 웹 MVC 강의 내용을 정리한 내용입니다.
---

**특정 헤더가 있는 요청을 처리하고 싶은 경우**

* @RequestMapping(header = "key")

```java
@Controller
public class SampleController {
  
  @GetMapping(value = "/hello", headers = HttpHeaders.FROM)
  @ResponseBody 
  public String hello() {
    return "hello";
  }
}
```

```java
@RunWith(SpringRunner.class)
@WebMvcTest
public class SampleControllerTest {
  
  @Autowired
  MockMvc mockMvc;
  
  @Test
  public void helloTest() throws Exception {
    mockMvc.perform(get("/hello")
                .header(HttpHeaders.FROM, "localhost"))
      			.andDo(print())
      			.andExpect(status().isOk())
      			.andExpect(content().string("hello"));
  }
}
```



**특정 헤더가 없는 요청을 처리하고 싶은 경우**

* @RequestMapping(header = "!key")

```java
@Controller
public class SampleController {
  
  @GetMapping(value = "/hello", headers = "!" + HttpHeaders.FROM)
  @ResponseBody 
  public String hello() {
    return "hello";
  }
}
```

```java
@RunWith(SpringRunner.class)
@WebMvcTest
public class SampleControllerTest {
  
  @Autowired
  MockMvc mockMvc;
  
  @Test
  public void helloTest() throws Exception {
    mockMvc.perform(get("/hello")
                .header(HttpHeaders.AUTHORIZATION, "111"))
      			.andDo(print())
      			.andExpect(status().isOk())
      			.andExpect(content().string("hello"));
  }
}
```



**특정한 헤더 키/값이 있는 요청을 처리하고 싶은 경우**

* @RequestMapping(header = "key=value")

```java
@Controller
public class SampleController {
  
  @GetMapping(value = "/hello", headers = HttpHeaders.AUTHORIZATION + "=" + "111")
  @ResponseBody 
  public String hello() {
    return "hello";
  }
}
```

```java
@RunWith(SpringRunner.class)
@WebMvcTest
public class SampleControllerTest {
  
  @Autowired
  MockMvc mockMvc;
  
  @Test
  public void helloTest() throws Exception {
    mockMvc.perform(get("/hello")
                .header(HttpHeaders.AUTHORIZATION, "111"))
      			.andDo(print())
      			.andExpect(status().isOk())
      			.andExpect(content().string("hello"));
  }
}
```



**특정 요청 매개변수 키를 가지고 있는 요청을 처리하고 싶은 경우**

* RequestMapping(params = "a")

```java
@Controller
public class SampleController {
  
  @GetMapping(value = "/hello", params = "name")
  @ResponseBody 
  public String hello() {
    return "hello";
  }
}
```

```java
@RunWith(SpringRunner.class)
@WebMvcTest
public class SampleControllerTest {
  
  @Autowired
  MockMvc mockMvc;
  
  @Test
  public void helloTest() throws Exception {
    mockMvc.perform(get("/hello")
                .param("name", "keesun"))
      			.andDo(print())
      			.andExpect(status().isOk())
      			.andExpect(content().string("hello"));
  }
}
```



**특정한 요청 매개변수가 없는 요청을 처리하고 싶은 경우**

* @RequestMapping(params = "!a")



**특정한 요청 매개변수 키/값을 가지고 있는 요청을 처리하고 싶은 경우**

* @RequestMapping(params = "a=b")
