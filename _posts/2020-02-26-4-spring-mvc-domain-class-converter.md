---
layout: post 
title: "도메인 클래스 컨버"
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

**도메인 클래스 컨버터**

* 스프링 데이터 JPA가 제공하는 Repository를 사용해서 ID에 해당하는 엔티티를 읽어온다.



**의존성 설정**

```xml
<dependency>
	<groupId>org.springframwork.boot</groupId>
  <artifactId>spring-boot-start-data-jpa</artifactId>
</dependency>
<dependency>
	<groupId>com.h2database</groupId>
  <artifactId>h2</artifactId>
</dependency>
```



**엔티티 맵핑**

```java
@Entity
public class Person {
  
  @Id @GenerateValue
  // @GenerateValue : Id 값 자동 생성을 위한 Annotation
  private Long id;
  
  private String name;
  
  public String getName() {
    return name;
  }
  
  public void setName(String name) {
    this.name = name;
  }
  
  public Long getId() {
    return id;
  }
  
  public void setId(Long id) {
    this.id = id;
  }
}
```



**Repository 추가**

```java
public interface PersonRepository extends JpaRepository<Person, Long> {
}
```



```java
@RestController
public class SampleController {
  
  @GetMapping("/hello")
  public String hello(@RequestParam("id") Person person) {
    return "hello " + person.getName();
  }
}
```

```java
@RunWith(SpringRunner.class)
@WebMvcTest
public class SampleControllerTest {
  
  @Autowired
  MockMvc mockMvc;
  
  @Autowired
  PersonRepository personRepository;
  
  @Test
  public void hello() {
    // Test data
    Person person = new Person();
    person.setName("keesun");
    Person savedPerson = personRepository.save(person);
    
    this.mockMvc.performI(get("/hello"))
                   .param("id", savedPerson.getId().toString()))
             .andDo(print())
             .andExpect(content().string("hello keesun"));
  }
}
```
