---
layout: post 
title: "요청 맵핑하기 3부: 미디어 타입"
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

**특정 타입의 데이터를 담고 있는 요청만 처리하는 핸들러**

* @RequestMapping(consumes=MediaType.APPLICATION_JSON_UTF8_VALUE)
* Content-Type 헤더로 필터링
* 매치 되지 않는 경우에 415 Not Supported MediaType 응답

```java
@Controller
public class SampleController {
  
  @GetMapping(value = "/hello", consumes = MediaType.APPLICATION_JSON_UTF8_VALUE)
  // "application/json"처럼 String을 사용할 수 있지만 MediaType을 사용하여 오타 방지
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
      			.contentType(MediaType.APPLICATION_JSON_UTF8))
      			.andDo(print())
      			.andExpect(status().isOk())
      			.andExpect(content().string("hello"));
  }
}
```



**특정한 타입의 응답을 만드는 핸들러**

* @RequestMapping(produces=MediaType.APPLICATION_JSON_UTF8_VALUE)
* Accept 헤더로 필터링
  * Request에 Accept 헤더가 설정되지 않는 경우 서버에서 설정한 어떠한 MediaType이라도 매핑이 된다.
* 매치 되지 않는 경우 406 Not Supported 응답

```java
@Controller
public class SampleController {
  
  @GetMapping(
    value = "/hello", 
    consumes = MediaType.APPLICATION_JSON_UTF8_VALUE,
  	produces = MediaType.TEXT_PLAIN_VALUE
  )
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
      				.contentType(MediaType.APPLICATION_JSON)
      				.accpet(MediaType.APPLICATION_JSON))
      			.andDo(print())
      			.andExpect(status().isOk())
      			.andExpect(content().string("hello"));
  }
}
```



consumes, produces 모두 문자열을 입력하는 대신 MediaType을 사용하면 상수를 (IDE에서) 자동 완성으로 사용할 수 있다.



클래스에서 선언한 @RequestMapping에 사용한 것과 조합이 되지 않고 메소드에 사용한 @RequestMapping의 설정으로 덮어쓴다. 조합을 해서 같이 쓰는 것이 아니다. 
