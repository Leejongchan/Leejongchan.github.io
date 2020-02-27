---
layout: post 
title: "핸들러 메소드 1부: 아규먼트와 리턴 타입"
date: 2020-02-29
excerpt: "백기선 강의 - 스프링 웹 MVC"
tags: 
- Spring
- 백기선 강의
- 스프링 웹 MVC
comments: true 
---

>백기선님의 스프링 웹 MVC 강의 내용을 정리한 내용입니다.
---

**Handler Method ```Argument```**

주로 요청 그 자체 또는 요청에 들어있는 정보를 받아오는데 사용한다.

| Handler Method Argument                                      | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| WebRequest<br />NativeWebRequest<br />ServletRequest(Response)<br />HttpServletRequest(Response) | 요청 또는 응답 자체에 접근 가능한 API                        |
| InputStream<br />Reader<br />OutputStream<br />Writer        | 요청 본문을 읽어오거나, 응답 본문을 쓸 때 사용할 수 있는 API |
| PushBuilder                                                  | 스프링 5, HTTP/2 리소스 푸쉬에 사용                          |
| HttpMethod                                                   | GET, POST 등에 대한 정보                                     |
| Locale<br />TimeZone<br />ZoneId                             | LocaleResolver가 분석한 요청의 Locale 정보                   |
| @PathVariable                                                | URI 템플릿 변수 읽을 때 사용                                 |
| @MatrixVariable                                              | URI 경로 중에 키/값 쌍을 읽어 올 때 사용                     |
| @RequestParam                                                | 서블릿 요청 매개변수 값을 선언한 메소드 아규먼트 타입으로 변환해준다.<br />단순 타입인 경우 이 Annotation을 생략할 수 있다. |
| @RequestHeader                                               | 요청 헤더 값을 선언한 메소드 아규먼트 타입으로 변환해준다.   |

**참고**
 * https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-arguments



**Handler Method ```Return```**

| Handler Method Argument        | 설명                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| @ResponseBody                  | 리턴 값을 HttpMessageConverter를 사용해 응답 본문으로 사용한다. |
| HttpEntity<br />ResponseEntity | 응답 본문 뿐 아니라 헤더 정보까지, 전체 응답을 만들 때 사용한다. |
| String                         | ViewResolver를 사용해서 뷰를 찾을 때 사용할 뷰 이름          |
| View                           | 암묵적인 모델 정보를 랜더링 뷰 인스턴스                      |
| Map<br />Model                 | (RequestToViewNameTranslator를 통해서) 암묵적인 판단한 뷰 랜더링할 떄 사용할 모델 정보 |
| @ModelAttribute                | (RequestToViewNameTranslator를 통해서) 암묵적으로 판단한 뷰 랜더링할 때 사용할 모델 정보에 추가한다.<br />이 Annotation은 생략할 수 있다. |

**참고**
 * [https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-return-types](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-ann-return-types)
