---
layout: post 
title: "기타 WebMvcConfigurer 설정"
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

**ArgumentResolver 설정 (addArgumentResolvers)**

스프링 MVC가 제공하는 기본 Argument Resolver 이외에 커스텀한 Argument Resolver를 추가하고 싶을 때 설정한다.

```java
@RestController
public class SampleController {
  
  @GetMapping("/message")
  public String message(@RequestBody String body) { return body; }
  // @RequestBody와 같은 Spring MVC가 제공하는 기본 Arguement 외에 커스터마이징하여 추가할 수 있다.
}
```



**Return Value 핸들러 설정(addReturnValueHandelrs)**

스프링 MVC가 제공하는 기본 리턴 값 핸들러 이외에 리턴 핸들러를 추가하고 싶을 때 설정한다.

```java
@RestController
public class SampleController {
  
  @GetMapping("/message")
  public String message(@RequestBody String body) { return body; }
  // String과 같은 Spring MVC가 제공하는 기본 Return Value 외에 커스터마이징하여 추가할 수 있다.
}
```



**View Controller 설정(addViewControllers)**

단순하게 요청 URL을 특정 뷰로 연결하고 싶을 떄 사용할 수 있다.

```java
@Controller
public class SampleController {
  
  @GetMapping("/hi")
  public String hi() { return "hi"; }
}
```

```java
// 위와 같이 Controller에서 코드를 작성하지 않고 View를 Return하도록 할 수 있다.
@Configuration
public class WebConfig implements WebMvcConfigurer {
  
  @Override
  public void addViewControllers(ViewControllerRegistry registry) {
    registry.addViewController("/hi").setViewName("hi");
  }
}
```



**비동기 처리 설정(configureAsyncSupport)**



**Content Negotiation 설정(configureContentNegotiation)**

요청 본문 또는 응답 본문을 어떤 (MIME) 타입으로 보내야 하는지 결정하는 전략을 설정한다.



**CORS 설정**

Cross Origin 요청 처리 설정으로 같은 도메인에서 온 요청이 아니더라도 처리를 허용하고 싶다면 설정한다.



**View Resolver 설정**

핸들러에서 리턴하는 뷰 이름에 해당하는 문자열을 View 인스턴스로 바꿔줄 View Resolver를 설정한다.
