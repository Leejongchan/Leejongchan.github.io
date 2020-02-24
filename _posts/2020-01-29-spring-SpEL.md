---
layout: post 
title: "SpEL (스프링 Expression Language)"
date: 2020-01-29
excerpt: "백기선 강의 - 스프링 프레임워크 핵심 기술"
tags: 
- Spring
- 백기선 강의
- 스프링 프레임워크 핵심 기술
comments: true 
---

>백기선님의 스프링 프레임워크 핵심 기술 강의 내용을 정리한 내용입니다.
---

[스프링 EL](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#expressions)은 스프링 3.0 부터 지원하며 객체 그래프를 조회하고 조작하는 기능을 제공한다. [Unified EL](https://docs.oracle.com/javaee/5/tutorial/doc/bnahq.html)과 비슷하지만 메소드 호출을 지원하며 문자열 탬플릿 기능도 지원한다.

OGNL, MVEL, JBOss EL 등 자바에서 사용할 수 있는 여러 EL이 있지만, SpEL은 모든 스프링 프로젝트 전반에 걸쳐 사용할 EL로 만들었다.

실제로 다음과 같은 곳에서 SpEL을 사용한다.

* @Value Annotation

* @ConditionalOnExpression Annotation

  * 선택적으로 Bean을 등록하거나 Bean 설정 File을 읽어들이는 Annotation
  * SpEL을 이용하여 선별적으로 Bean을 등록할 수 있다.

* [Spring Security](https://docs.spring.io/spring-security/site/docs/3.0.x/reference/el-access.html)

  * Method Security, @PreAuthorize, @PostAuthorize, @PreFilter, @PostFilter

  * XML 인터셉터 URL 설정

  * ...

  * XML설정의 hasRole, hasIpAddress와 같은 함수들은 StandardEvaluationContext에서 온다. StandardEvaluationContext에서 Bean을 만들어주면 Bean이 제공하는 함수를 사용할 수 있다. 

    ```java
    StandardEvaluationContext context = new StandardEvaluationContext(Bean);
    ```

    이러한 함수들은 preAuthorize, PostAuthorize 등 Annotation을 사용할 수 있다.

* [Spring Data](https://spring.io/blog/2014/07/15/spel-support-in-spring-data-jpa-query-definitions)

  * @Query Annotation

* [Thymeleaf](https://blog.outsider.ne.kr/997)

* ...



\#은 표현식을 사용하는 방법이고 $는 Property를 참고하는 방법이다. 표현식 안에서는 Property를 사용할 수 있지만 Property안에는 표현식을 사용할 수 없다.

```java
@Component
public class Apprunner implements ApplicationRunner {
  
  @Value("#{1 + 1}")
  int value;
  
  @Value("#{'hello ' + 'world'}")
  String greeting;
  
  @Value("#{1 eq 1}")
  boolean trueOrFalse;
  
  @Value("hello")
  String hello;
  
  @Value("${my.value}")
  int myValue;
  
  @Value("#{${my.value} eq 100}")
  boolean isMyValue100;
  
  // Bean에 있는 값을 참조
  @Value("#{sample.data}")
  int sampleData;
  
  @Override
  public void run(ApplicationArguments args) throws Exception {
    System.out.println("===============");
    System.out.println(value); // 2
    System.out.println(greeting); // hello world
    System.out.println(trueOrFalse); // true
    System.out.println(hello); // hello
    System.out.println(isMyValue100); // true
    
    ExpressionParser parser = new SpelExpressionParser();
    Expression expression = parser.parseExpression("2 + 100"); 
    // 이 자체가 Expression이기 때문에 {}안에 들어갈 내용만 넣는다.
    value = expression.getValue(Integer.class); 
    // 이 때 SpEL도 Type을 변경할 때 ConversionService를 사용한다.
    System.out.println(value); // 102
  }
}
```

```
// application.properties
my.value = 100
```

```java
@Component
public class Sample {
  
  private int data = 200;
  
  public int getData() {
    return data;
  }
  
  public void setData(int data) {
    this.data = data;
  }
}
```

위 예제 외에도 다양한 [레퍼런스](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#expressions-language-ref)가 있다.



Spring Expression의 기반을 이해하기 위해서는 ExpressionParser와 StandardEvaluationContext를 이해하는 것이 좋다.

ExpressionParser를 프로그래밍으로 활용할 수 있다.

```java
@Component
public class AppRunner implements ApplicationRunner {
 
  @Override
  public void run(ApplicationArguments args) throws Exception {
    ExpressionParser parser = new SpelExpressionParser();
    Expression expression = parser.parseExpression("2 + 100"); 
    // 이 자체가 Expression이기 때문에 {}안에 들어갈 내용만 넣는다.
    Integer value = expression.getValue(Integer.class); 
    // 이 때 SpEL도 Type을 변경할 때 ConversionService를 사용한다.
    System.out.println(value); // 102
  }
}
```


