---
layout: post 
title: "HTTP 메세지 컨버터"
date: 2020-02-27
excerpt: "백기선 강의 - 스프링 웹 MVC"
tags: 
- Spring
- 백기선 강의
- 스프링 웹 MVC
comments: true 
---

>백기선님의 스프링 웹 MVC 강의 내용을 정리한 내용입니다.
---

# HTTP 메세지 컨버터 JSON

JSON 메세지 컨버터는 의존성에 따라 조건적으로 등록된다. 스프링 부트를 사용하지 않는 경우 사용하고 싶은 JSON 랑비르러리를 의존성으로 추가해야한다.

* Gson
* JacksonJSON
* JacksonJSON 2



**스프링 부트를 사용하는 경우** spring-boot-starter-web을 추가하면 기본적으로 JacksonJSON 2가 의존성에 들어있다. 즉, **JSON용 HTTP 메세지 컨버터가 기본으로 등록되어 있다.**



```java
@RestController
public class SampleController {
  
  @GetMapping("/jsonMessage")
  public Person jsonMessage(@RequestBody Person person) {
    return person;
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
  ObjectMapper objectMapper;
  
 	@Test
  public void jsonMessage() throws Exception {
    Person person = new Person();
    person.setId(2019l);
    person.setName("keesun");
    
    String jsonString = objectMapper.writeValueAsString(person);
    
    this.mockMvc.perform(get("/jsonMessage")
                .contentType(MediaType.APPLICATION_JSON_UTF8) // 본문에 JSON을 담아서 요청한다.
                .accept(MediaType.APPLICATION_JSON_UTF8) // 응답을 JSON 받길 원한다.
                .content(jsonString))
      			.andDo(print())
      			.andExpect(status().isOk)
    			.andExpect(jsonPath("$.id").value(2019))
    			.andExpect(jsonPath("$.name").value("keesun"));
  }
}
```

**JSON Path 문법**
* https://github.com/json-path/JsonPath http://jsonpath.com/
* http://jsonpath.com/
