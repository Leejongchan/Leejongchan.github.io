---
layout: post 
title: "Null-safety"
date: 2020-02-04
excerpt: "백기선 강의 - 스프링 프레임워크 핵심 기술"
tags: 
- Spring
- 백기선 강의
- 스프링 프레임워크 핵심 기술
comments: true 
---

>백기선님의 스프링 프레임워크 핵심 기술 강의 내용을 정리한 내용입니다.
---

툴의 지원을 받아 Compile 타임에 최대한 NullPointException을 미연에 방지하기 위하여 Spring Framework 5에 Null 관련 Annotation이 추가되었다.

1. @NonNull
2. @Nullable
3. @NonNullApi (패키지 레벨 설정)
   * package file에 설정하여 package file 이하의 paramter와 return type에 NonNull을 적용
4. @NonNullFields (패키지 레벨 설정)



```java
@Service
public class EventService {
  
  @NonNull
  // Return Null을 허용하지 않는다.
  public String createEvent(@NonNull String name) { // name이 Null인 것을 허용하지 않는다.
    return "hello" + name;
  }
}
```

```java
@Component
public class AppRunner implements ApplicationRunner {
  
  @Autowired
  EventService eventService;
  
  @Override
  public void run(ApplicationArguments args) throws Exception {
    String message = eventService.createEvent("keesun");
  }
}
```



Preferences - compiler - Configure annotations... 에서 필요한 Annotation을 추가해야 Run전에 Intellij에서 문제를 확인할 수 있다.
