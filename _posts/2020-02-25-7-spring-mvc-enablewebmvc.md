---
layout: post 
title: "@EnableWebMvc, WebMvcConfigurer"
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

**@EnableWebMvc**

Annotation 기반 Spring MVC를 사용할 때 편리한 웹 MVC 기본 설정

```java
@Configuration
@EnableWebMvc
public class WebConfig {
    
}
```



@EnableWebMvc는 DelegatingWebMvcConfiguration라는 것을 읽어오는데(import) 어딘가에 위임을 해서 읽어오는 Deligation구조로 되어 확장성을 높이도록 되어있다. 따라서 손쉽게 기존에 있는 Bean( ex) HandlerMapping, HandlerAdapter)에 Interceptor나 MessageConverter 등을 추가할 수 있다.

처음부터 HandlerMapping, HandlerAdapter 등을 등록하는 것이 아니라 WebMvcConfigurationSupport 클래스가 제공하는 HandlerMapping, HandlerAdapter를 조금만 수정하는식으로 Bean 설정을 할 수 있다.



**WebMvcConfigurer**

사용자가 원하는대로 확장하는 지점은 WebMvcConfigurer Interface를 통해 지원한다.

예를 들어 @EnableWebMvc가 등록해주는 ViewResolver를 커스터마이징할 수 있다.

```java
@Configuration
@ComponentScan
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {
    
    @Override
    public void configureViewResolvers(ViewResolverRegistry registry) {
        registry.jsp("/WEB_INF/", ".jsp");
    }
}
```

```java
public class WebApplication implements WebApplicationInitializer {
    
    @Override
    public void onStartup(ServletContext servletContext) throws ServletException {
        AnnotationConfigWebApplicationContext context = new AnnotationConfigWebApplicationContext();
        context.setServletContext(servletContext); 
        // servletContext를 설정하지 않으면 EnableWebMvc가 불러오는 DelegatingWebMvcConfiguration이 ServletContext를 미참조할 수가 있다.
        context.register(WebConfig.class);
        context.refresh();
        
        DispatcherServlet dispatcherServlet = new DispatcherServlet(context);
        ServletRegistration.Dynamic app = servletContext.addServlet("app", dispatcherServlet);
        add.addMapping("/app/*");
    }
}
```

