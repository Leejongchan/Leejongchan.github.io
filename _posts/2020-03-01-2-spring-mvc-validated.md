---
layout: post 
title: "핸들러 메소드 6부: @Validated"
date: 2020-03-01
excerpt: "백기선 강의 - 스프링 웹 MVC"
tags: 
- Spring
- 백기선 강의
- 스프링 웹 MVC
comments: true 
---

>백기선님의 스프링 웹 MVC 강의 내용을 정리한 내용입니다.
---

스프링 MVC 핸들러 메소드 아규먼트에 사용할 수 있으며 Validation Group이라는 힌트를 사용할 수 있다.

@Valid Annotation은 그룹을 지정할 방법이 없다.

Group 없이 @Validated Annotation만 선언한 경우 @Valid와 같은 기능을 수행한다.

```java
@Controller
public class SampleController {
  
  @PostMapping("/events")
  @ResponseBody
  public Event postEvent(@Validated @ModelAttribute Event event, BindingResult bindResult) {
    if(bindingReuslt.hasError()) {
      bindingResult.getAllErrors().forEach(c -> {
        System.out.println(c.toString());
      });
    }
    return event;
  }
}
```



그룹을 지정하여 다음과 같이 @Validated를 사용할 수 있다.

```java
@Controller
public class SampleController {
  
  @PostMapping("/events")
  @ResponseBody
  public Event postEvent(@Validated(Event.ValidateName.class) @ModelAttribute Event event, BindingResult bindResult) {
    if(bindingReuslt.hasError()) {
      bindingResult.getAllErrors().forEach(c -> {
        System.out.println(c.toString());
      });
    }
    return event;
  }
}
```

```java
public class Event {
  
  interface ValidateLimit {}
  interface ValidateName {}
  
  Integer id;
  
  @NoBlank(groups = ValidateName.class)
  String name;
  
  @Min(value = 0, groups = ValidateLimit.class)
  Integer limit;
  
  public Integer getId() {
    return id;
  }
  
  public void setId(Integer id) {
    this.id = id;
  }
  
  public String getName() {
    return name;
  }
  
  public void setName(String name) {
    this.name = name;
  }
  
  public Integer getLimit() {
    return limit;
  }
  
  public void setLimit(Integer limit) {
    this.limit = limit;
  }
}
```

