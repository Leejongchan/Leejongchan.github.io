---
layout: post 
title: "스프링 MVC 핵심 기술"
date: 2020-02-28
excerpt: "백기선 강의 - 스프링 웹 MVC"
tags: 
- Spring
- 백기선 강의
- 스프링 웹 MVC
comments: true 
---

>백기선님의 스프링 웹 MVC 강의 내용을 정리한 내용입니다.
---

DispatcherServlet이 요청을 받아서 처리할 Handler를 찾고 Handler를 실행해서 요청을 처리한다.

이 일련의 과정에서 어떻게 DispatcherServlet을 잘 활용할 수 있을지, Annotation 기반의 스프링 MVC에서 어떻게 Annotation기반으로 요청을 특정 Handler 매핑할 수 있는지, 매핑된 Handler를 어떻게 만들 수 있는지 살펴보도록 한다. 그 다음에 Model과 View를 다루는 방법을 살펴보도록 한다.



**Annotation 기반의 스프링 MVC**에서 알아 볼 것들

* 요청 매핑하기
* 핸들러 메소드
* 모델과 뷰
* 데이터 바인더
* 예외 처리
* 글로벌 컨트롤러



**사용할 기술**

* 스프링 부트
* 스프링 프레임워크 웹 MVC
* 타임리프



**학습할 Annotation**

* @RequestMapping
  * @GetMapping, @PostMapping, @PutMapping
* @ModelAttribute
* @RequestParam, @RequestHeader
* @PathVariable, @MatrixVariable
* @SessionAttribute, @RequestAttribute, @CookieValue
* @Valid
* @RequestBody, @ResponseBody
* @ExceptionHandler
* @ControllerAdvice
