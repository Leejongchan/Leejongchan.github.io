---
layout: post 
title: "WebMvcConfigurer 설정 1부: Formatter 설정"
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

```java
@RestController
public class SampleController {
  
  @GetMapping("/hello")
  public String hello(@RequestParam("name") Person person) {
    return "hello " + person.getName();
  }
}
```

```java
public class Person {
  
  private String name;
  
  public String getName() {
    return name;
  }
  
  public void setName(String name) {
    this.name = name;
  }
}
```

```java
public class PersonFormatter implements Formatter<Person> {
  
  @Override
  public Person parse(String text, Locale locale) throws ParseException {
    Person person = new Person();
    person.setName(text);
    return person;
  }

  @Override
  public String print(Person object, Locale locale) {
    return object.toString();
  }
}
```



**Formatter 등록 방법**

1. Spring MVC

   ```java
   @Configuration
   public class WebConfig implements WebMvcConfiguerer {
     
     @Override
     public void addFormatters(FormatterRegistry registry) {
       registry.addFormatter(new PersonFormatter());
     }
   }
   ```

2. SpringBoot

   * Formatter가 Bean으로 등록되어 있다면 SpringBoot가 Bean으로 등록한다.

   ```java
   @Component
   public class PersonFormatter implements Formatter<Person> {
     
     @Override
     public Person parse(String text, Locale locale) throws ParseException {
         Person person = new Person();
         person.setName(text);
         return person;
     }
   
     @Override
     public String print(Person object, Locale locale) {
         return object.toString();
     }
   }
   ```



Ctrl+Shift+T 커맨드를 통해 Test 코드 작성을 위한 Test파일을 생성할 수 있다.

```java
@RunWith(SpringRunner.class)
@WebMvcTest 
// Web과 관련 Bean들만 등록
// Formatter는 Web과 관련된 Bean이 아니므로 Test에서 문제 생길 수 있다.
// 해결 방법으로 @SpringBootTest와 @AutoConfigureMockMvc를 @WebMvcTest 대신에 이용한다. 
// @SpringBootTest을 사용할 경우 MockMvc을 사용할 수 없기 때문에 @AutoConfigureMockMvc 또한 이용한다.
public class SampleControllerTest {
  
  @Autowired
  MockMvc mockMvc;
  
  @Test
  public void hello() {
    this.mockMvc.performI(get("/hello/keesun"))
      					.param("name", "keesun")
      			.andDo(print()) // 요청과 응답을 출력
      			.andExpect(content().string("hello keesun"));
  }
}
```


