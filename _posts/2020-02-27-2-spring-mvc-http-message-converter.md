---
layout: post 
title: "HTTP 메세지 컨버터"
date: 2020-02-27
excerpt: "백기선 강의 - 스프링 웹 MVC"
tags: 
- Spring
- 백기선 강의
- 스프링 웹 MVC
comments: true 
---

>백기선님의 스프링 웹 MVC 강의 내용을 정리한 내용입니다.
---


**HTTP 메세지 컨버터**

요청 본문에서 메세지를 읽어들이거나(@RequestBody) 응답 본문에 메세지를 작성할 때(@ResponseBody) 사용한다.

```java
@Controller
public class SampleController {
  
  @GetMapping("/message")
  @ResponseBody
  puvlic String message(@RequestBody Person person) {
    return "hello person"
  }
}
```



**기본으로 등록된 HTTP 메세지 컨버터**

1. 바이트 배열 컨버터
2. 문자열 컨버터
3. Resource 컨버터
   * octet이라는 타입의 요청이나 응답의 경우
4. Form 컨버터
   * HTML Form 데이터를 Map으로 변환 또는 Map에서 Form 데이터로 변환
5. pom.xml에 dependency가 등록된 경우
   * JAXB2 컨버터
   * Jackson2 컨버터
   * Jackson 컨버터
   * Gson 컨버터
   * Atom 컨버터
   * RSS 컨버터



```java
@RestController
public class SampleController {
  
  @GetMapping("/message")
  public String message(@RequestBody String body) {
    return body;
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
  public void stringMessage() throws Exception {
    this.mockMvc.perform(get("/message")
      		.content("hello"))
      		.andDo(print())
      		.andExpect(status().isOk())
					.andExpect(content().string("hello"));
  }
}
```



Request의 Content-type에 따라 어떤 HTTP 메세지 컨버터가 정해지는지 결정된다.



**새로운 HTTP 메세지 컨버터 추가**

1. Config 설정에 컨버터 추가

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
  
 
  // 기본으로 제공되는 HTTP 메세지 컨버터를 모두 사용할 수 없다.
  @Override
  public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
    
  }
  
  // 기본으로 등록해주는 컨버터에 새로운 컨버터를 추가한다.
  @Override
  public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
    
  }
}
```

2. **의존성 추가로 컨버터 등록** (추천)
   * 메이븐 또는 그래들 설정에 의존성을 추가하면 그에 따른 컨버터가 자동으로 등록 된다.
   * Spring Framework(Not SpringBoot)의 기능인 WebMvcConfigurationSupport Class에 의존성에 따라 조건적으로 등록된다.
