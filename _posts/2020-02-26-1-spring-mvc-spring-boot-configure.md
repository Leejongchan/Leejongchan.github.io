---
layout: post 
title: "스프링 부트의 스프링 MVC 설정"
date: 2020-02-26
excerpt: "백기선 강의 - 스프링 웹 MVC"
tags: 
- Spring
- 백기선 강의
- 스프링 웹 MVC
comments: true 
---

>백기선님의 스프링 웹 MVC 강의 내용을 정리한 내용입니다.
---

![springboot](../assets/img/2020-02-26-spring-mvc-spring-boot-configure/springboot.png)

**스프링 부트의 주관이 적용된 자동 설정이 동작한다.**

* JSP 보다 Thymeleaf 선호
* JSON 지원
* 정적 리소스 지원 (웰컴 페이지, 파비콘 등)



**스프링 부트에서의 HandlerMapping**

1. SimpleUrlHandlerMapping

   * 파비콘 처리를 위한 핸들러

2. RequestMappingHandlerMapping

   * Annotation 기반의 MVC 처리를 위한 핸들러

3. BeanNameUrlHandlerMapping

   * 지금은 딱히 쓰지 않지만 사용 가능하다. 

4. SimpleUrlHandlerMapping

   * 스프링 부트가 지원하는 정적 리소스 지원 기능이 이 핸들러 때문에 가능하다.

   * 디렉토리 안에 들어있는 정적 리소스에 리소스 핸들러 매핑을 적용하면 캐시관련 정보가 응답헤더에 추가된다. 따라서 리소스를 효율적으로 사용할 수 있다. 예를 들어 리소스가 변경되지 않았으면 304(Not Modified)라는 응답을 보내 브라우저가 캐싱하고 있는 리소스를 그대로 사용하도록 한다.

5. WelcomePageHandlerMapping

   * Index Page 등을 제공해주는 리소스관련 핸들러 매핑이다.



**스프링 부트에서의 HandlerAdapter**

1. RequestMappingHandlerAdapter
   1. Annotation 기반의 MVC 처리를 위한 핸들러를 처리
2. HttpRequestHandlerAdapter
3. SimpleControllerHandlerAdapter



**스프링 부트에서의 ViewResolvers**

1. ContentNegotiatingViewResolver
   * 자기가 직접 View 이름에 해당하는 View를 찾는 것이 아니라 일을 다른 ViewResolver에게 위임한다.
   * 요청을 분석하여 아래 네개의 ViewResolver 중에 가장 적절한 ViewResolver를 선택한다.
2. BeanNameViewResolver
3. ThymeleafViewResolver
   * SpringBoot 의존성에 Thymeleaf를 추가했기 때문에 사용할 수 있다.
4. ViewResolverComposite
5. InternalResourceViewResolver





실질적으로 DispatcherServlet이 사용하고 있는 HandlerMapping, HandlerAdapter, ViewResolvers 등은 (External Libraries 안의) spring-boot-autoconfigure이라는 jar파일을 열어보면 META-INF spring.factories파일이 있다. 이 안에 EnableAutoConfiguration에 해당하는 모든 자동 설정 파일이 조건에 따라 적용된다. 이 설정 중 DispatcherServlet 설정(DispatcherServletAutoConfiguration)을 찾아 볼 수 있다. WebMvcAutoConfiguration 설정은 Spring MVC 자동 설정이다. 

각각의 설정파일에 들어가 스프링 부트가 지원하는 설정한 값들을 확인할 수 있다.



**스프링 MVC 커스터마이징**

1. application.properties
   * 스프링 부트의 자동설정 안에서 properties들(ResourceProperties)이 사용되는데 properties들은 @ConfigurationProperties를 사용하여 전부 prefix가 spring.resources로 설정되어 있다.
     따라서 값들을 application.properties에서 설정하고 읽어올 수 있다.
   * 가장 많이 손을 안대고 커스터마이징 할 수 있는 방법
2. **@Configuration + Implements WebMvcConfigurer**
   * 스프링 부트의 스프링 MVC 자동설정 + 추가 설정
3. @Configuration + @EnableWebMvc + Implements WebMvcConfigurer
   * 스프링 부트의 스프링 MVC 자동설정을 사용하지 않음



