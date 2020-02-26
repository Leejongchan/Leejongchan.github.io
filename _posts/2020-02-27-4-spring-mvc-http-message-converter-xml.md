---
layout: post 
title: "HTTP 메세지 컨버터 XML"
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

OXM(Object-XML Mapper) 라이브러리 중에 스프링이 지원하는 의존성 추가

* JacksonXML
* JAXB



스프링 부트를 사용하는 경우 기본적으로 XML 의존성 추가해주지 않는다.



**JAXB 의존성 추가**

```xml
<dependency>
	<groupId>javax.xml.bind</groupId>
  <artifactId>jaxb-api</artifactId>
</dependency>
<dependency>
	<groupId>org.glassfish.jaxb</groupId>
  <artifactId>jaxb-runtime</artifactId>
</dependency>
<dependency>
	<groupId>org.springframwork</groupId>
  <artifactId>spring-oxm</artifactId>
  <version>${spring-framework.version}</version>
</dependency>
```



```java
@XmlRootElement // RootElement를 알려줘야 jaxb가 변환할 수 있다.
@Entity
public class Person {

	@Id @GerneateValue
  private Long id;
  
  private String name;
  
  public String getName() { return name; }
  
  public void setName(String name) { this.name = name; }
  
  public Long getId() { return id; }
  
  public void setId() { this.id = id }
}
```

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
  
  // Marshaller 등록
  @Bean
  public Jaxb2Marshaller jaxb2Marshaller() {
    Jaxb2Marshaller jaxb2Marshaller = new Jaxb2Marshaller();
    jaxb2Marshaller.setPackagesToScan(Person.class.getPackageName());
    return jaxb2Marshaller;
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
  Marshaller marshaller;
  
 	@Test
  public void xmlMessage() throws Exception {
    Person person = new Person();
    person.setId(2019l);
    person.setName("keesun");
    
    StringWriter stringWriter = new StringWriter();
    Result result = new StreamResult();
    marshaller.marshal(person, result);
    String xmlString = stringWriter.toString();
    
    this.mockMvc.perform(get("/jsonMessage")
                .contentType(MediaType.APPLICATION_XML) // 본문에 XML을 담아서 요청한다.
                .accept(MediaType.APPLICATION_XML) // 응답을 XML 받길 원한다.
                .content(xmlString))
      			.andDo(print())
      			.andExpect(status().isOk);
    				.andExpect(xpath("person/name").string("keesun"))
    				.andExpect(xpath("person/id").string("2019"));
  }
}
```



**Xpath 문법**

*  https://www.w3schools.com/xml/xpath_syntax.asp 
*  https://www.freeformatter.com/xpath-tester.html
