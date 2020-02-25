---
layout: post 
title: "DispatcherServlet 동작 원리: 2부"
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


# View를 Return 하는 경우

**DispatcherServlet 동작 순서**

1. 요청을 분석한다
   * 로케일, 테마, 멀티파트 등
2. HandlerMapping에게 위임하여 요청을 처리할 Handler를 찾는다.
   * DispatcherServlet에 기본적으로 BeanNameHandlerMapping과 RequestMappingHandlerMapping이 등록되어 있다.
3. 등록되어 있는 HandlerAdapter 중에 해당 Handler를 실행할 수 있는 HandlerAdapter를 찾는다.
4. 찾아낸 HandlerAdapter를 사용해서 Handler의 응답을 처리한다.
5. 부가적으로 예외가 발생했다면, 예외 처리 Handler에 요청 처리를 위임한다.
6. Handler의 리턴값을 보고 어떻게 처리할지 판단한다.
   * Handler의 리턴값을 보고 어떻게 처리할지 판단한다.
     1. **뷰 이름에 해당하는 뷰를 찾아서 모델 데이터를 랜더링한다.**
     2. @ResponseEntity가 있다면 Converter를 사용해서 응답 본문을 만든다.
7. 최종적으로 응답을 보낸다.



```java
@Controller
public class HelloController {
    
    @Autowired
    HelloService helloService;
    
    @GetMapping("/hello")
    @ResponseBody
    public String hello() {
        return "Hello, " + helloService.getName();
        // DispatcherServlet 동작 과정에서 View에 Model을 Rendering하는 ModelAndView 객체가 NULL이다.
    }
    
    // ResponseBody 없이 문자열만 Return
    @GetMapping("/sample")
    public String sample() {
        return "/WEB-INF/sample.jsp";
        // 이 경우에는 DispatcherServlet 동작 과정에서 ModelAndView 객체가 NULL이 아니다.
    }
}
```

```java
// sample.jsp
<html>
<body>
    <h2>Hello World!</h2>
</body>
</html>
```



**BeanNameUrlHandlerMapping이 요청을 처리할 Handler를 찾아주는 경우**

다음과 같은 경우는 RequestMappingHandlerMapping이 아닌 BeanNameUrlHandlerMapping가 Handler를 찾아준다. 그리고 Handler를 실행할 수 있는 Adapter도 RequestMappingHandlerMapping를 실행하는 Adapter와 다르다.

```java
@org.springframework.stereotype.Controller("/simple") // simple이라는 요청을 처리하는 Handler가 된다.
public class SimpleController implements Controller {
    
    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) {
        return new ModelAndView("/WEB-INF/simple.jsp");
        // Parameter로 View 정보, View에서 사용할 Model 정보를 줄 수 있다.
    }
}
```

