---
layout: post 
title: "DispatcherServlet 동작 원리: 1부"
date: 2020-02-25
excerpt: "백기선 강의 - 스프링 웹 MVC"
tags: 
- Spring
- 백기선 강의
- 스프링 웹 MVC
comments: true 
---

>백기선님의 스프링 웹 MVC 강의 내용을 정리한 내용입니다.
---


# DispatcherServlet 동작원리

Debug모드로 실행하여 Breakpoint를 걸고 DispatcherServlet 동작 과정을 확인할 수 있다.



**DispatcherServlet 초기화**

다음의 특별한 타입의 빈들을 찾거나, 기본 전력에 해당하는 빈들을 등록한다.

1. HandlerMapping
   * 핸들러를 찾아주는 인터페이스
   * 디자인 패턴 중에 Strategy 패턴이 적용되어 있다.
2. HandlerAdapter
   * 핸들러를 실행하는 인터페이스
3. HandlerExcepitonResolver
4. ViewResolver
5. ...



@RestController는 @Controller Annotation을 붙인 Class의 요청을 처리하는 모든 Method에 @ResponseBody Annotation을 붙인 것과 같다.

```java
@Controller
public class HelloController {
    
    @Autowired
    HelloService helloService;
    
    @GetMapping("/hello")
    @ResponseBody
    public String hello() {
        return "Hello, " + helloService.getName();
    }
}
```



**DispatcherServlet 동작 순서**

1. 요청을 분석한다
   * 로케일, 테마, 멀티파트 등
2. HandlerMapping에게 위임하여 요청을 처리할 Handler를 찾는다
3. 등록되어 있는 HandlerAdapter 중에 해당 Handler를 실행할 수 있는 HandlerAdapter를 찾는다.
4. 찾아낸 HandlerAdapter를 사용해서 Handler의 응답을 처리한다.
5. 부가적으로 예외가 발생했다면, 예외 처리 Handler에 요청 처리를 위임한다.
6. Handler의 리턴값을 보고 어떻게 처리할지 판단한다.
   * Handler의 리턴값을 보고 어떻게 처리할지 판단한다.
     1. 뷰 이름에 해당하는 뷰를 찾아서 모델 데이터를 랜더링한다.
     2. @ResponseEntity가 있다면 Converter를 사용해서 응답 본문을 만든다.
7. 최종적으로 응답을 보낸다.
