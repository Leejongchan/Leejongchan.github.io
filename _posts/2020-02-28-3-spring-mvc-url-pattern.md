---
layout: post 
title: "요청 맵핑하기 2부: URI 패턴"
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

**[URI, URL, URN 차이점](https://stackoverflow.com/questions/176264/what-is-the-difference-between-a-uri-a-url-and-a-urn) 알아보기**



**여러개의 문자열로 요청 매핑**

```java
@Controller
public class SampleController {
  
  @GetMapping({"/hello", "/hi"})
  @ResponseBody 
  public String hello() {
    return "hello";
  }
}
```



**특정 패턴으로 요청 매핑하기**

@RequestMapping은 다음의 패턴을 지원한다.

1. ?
   * 한글자
   * 예시) "/author/???"의 경우 "/author/123"와 매핑된다.
2. *
   * 여러 글자
   * 예시) "/author/*"의 경우 "/author/keesun"와 매핑된다.
3. **
   * 여러 패스
   * 예시) "/author/**"의 경우 "/author/keesun/book"와 매핑된다.

```java
@Controller
public class SampleController {
  
  @GetMapping("/hello/?")
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
    mockMvc.perform(get("/hello/1"))
      			.andDo(print())
      			.andExpect(status().isOk())
      			.andExpect(content().string("hello"));
  }
}
```



**클래스에 선언한 @RequestMapping과 조합**

클래스에 선언한  URI 패턴뒤에 이어 붙여서 매핑한다.

```java
@Controller
@RequestMapping("/hello")
public class SampleController {
  
  @RequestMapping("/**")
  @ResponseBody 
  public String hello() {
    return "hello";
  }
}
```

```java
@Controller
@RequestMapping("/hello")
public class SampleController {
  
  @RequestMapping("/**")
  // 정규식에 해당하는 패턴을 name으로 받는다.
  @ResponseBody 
  public String hello() {
    return "hello";
  }
}
```



**정규식 표현으로 매핑할 수 있다.**

```java
@Controller
@RequestMapping("/hello")
public class SampleController {
  
  @RequestMapping("/{name:[a-z]+}")
  // 정규식에 해당하는 패턴을 name으로 받는다.
  @ResponseBody 
  public String hello(@PathVariable String name) {
    return "hello" + name;
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
    mockMvc.perform(get("/hello/keesun"))
      			.andDo(print())
      			.andExpect(status().isOk())
      			.andExpect(content().string("hello keesun"));
  }
}
```



**패턴이 중복되는 경우**

가장 구체적으로 맵핑되는 핸들러를 선택한다.

```java
@Controller
@RequestMapping("/hello")
public class SampleController {
  
  @RequestMapping("/keesun")
  @ResponseBody 
  public String helloKeesun() {
    return "hello" + name;
  }
  
  @RequestMapping("/**")
  @ResponseBody 
  public String hello() {
    return "hello" + name;
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
    mockMvc.perform(get("/hello/keesun"))
      			.andDo(print())
      			.andExpect(status().isOk())
      			.andExpect(content().string("hello keesun"))
      			.andExpect(handler().handlerType(SampleController.class))
      			.andExpect(handler().methodName("helloKeesun"));
  }
}
```



**URI 확장자 맵핑 지원**

Spring MVC의 경우 아래와 같이 Mapping을 설정하면 **/keesun.\***으로 매핑되는 것을 지원한다.

```java
@RequestMapping("/keesun")
```

SpringBoot에서는 기본적으로 위와 같은 자동 매핑을 지원하지 않는다.

이 기능은 다음과 같은 이유로 권장하지 않는다.

* 보안 이슈 (RFD Attack)
  * 최신 버전에서는 응답에 RFD Attack을 방지할 수 있는 헤더 정보가 들어간다.
* URI 변수, Path 매개변수, URI 인코딩 사용할 때 불명확하다.
