---
layout: post 
title: "스프링 MVC 소개"
date: 2020-02-09
excerpt: "백기선 강의 - 스프링 웹 MVC"
tags: 
- Spring
- 백기선 강의
- 스프링 웹 MVC
comments: true 
---

>백기선님의 스프링 웹 MVC 강의 내용을 정리한 내용입니다.
---


스프링 MVC는 Servlet기반의 Application에서 MVC 패턴을 사용하기 쉽게끔 도와주는 프레임워크이다.



1. M (Model)
   * 도메인 객체 또는 DTO로 화면에 전달할 또는 화면에서 전달 받은 데이터를 담고 있는 객체
   * 평범한 자바 객체 POJO
2. V (View)
   * 데이터를 보여주는 역할. 다양한 형태로 보여줄 수 있다. HTML, JSON, XML 등
   * HTML. JSP, [thymeleaf](https://www.thymeleaf.org/doc/tutorials/2.1/usingthymeleaf.html) 등
3. C (Controller)
   * 사용자 입력을 받아 모델 객체의 데이터를 변경하거나, 모델 객체를 뷰에 전달하는 역할
     * 입력값 검증
     * 입력 받은 데이터로 모델 객체 변경
     * 변경된 모델 객체를 뷰에 전달
   *  스프링 @MVC
     
     

```java
@Controller
public class EventController {
  
  @Autowired
  EventService eventService;
  
  // events라는 GET 요청이 들어오는 경우
  // Spring 4.3부터 @GetMapping("events")으로도 GET요청을 처리할 수 있다.
  @RequestMapping(value = "/events", method = RequestMethod.GET)
  pbulic String events(Model model) {
    // Model은 Java의 Map이라고 생각하면 된다.
    
    model.addAttribute("events", eventService.getEvent());
    
    // Spring에서 동적인 View는 resources/templates에서 찾는다.
    return "events";
  }
}
```

```java
// Model은 전달하고싶은 객체로 Application에서 다루는 POJO 객체이다.

// lombok annotation을 사용하여 Compile 시점에 자동으로 필요한 것들을 추가되도록 한다.
@Getter @Setter
@Builder @NoArgsConstructor @AllArgsConstructor
public class Event {
  
  private String name;
  
  private int limitOfEnrollment;
  
  private LocalDateTime startDateTime;
  
  private LocalDateTime endDateTime;
}
```

```java
@Service
public class EventService {
  
  public List<Event> getEvents() {
    Event event1 = Event.builder()
      			.name("스프링 웹 MVC 스터디 1차")
      			.limitOfEnrollment(5)
      			.startDateTime(LocalDateTime.of(2019,1,10,10,0))
      			.startDateTime(LocalDateTime.of(2019,1,10,17,0))
      			.build();
      
    Event event2 = Event.builder()
      			.name("스프링 웹 MVC 스터디 1차")
      			.limitOfEnrollment(5)
      			.startDateTime(LocalDateTime.of(2019,1,17,10,0))
      			.startDateTime(LocalDateTime.of(2019,1,17,12,0))
      			.build();
    
    return list.of(event1, event2);
  }
}
```

```html
<!-- thymeleaf -->
<!DOCTYPE html>
<html lang="en" xmlns:th="https://www.thymeleaf.org"> <!-- Timeleaf를 쓰려면 namespace를 정의해야 한다. -->
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>
	<h1>
    이벤트 목록
  </h1>  
  <table>
    <tr>
    	<th>이름</th>
      <th>참가 인원</th>
      <th>시작</th>
      <th>종료</th>
    </tr>
    <tr th:each="event: ${events}">
    	<td th:text="${event.name}">이벤트 이름</td>
    	<td th:text="${event.limitOfEnrollment}">100</td>
    	<td th:text="${event.startDateTime}">2020년 1월 10일 오전 10시</td>
    	<td th:text="${event.endDateTime}">2020년 1월 10일 오전 12시</td>
    </tr>
  </table>
</body>
</html>
```



# MVC 패턴의 장점

1. 동시 다발적(Simultaneous) 개발
   * 백엔드 개발자와 프론트엔드 개발자가 독립적으로 개발을 진행할 수 있다.
2. 높은 결합도
   * 논리적으로 관련있는 기능을 하나의 컨트롤러로 묶을 수 있다.
   * 특정 모델과 관련있는 뷰를 그룹화 할 수 있다. (Directory를 활용하여)
3. 낮은 의존도
   * 뷰, 모델, 컨트롤러는 각각 독립적이다.
4. 개발 용이성
   * 책임이 구분되어 있어 코드 수정하는 것이 편하다.
   * 한 모델에 대한 여러 형태의 뷰를 가질 수 있다.





# MVC 패턴의 단점

1. 코드 네비게이션 복잡함
2. 코드 일관성 유지에 노력이 필요함
3. 높은 학습 곡선


**참고**  
- [빌더 패턴(Builder Pattern)](https://asfirstalways.tistory.com/350)
