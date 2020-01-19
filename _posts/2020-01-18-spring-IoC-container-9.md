---
layout: post 
title: "IoC 컨테이너 9부: ResourceLoader"
date: 2020-01-18
excerpt: ""
tags: 
- Spring
- 백기선 강의
- 스프링 프레임워크 핵심 기술
comments: true 
---

Resource를 Loading해주는 Interface를 ApplicationContext가 상속받아 구현하고 있다.

Resource는 Resource Class의 getResource(java.lang.String location) method를 이용하여 읽어올 수 있다.

```java
@Component
public class AppRunner implements ApplicationRunner {
  
  @Autowired
  ResourceLoader resourceLoader;
  
  @Override
  public void run(ApplicationArguments args) throws Exception {
    Resource resource = resourceLoader.getResource("classpath:text.txt");
    System.out.println(resource.exists());
    System.out.println(resource.getDescription()); 
    // File의 Full 경로 출력
    System.out.println(Files.readString(Path.of(resource.getURI())));
    // File의 내용을 출력
    // readString은 JAVA 11 Version부터 사용할 수 있는 Method
  }
}
```

