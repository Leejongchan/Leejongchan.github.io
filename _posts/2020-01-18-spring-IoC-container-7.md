---
layout: post 
title: "IoC 컨테이너 7부: MessageSource"
date: 2020-01-18
excerpt: ""
tags: 
- Spring
- 백기선 강의
- 스프링 프레임워크 핵심 기술
comments: true 
---

MessageSource는 국제화(i18n)을 제공하는 기능이다. ApplicationContext는 MessageSource를 상속받고 있기 때문에 ApplicationContext를 사용하면 MessageSource 기능을 사용 할 수 있다.


SpringBoot에서는 이름이 messages로 시작하는 properties를 MessageSource로 읽어주기 때문에 바로 사용가능하다. 

```bash
/resources/messages.properties
greeting= Hello {0}
```

```bash
/resources/messages_ko_KR.properties
greeting= 안녕, {0}
```

```java
@Component
public class AppRunner implements ApplicationRunner {
  
  @Autowired
  MessageSource messageSource;
  // ApplicactionContext messageSource;
  // ApplicationContext가 messageSource 기능을 상속하고 있기 때문에 위와 같이 사용가능 하지만 좋은 코딩 방법은 아니다. 사용하고자하는 Interface type을 사용하여 어떤 의도로 사용하는지를 명확하게 하는 것이 좋다.
  
  @Override
  public void run(ApplicationArguments args) throws Exception {
    System.out.println(messageSource.getClass());
    // Spring boot를 쓰면 ResourceBundleMessageSource가 Bean로 등록 되어있어 이 Bean이 messages resource bundle을 읽게 되어 있다.
    
    System.out.println(messageSource.getMessage("greeting"), new String[]{"keesun"}, Local.KOREA));
    // 안녕, keesun
    System.out.println(messageSource.getMessage("greeting"), new String[]{"keesun"}, Local.getDefault()));
    // Hello keesun
  }
}
```



### Reloading 기능

```java
@SpringBootApplication
public class Demospring51Application {
  
  public static void main(String[] args) {
    SpringApplication.run(Demospring51Application.class);
  }
  
  @Bean
  public MessageSource messageSource() {
    var messageSource = new ReloadableResourceBundleMessageSource();
    messageSource.setBasename("classpath:/messages");
    messageSource.setDefaultEncoding("UTF-8");
    messageSource.setCacheSeconds(3);
    // Resource를 Cache하는 시간을 3초로 설정하여 3초마다 다시 읽도록 함
    return messageSource;
  }
}
```

```java
@Component
public class AppRunner implements ApplicationRunner {
  
  @Autowired
  MessageSource messageSource;
  // ApplicactionContext messageSource;
  // ApplicationContext가 messageSource 기능을 상속하고 있기 때문에 위와 같이 사용가능 하지만 좋은 코딩 방법은 아니다. 사용하고자하는 Interface type을 사용하여 어떤 의도로 사용하는지를 명확하게 하는 것이 좋다.
  
  @Override
  public void run(ApplicationArguments args) throws Exception {
    while(true) {
          System.out.println(messageSource.getMessage("greeting"), new String[]{"keesun"}, Local.KOREA));
    System.out.println(messageSource.getMessage("greeting"), new String[]{"keesun"}, Local.getDefault()));
      Thread.sleep(1000); // 1초마다 출력
    }
  }
}
```

Reloadable한 ResourceBundleMessageSource이기 때문에 운영중에 메세지를 바꿀 수 있다.

message file의 내용을 바꾼 후 Build하면 운영중에 출력되는 메세지 값이 변경된다.
