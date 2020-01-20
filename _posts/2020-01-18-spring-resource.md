---
layout: post 
title: "Resource 추상화"
date: 2020-01-18
excerpt: ""
tags: 
- Spring
- 백기선 강의
- 스프링 프레임워크 핵심 기술
comments: true 
---

>백기선님의 스프링 프레임워크 핵심 기술 강의 내용을 정리한 내용입니다.
---


java.net.URL이라는 Class를 org.springframwork.core.io.[Resource](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/Resource.html) Class로 감싸서 실제 Low level에 있는 Resource에 접근하는 기능을 만든 것이다.

추상화 한 이유는 다음과 같다.

1. classpath 기준으로 리소스 읽어오는 기능 부재
   * 다른 방법으로도 리소스를 읽어올 수 있지만 Spring에서 리소스를 읽어오는 방법을 통일 시키기 위하여 classpath 기준으로 리소스를 읽어오도록 했다.
2. ServletContext를 기준으로 상대 경로로 읽어오는 기능 부재
3. 새로운 Handler를 등록하여 특별한 URL 접미사를 만들어 사용할 수 있지만 구현이 복잡하고 편의성 메소드가 부족하다.



# Resource 주요 Method

* getInputStream()
* exists()
* isOpen()
* getDescription()



ApplicationContext를 만들 때 XML파일을 통해 만들 경우 XML  file을 ClassPathXmlApplicationContext를 통해 만들었다. 이 때 .xml file 문자열이 내부적으로 Resource로 변환된다.

```java
var ctx = new ClassPathXmlApplicationContext("test.xml")
```

FileSystemXmlApplicationContext은 File System 경로 기준으로 문자열에 해당하는 Resource를 찾아서 Bean 설정 파일로 사용하는 것이다.

```java
var ctx = new FileSystemXmlApplicationContext("test.xml");
```

ClassPathXmlApplicationContext는 classpath 기준으로 문자열에 해당하는 Resource를 찾는다.



### Resource 구현체

1. UrlResource
   * prefix로 http, https, ftp, file, jar을 지원한다.
2. ClassPathResource
   * classpath를 접두어로 지원한다.
3. FileSystemResource
4. ServletContextResource
   * 웹 어플리케이션 루트에서 상대 경로로 리소스를 찾는다.
   * 가장 많이 사용하는 구현체로 그 이유는 읽어들이는 Resource Type이 ApplicationContext Type에 따라 결정되기 때문이다. ApplicationContext는 대부분의 경우 WebApplicationContext를 사용한다. 따라서, ServletContextResource를 사용하게 된다.
5. ...



### 리소스 읽어오기

Resource의 Type은 Location 문자열과 ApplicationContext의 Type에 따라 결정 된다.

```java
@Component
public class AppRunner implements ApplicationRunner {
  
  @Autowried
  ResourceLoader resourceLoader;
  
  @Override
  public void run(ApplicationArguments args) throws Exception {
    var ctx = new ClassPathXmlApplicationContext("test.xml"); //Resource Type : ClassPathResource
    
    Resource resource = resourceLoader.getRosource("text.txt");
    // ResourceLoader자체가 ApplicationContext이다. 만약 이 ApplicationContext가 ClassPathXmlApplicationContext라면 location은 ClassPathXmlApplicationContext 기준으로 Resource를 읽어온다.
  }
}
```



* ClassPathXmlApplicationContext : ClassPathResource
* FileSystemXmlApplicationContext : FileSystemResource
* WebApplicationContext : ServletContextResource



ApplicationContext의 Type에 상관없이 Resource Type을 강제하려면 접두어를 이용할 수 있다.

```java
var ctx = new ClassPathXmlApplicationContext("classpath:test.xml");
```



백기선님은 접두어를 사용하면 어떠한 경로로 Resource가 오는지 명시적이기 때문에 선호하신다고 한다.

* ClassPathResource
  * classpath:me/whiteship/config.xml
* FileSystemResource
  * file:///some/resource/path/config.xml (root부터 읽는 경우 /// 세개를 사용)





```java
@Component
public class AppRunner implements ApplicationRunner {
  
  @Autowried
  ApplicationContext resourceLoader;
  
  @Override
  public void run(ApplicationArguments args) throws Exception {
    System.out.println(resourceLoader.getClass()); // WebApplicationContext을 상속한 AnnotationConfigServletWebServerApplicationContext
    
    Resource resource = resourceLoader.getResource("classpath:text.txt");
    System.out.println(resource.getClass()); // ClassPathResource
  }
}
```

```java
@Component
public class AppRunner implements ApplicationRunner {
  
  @Autowried
  ApplicationContext resourceLoader;
  
  @Override
  public void run(ApplicationArguments args) throws Exception {
    System.out.println(resourceLoader.getClass());
    
    Resource resource = resourceLoader.getResource("text.txt"); // ServletContextResource
    System.out.println(resource.exists());
    // false : ServletContextResource는 WebApplicationContext root(context path)부터 찾게 되는데 Spring boot의 내장형 tomcat에는 context path가 지정되어 있지 않아 Resource를 찾을 수 없다.
    System.out.println(resource.getDescription());
    System.out.println(resource.getClass());
    System.out.println(Files.readString(Path.of(resource.getURI()))); // 없는 File을 읽으려고 하기 때문에 Error
  }
}
```


