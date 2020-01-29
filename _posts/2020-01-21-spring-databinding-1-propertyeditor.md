---
layout: post 
title: "데이터 바인딩 추상화: PropertyEditor"
date: 2020-01-21
excerpt: ""
tags: 
- Spring
- 백기선 강의
- 스프링 프레임워크 핵심 기술
comments: true 
---

>백기선님의 스프링 프레임워크 핵심 기술 강의 내용을 정리한 내용입니다.
---

Property의 값을 Target 객체에 설정하는 것을 데이터 바인딩이라고 한다.

사용자 관점에서 보면 사용자가 입력한 값을 Application Domain 객체에 동적으로 할당하는 기능이다.

사용자가 입력한 값은 주로 문자열인데 객체가 가진 다양한 Property Type(int, long, boolean, domain type 등)에 맞추어 변환하는 기능을 데이터 바인딩이라고 한다.



# DataBinder Interface

[DataBinder](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/validation/DataBinder.html) class는 데이터 바인딩을 위해 Spring에서 제공하는 class이다. 



# PropertyEditor

DataBinder는 Spring 3.0 이전까지 변환 작업으로 PropertyEditor Interface를 사용했다.

PropertyEditor는 Thread-safe하지 않다. 즉 상태 정보를 저장하고 있어 Singleton Bean으로 만들어 사용하면 안된다. 그리고 Object와 String간의 변환만 가능하기 때문에 사용 범위가 제한적이다.



PropertyEditor를 implements하여 직접 Editor를 구현할 수 있지만 PropertyEditor의 구현체인 PropertyEditorSupport를 사용하여 데이터 바인딩용 Class를 만들 수 있다.



```java
public class Event {

    private Integer id;

    private String title;

    public Event(Integer id) {
        this.id = id;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }
  
    @Override
    public String toString() {
        return "Event{" +
                "id=" + id +
                ", title='" + title + '\'' +
                '}';
    }
}
```

```java
@RestController
public class EventController {
  
  @InitBinder
  public void init(WebDataBinder webDataBinder) {
    webDataBinder.registerCustomEditor(Event.class, new EventEditor());
    // Event Class를 처리할 DataBinder 등록
  }
  
  @GetMapping("/event/{event}")
  public String getEvent(@PathVariable Event event) {
    System.out.println(event);
    return event.getId().toString();
  }
}
```

```java
@RunWith(SpringRunner.class)
@WebMvcTest
public class EventControllerTest {
  
  @Autowired
  MockMvc mockMvc;
  
  @Test
  public void getTest() throws Exception {
    mockMvc.perform(get("/event/1"))
    		.andExpect(status().isOk())
            .andExpect(content().string("1"));
  }
}
```

```java
public class EventEditor extends PropertyEditorSupport {
  
  @Override
  public String getAsText() {
    Event event = (Event)getValue();
    return event.getId().toString();
  }
  
  @Override
  public void setAsTest(String text) throws IllegalArgumentException {
    setValue(new Event(Integer.parseInt(text)));
  }
}
```


