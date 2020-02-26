---
layout: post 
title: "스프링 MVC 설정 마무리"
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

가장 기본적인 설정 방법은 DispatcherServlet이 사용하는 모든 Bean들을 직접 등록하는 방법이다.

DispatcherServlet은 ApplicationContext에 등록되어 있는 모든 Bean들을 찾아 사용한다.

* @Configuration을 사용한 자바 설정 파일에 직접 @Bean을 사용해서 등록하기



이렇게 여러가지 Bean들을 등록하는 것은 번거롭다. 이러한 번거로움을 줄이기 위하여 Spring MVC는 Annotation 기반의 웹 MVC 기본 설정(**@EnableWebMvc**)을 제공한다.



**@EnableWebMvc** 자체로도 Annotation 기반의 Controller 작성에 크게 문제 없지만 경우에 따라 커스터마이징 하고 싶은 경우  @EnableWebMvc가 제공하는 Delegation 메카니즘이 있는데 그 메카니즘에 따르면 어떠한 설정이 WebMvcConfigurer라는 Interface를 구현하고 있다면 그 구현체에서 커스터마이징 할 수 있다.

```java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {
  
  @Override
  public void configureViewResolvers(ViewResolverRegistry registry) {
    registry.jsp("/WEB-INF/", ".jsp");
  }
}
```



SpringBoot를 사용하고 있다면 위와 같은 설정파일을 만들지 않더라도 SpringBoot가 제공하는 자동설정으로 인해 다양한 기능이 제공된다.

* 정적 리소스 지원
* JSON 지원으로 JSON HTTP 메세지 컨버터 기능
* Thymeleaf
* 등



SpringBoot도 WebMvcConfigurer라는 Interface를 사용하여 커스터마이징 할 수 있다.

그러나 @EnableWebMvc를 사용하는 순간 SpringBoot가 제공하는 자동설정을 사용할 수 없다.

SpringBoot가 제공하는 자동설정과 추가 설정을 사용하고 싶다면 @EnableWebMvc를 사용하면 안된다.



SpringBoot에서 application.properties에서 spring.mvc라는 prefix를 가지고 설정할 수 있는 것들이 많이 때문에 key-value 설정을 통해 커스터마이징하는 것을 우선적으로 고려하고 불가능한 경우에 WebMvcConfigurer를 이용하고 WebMvcConfigurer로도 원하는 커스터마이징이 불가능 한 경우 직접 Bean으로 등록한다.

**순서**

1. SpringBoot를 사용하는 경우 application.properties 부터 시작
2. WebMvcConfigurer로 시작
3. @Bean으로 MVC 구성 요소 직접 등록



