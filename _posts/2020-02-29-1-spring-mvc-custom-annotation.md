---
layout: post 
title: "HTTP 요청 맵핑하기 6부: 커스텀 애노테이션"
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

@RequestMapping Annotation을 메타 Annotation으로 사용할 수 있다.

* @GetMapping 같은 커스텀한 Annotation을 만들 수 있다.



**메타(Meta) Annotation**

* Annotation에 사용할 수 있는 Annotation
* 스프링이 제공하는 대부분의 Annotation은 메타 Annotation으로 사용할 수 있다.



**조합(Composed) Annotation**

* 한개 혹은 여러 메타 Annotation을 조합해서 만든 Annotation
* 코드를 간결하게 줄일 수 있다.
* 보다 구체적인 의미를 부여할 수 있다.



**@Retension**

해당 Annotation 정보를 언제까지 유지할 것인지 설정

1. Source

   * 소스 코드까지만 유지. 즉, 컴파일 하면 해당 Annotation 정보가 사라진다.

   * 주석

2. Class

   * 컴파일 한 .class 파일에도 유지. 즉 런타임 시, 클래스를 메모리로 읽어오면 해당 정보는 사라진다.

3. Runtime

   * 클래스를 메모리에 읽어왔을 때까지 유지한다. 코드에서 이 정보를 바탕으로 특정 로직을 실행할 수 있다.



**@Target**

해당 Annotation을 어디에 사용할 수 있는지 결정한다.



**@Documented**

해당 Annotation을 사용한 코드의 문서에 그 Annotation에 대한 정보를 표기할지 결정한다.



```java
@Documented // Annotation을 사용하는 Java Doc에 이 Annotation도 문서에 남도록 하기 위해 설정
@Target(ElementType.METHOD) // Annotation이 사용할 곳
@Retention(RetentionPolicy.RUNTIME) 
// 해당 Annotation을 언제까지 유지할지 설정 (Java의 Annotation 지식)
// RetentionPolicy.CLASS의 경우 컴파일 이후에 클래스 파일에 유지하고 클래스 파일을 로딩하는 순간에 Annotation 정보가 사라진다. 하지만, DispatcherServlet이 동작할 때 Annotation 정보를 참고해야 하므로(Runtime시 결정하므로) RetentionPolicy.RUNTIME으로 설정
@RequestMapping(method = RequestMethod.GET, value = "/hello")
public @interface GetHelloMapping {
  
}
```

```java
@COntroller
public class SampleController {
  
  @GetHelloMapping
  @ResponseBody
  public String hello() {
    return "hello";
  }
}
```

```java
@RunWith(SpringRunner.class)
@WebMvcTest
public class SampleControllerTest {
  
  @Autowired
  MockMvc mockMvc;
  
 	@Test
  public void helloStatic() throws Exception {
    this.mockMvc.perform(get("/hello"))
      				.andDo(print())
      				.andExpect(status().isOk());
  }
}
```

