---
layout: post 
title: "DispatcherServlet 동작 원리 3부 : 커스텀 ViewResolver"
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

**ViewResolver**

ViewResolver를 직접 Bean으로 등록한 것이 없다면 기본전략으로 InternalResourceViewResolver를 사용한다.



**InternalResourceViewResolver 커스텀**

prefix와 suffix를 다음과 같이 설정하여 사용할 수 있다.

```java
@Configuration // Bean 설정 파일(XML 파일을 대체)임을 알려준다.
@ComponentScan // Bean으로 등록되기 위한 Annotation이 부여된 클래스들을 자동으로 IoC Container에 등록되도록 한다.
public class WebConfig {
    
    @Bean
    public ViewResolver viewResolver() {
        InternalResourceViewResolver viewResolver = new InternalResourceViewResolver();
        viewResolver.setPrefix("/WEB_INF/");
        viewResolver.setSuffix(".jsp");
        return viewResolver;
    }
}
```

```java
@org.springframework.stereotype.Controller("/simple")
public class SimpleController implements Controller {
    
    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) {
        return new ModelAndView("simple");
    }
}
```



ViewResolver를 init하는 initViewResolvers() method도 Servlet 생명주기에 의해서 최초 요청시에만 호출되고 이 후에는 init을 다시 호출하지 않는다.


