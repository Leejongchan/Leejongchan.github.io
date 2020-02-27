---
layout: post 
title: "요청 맵핑하기 5부: HEAD와 OPTIONS"
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

**우리가 구현하지 않아도 스프링 웹 MVC에서 자동으로 처리하는 HTTP Method**

* HEAD
* OPTIONS



**HEAD**

GET 요청과 동일하지만 응답 본문을 받아오지 않고 응답 헤더만 받아온다.



```java
@Controller
public class SampleController {
  
  @GetMapping(value = "/hello")
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
    mockMvc.perform(head("/hello"))
      			.andDo(print())
      			.andExpect(status().isOk());
  }
}
```





**OPTIONS**

* 사용할 수 있는 HTTP Method 제공한다.
* 서버 또는 특정 리소스가 제공하는 기능을 확인할 수 있다.
* 서버는 Allow 응답 헤더에 사용할 수 있는 HTTP Method 목록을 제공해야 한다.

```java
@Controller
public class SampleController {
  
  @GetMapping(value = "/hello")
  @ResponseBody 
  public String hello() {
    return "hello";
  }
  
  @PostMapping(value = "/hello")
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
    mockMvc.perform(options("/hello"))
      			.andDo(print())
      			.andExpect(status().isOk())
      			.andExpect(header().stringValues(HttpHeaders.ALLOW,
                                             hasItems(
                                               containsString("GET"), 
                                               containsString("POST"),
                                               containsString("HEAD"),
                                               containsString("OPTIONS")
                                               )));
  }
}
```

