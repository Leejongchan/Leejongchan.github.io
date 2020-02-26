---
layout: post 
title: "리소스 핸들러"
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

**리소스 핸들러**

이미지, 자바스크립트, CSS 그리고 HTML 파일과 같은 정적인 리소스를 처리하는 핸들러이다.



**디폴트(Default) 서블릿**

[서블릿 컨테이너가 기본으로 제공하는 서블릿](https://tomcat.apache.org/tomcat-9.0-doc/default-servlet.html)으로 정적인 리소스를 처리할 때 사용한다.

Spring은 디폴트 서블릿에 요청을 위임하여 정적인 리소스를 처리한다.



정적 리소스 핸들러가 요청을 먼저 가로채면 우리가 만든 핸들러가 먼저 찾아지는 것이 아니라 정적 리소스가 먼저 찾아지므로 우리가 만든 핸들러 우선 순위가 높도록 정적 리소스 핸들러는 가장 낮은 우선 순위로 등록된다.



스프링 부트는 기본 정적 리소스 핸들러와 캐싱을 제공한다.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>Title</title>
  </head>
  <body>
    <h1>
      hello index
    </h1>
  </body>
</html>
```

```java
@RunWith(SpringRunner.class)
@WebMvcTest
public class SampleControllerTest {
  
  @Autowired
  MockMvc mockMvc;
  
 	@Test
  public void helloStatic() throws Exception {
    this.mockMvc.perform(get("/index.html"))
      				.andDo(print())
      				.andExpect(status().isOk())
      				.andExpect(content().string(Matchers.containsString("hello index.html")));
  }
}
```



**리소스 핸들러 설정**

* 어떤 요청 패턴을 지원할 것인지 설정
* 어디서 리소스를 찾을 것인지 설정
* 캐싱 설정
* ResourceResolver
  * 요청에 해당하는 리소스를 찾는 방법
    * 캐싱, 인코딩(gzip, brotli), WebJar 등에 해당하는 요청
* ResourceTransformer
  * 응답으로 보낼 리소스를 수정하는 전략
    * 캐싱, CSS 링크를 수정, HTML5 AppCache Manifest 정보를 응답에 추가
* [ResourceResolver와 ResourceTransformer 참고](https://www.slideshare.net/rstoya05/resource-handling-spring-framework-41)

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
  
  @Override
  public void addResourceHandlers(ResourceHandlerRegistry registry) {
    registry.addResourceHandler("/mobile/**")
      			.addResourceLocations("classpath:/mobile/")
      			.setCacheControl(CacheControl.maxAge(10, TimeUnit.MINUTES));
    // mobile로 오는 URL PATH를 resources 폴더 안의 mobile 폴더로 경로 설정
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
    this.mockMvc.perform(get("/mobile/index.html"))
      				.andDo(print())
      				.andExpect(status().isOk())
      				.andExpect(content().string(Matchers.containsString("hello Mobile")))
    					.andExpect(header().exists(HttpHeaders.CACHE_CONTROL));
  }
}
```



