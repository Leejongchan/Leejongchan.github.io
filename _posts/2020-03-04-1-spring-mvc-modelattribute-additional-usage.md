---
layout: post 
title: "모델: @ModelAttribute 또 다른 사용법"
date: 2020-03-04
excerpt: "백기선 강의 - 스프링 웹 MVC"
tags: 
- Spring
- 백기선 강의
- 스프링 웹 MVC
comments: true 
---

>백기선님의 스프링 웹 MVC 강의 내용을 정리한 내용입니다.
---

**@RequestMapping을 사용한 핸들러 메소드의 아규먼트에 사용하기**



**@Controller 또는 @ControllerAdvice(이 후 Post에서 다룰 예정)를 사용한 클래스에서 모델 정보를 초기화할 때 사용**

Model이나 ModelMap이라는 Method Argument를 사용해서 Model 정보를 담아줄 수 있고 View에서 참조하여 Rendering할 수 있다.

모든 Handler에서 공통적으로 참조해야하는 Model 정보가 있는 경우 매번 모든 Handler에서 Model 정보를 Model에 addAttribute하는 것은 귀찮으므로 다음과 같이 @ModelAttribute를 사용할 수 있다.

```java
@Controller 
public class EventController {
  
  @ModelAttribute
  public void categories(Model model) {
    model.addAttribute("categories", list.of("study", "seminar", "hobby", "social"));
  }
}
```

```java
@Controller 
public class EventController {
  
  @ModelAttribute("categories")
  public List<String> categories(Model model) {
    return list.of("study", "seminar", "hobby", "social");
  }
}
```



**@RequestMapping과 같이 사용하면 해당 메소드에서 리턴하는 객체를 모델에 넣어 준다.**

RequestToViewTranslator가 요청 URI과 일치하는 View를 Return 한다.

```java
@Controller 
public class EventController {
  
  @ModelAttribute("categories")
  public List<String> categories(Model model) {
    return list.of("study", "seminar", "hobby", "social");
  }
  
  @GetMapping("/events/form/name")
  @ModelAttribute // @ModelAttribute Annotation 생략 가능
  public Event eventsFormName() {
    return new Event();
  }
}
```

