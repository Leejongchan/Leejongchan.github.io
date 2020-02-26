---
layout: post 
title: "핸들러 인터셉터"
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

**HandlerInterceptor**

* 핸들러 맵핑에 설정할 수 있는 인터셉터
* 핸들러를 실행하기 전(랜더링 전), 완료(랜더링까지 끝난 이후) 시점에 부가 작업을 하고 싶은 경우에 사용할 수 있다.
* 여러 핸들러에서 반복적으로 사용하는 코드를 줄이고 싶을 때 사용할 수 있다.
  * 로깅, 인증 체크, Locale 변경 등



**실행 순서**

1. preHandle
2. 요청 처리
3. postHandler
4. 뷰 랜더링
5. afterCompletion



**핸들러 인터셉터가 여러개일 경우 실행 순서**

1. preHandle 1
2. preHandle 2
3. 요청 처리
4. post Handle 2
5. post Handle 1
6. 뷰 랜더링
7. afterCompletion 2
8. after Completion 1



**boolean preHandle(request, response, handler)**

* 핸들러 실행하기 전에 호출 된다.
* 핸들러에 대한 정보를 사용할 수 있기 때문에 서블릿 필터에 비해 보다 세밀한 로직을 구현할 수 있다.
* 리턴값으로 계속 다음 인터셉터 또는 핸들러로 요청, 응답을 전달할지(true) 응답 처리가 이곳에서 끝났는지(false)를 전달한다.



**void postHandle(request, response, modelAndView)**

* 핸들러 실행이 끝나고 아직 뷰를 랜더링 하기 이전에 호출 된다.
* 뷰에 전달할 추가적이거나 여러 핸들러에 공통적인 모델 정보를 담는데 사용할 수도 있다.
* 인터셉터 역순으로 호출된다.
* 비동기적인 요청 처리 시에는 호출되지 않는다.



**void afterCompletion(request, response, handler, ex)**

* 요청 처리가 완전히 끝난 뒤(뷰 랜더링 끝난 뒤)에 호출 된다.
* preHandler에서 true를 리턴한 경우에만 호출 된다.
* 인터셉터 역순으로 호출된다.
* 비동기적인 요청 처리 시에는 호출되지 않는다.



**vs 서블릿 필터**

* 서블릿보다 구체적인 처리가 가능하다.
* 일반적인 기능을 구현할 때는 서블릿 필터, Spring MVC에 특화되어 있는 정보를 참고해야하는 경우에는 HandlerInterceptor로 구현하는 것이 좋다.
  * 예를 들어 XSS를 차단하는 기능은 서블릿 필터로 구현한다.

