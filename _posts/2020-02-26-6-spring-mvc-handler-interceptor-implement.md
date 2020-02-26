---
layout: post 
title: "핸들러 인터셉터 구현"
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

**Interceptor 구현**

```java
public class GreetingInterceptor implements HandlerInterceptor {
    
  @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle 1");
        return true; 
    }
  
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle 1"); 
    }
  
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion 1"); 
    }
}
```

```java
public class AnotherInterceptor implements HandlerInterceptor {
    
  @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle 2");
        return true; 
    }
  
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle 2"); 
    }
  
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion 2"); 
    }
}
```



**WebConfig에 Interceptor 추가**

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
  
  @Override
  public void addInterceptor(InterceptorRegistry registry) {
    registry.addInterceptor(new GreetingInterceptor()).order(0);
    registry.addInterceptor(new AnotherInterceptor())
      			.addPathPatterns("/hi") // 특정한 패턴에만 Interceptor를 적용할 수 있다.
      			.order(-1);
    // Order는 숫자가 낮을수록 우선순위가 높다.
  }
}
```



**Controller**

```java
@RestController
public class SampleController {
  
  @GetMapping("/hello")
  public String hello(@RequestParam("id") Person person) {
    return "hello " + person.getName();
  }
}
```



**Test Code**

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

