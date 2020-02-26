---
layout: post 
title: "스프링 MVC 빈 설정"
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


**@Configuration을 사용한 자바 설정 파일에 직접 @Bean 사용해서 등록하기**

web.xml file에 아래 Config 파일을 등록한다.

```java
@Configuration
@ComponentScan
public class WebConfig {
    
    @Bean
    public HandlerMapping handlerMapping() {
        RequestMappingHandlerMapping handlerMapping = new RequestMappingHandlerMapping();
        handlerMapping.setInterceptors();
        // 핸들러를 찾아 처리하기 전 또는 이후에 Servlet Filter와 비슷한 Interceptor가 있다.
        // Interceptor는 Bean으로 등록될 수 있고 따라서 Spring IoC Container의 장점을 활용할 수 있다. 서블릿보다 더 유연할 수 있다.
        handlerMapping.setOrder(Ordered.HIGHEST_PRECEDENCE);
        return handlerMapping;
    }
    
    @Bean
    public HandlerAdapter handlerAdapter() {
        RequestMappingHandlerAdapter handlerAdapter = new RequestMappingHandlerAdapter();
        //handlerAdapter.setArgumentResolver(); // Handler가 처리하는 Method의 Parameter에 사용할 수 있는 것들을 설정을 통해 추가적인 Argument를 정의할 수 있다.
        return handlerAdapter;
    }
}
```



위와 같이 Bean을 정의하는 방법은 Low Level 설정 방법이다. 
Spring MVC관련 Bean을 일일히 등록할 필요 없이 WebMvcConfigurer 인터페이스의 특정 메소드를 구현함으로써 더 편하게 설정할 수 있도록 Spring MVC에서 제공하는 [방법](https://leejongchan.github.io/7-spring-mvc-servlet-enablewebmvc/)이 있다.

