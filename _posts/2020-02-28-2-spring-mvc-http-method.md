---
layout: post 
title: "요청 맵핑하기 1부: HTTP METHOD"
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

**HTTP Method**

* GET
* POST
  PUT
* PATCH
* DELETE



**GET 요청**

* 클라이언트가 서버의 리소스를 요청할 때 사용한다.
* 캐싱할 수 있다.
  * 응답을 보낼 때 캐시와 관련된 헤더를 응답에 넣어 보낸다. 이 후 동일한 요청을 브라우저에서 보낼 때 요청이 조건적인 GET으로 바뀔 수 있다. 조건적인 GET은 if-not-modified, modified-since 헤더를 사용하는 것으로 조건에 따라 응답에 따라 서버가 응답 바디(본문)를 보내지 않아도 클라이언트가 캐싱했던 정보를 보여주기 때문에 요청처리가 빠르고 서버의 리소스를 아낄 수 있다.
* 브라우저 기록에 남는다.
* 북마크 할 수 있다.
* 민감한 데이터를 보낼 떄 사용하지 말 것 (URL에 노출된다.)
* idemponent
  * 동일한 GET요청은 항상 동일한 응답을 응답한다.
  * POST 요청은 idemponent하지 않다.



**POST 요청**

* 클라이언트가 서버의 리소스를 수정하거나 새로 만들 때 사용한다.
* 서버에 보내는 데이터를 POST 요청 본문에 담는다.
* 캐시할 수 없다.
* 브라우저 기록에 남지 않는다.
* 북마크 할 수 없다.
* 데이터 길이 제한이 없다.



**PUT 요청**

* URI에 해당하는 데이터를 새로 만들거나 수정할 때 사용한다.
* POST와 다른점은 URI에 대한 의미가 다르다.
  * POST의 URI는 보내는 데이터를 **처리할** 리소스를 지칭한다.
  * PUT의 URI는 보내는 데이터에 **해당하는** 리소스를 지칭한다.
* Idemponent



**PATCH 요청**

* PUT과 비슷하지만, 기존 엔티티와 새 데이터의 차이점만 보낸다는 차이가 있다.
  * 리소스가 가지고 있는 일부 데이터를 수정하고 싶은 경우에 사용한다.
* Idemponent



**DELETE 요청**

* URI에 해당하는 리소스를 삭제할 때 사용한다.
* Idemponent



**스프링 웹 MVC에서 HTTP Method 맵핑하기**

* @RequestMapping(method=RequestMethod.GET)
* @RequestMapping(method={RequestMethod.GET, RequestMethod.POST})
* @GetMapping, @PostMapping 등



기본적으로 Http Method를 지정하지 않으면 모든 Method를 허용한다.

```java
@Controller
public class SampleController {
  
  @RequestMapping("/hello")
  @ResponseBody 
  public String hello() {
    return "hello";
  }
}
```

```java
@RunWith(SpringRunner.class)
// Spring에서 제공하는 JUnit Annotation으로 Spring Test 실행 시 효율적으로 Test하도록 돕는다.
@WebMvcTest
// Web쪽만 Test할 것이므로 @WebMvcTest 이용
public class SampleControllerTest {
  
  @Autowired
  MockMvc mockMvc;
  // @WebMvcTest Annotation을 사용하기 때문에 사용 가능
  
  @Test
  // public void로 생성해야 JUnit Test로 인식된다.
  public void helloTest() throws Exception {
    mockMvc.perform(get("/hello"))
      			.andDo(print())
      			.andExpect(status().isOk())
      			.andExpect(content().string("hello"));
    
    mockMvc.perform(post("/hello"))
      			.andDo(print())
      			.andExpect(status().isOk())
      			.andExpect(content().string("hello"));
  }
}
```



특정 Http Method을 허용하는 경우

```java
@Controller
public class SampleController {
  
  @RequestMapping(value = "/hello", method = {RequestMethod.GET, RequestMethod.PUT})
  @ResponseBody 
  public String hello() {
    return "hello";
  }
}
```

```java
@RunWith(SpringRunner.class)
// Spring에서 제공하는 JUnit Annotation으로 Spring Test 실행 시 효율적으로 Test하도록 돕는다.
@WebMvcTest
// Web쪽만 Test할 것이므로 @WebMvcTest 이용
public class SampleControllerTest {
  
  @Autowired
  MockMvc mockMvc;
  // @WebMvcTest Annotation을 사용하기 때문에 사용 가능
  
  @Test
  // public void로 생성해야 JUnit Test로 인식된.
  public void helloTest() throws Exception {
    mockMvc.perform(get("/hello"))
      			.andDo(print())
      			.andExpect(status().isOk);
    
    mockMvc.perform(put("/hello"))
      			.andDo(print())
      			.andExpect(status().isOk);
    
    mockMvc.perform(post("/hello"))
      			.andDo(print())
      			.andExpect(status().isMethodNotAllowed());
  }
}
```



**Controller안의 모든 Handler에 HTTP Method 적용**

```java
@Controller
@RequestMapping(method = RequestMethod.GET)
// 모든 Handler에 HTTP Method GET 요청 적용
public class SampleController {
  
  @RequestMapping("/hello")
  @ResponseBody 
  public String hello() {
    return "hello";
  }
}
```

