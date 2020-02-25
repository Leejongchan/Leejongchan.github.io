---
layout: post 
title: "스프링 MVC 동작 원리 마무리"
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

**스프링 부트 사용하지 않는 스프링 MVC**

* 서블릿 컨테이너(ex) 톰캣 )에 등록한 웹 어플리케이션(WAR)에 DispatcherServlet을 등록한다.

  * web.xml에 서블릿 등록

  * 또는 WebApplicationInitializer에 자바 코드로 서블릿 등록(스프링 3.1+, 서블릿 3.0+)

    ```java
    public class WebApplication implements WebApplicationInitializer {
        
        @Override
        public void onStartup(ServletContext servletContext) throws ServletException {
            AnnotationConfigWebApplicationContext context = new AnnotationConfigWebApplicationContext();
            context.register(WebConfig.class);
            context.refresh();
            
            DispatcherServlet dispatcherServlet = new DispatcherServlet(context);
            ServletRegistration.Dynamic app = servletContext.addServlet("app", dispatcherServlet);
            add.addMapping("/app/*");
        }
    }
    ```

* 세부 구성 요소는 빈 설정하기 나름



**스프링 부트로 사용하는 스프링 MVC**

* 자바 애플리케이션에 내장 톰캣을 만들고 그 안에 DispatcherServlet을 등록한다.
  * 스프링 부트 자동 설정이 자동으로 해준다.
* 스프링 부트의 주관에 따라 여러 인터페이스 구현체를 빈으로 등록한다.
