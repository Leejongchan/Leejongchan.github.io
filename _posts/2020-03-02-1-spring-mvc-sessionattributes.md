---
layout: post 
title: "핸들러 메소드 8부: @SessionAttributes"
date: 2020-03-02
excerpt: "백기선 강의 - 스프링 웹 MVC"
tags: 
- Spring
- 백기선 강의
- 스프링 웹 MVC
comments: true 
---

>백기선님의 스프링 웹 MVC 강의 내용을 정리한 내용입니다.
---

**@SessionAttributes**

* 모델 정보를 HTTP 세션에 저장해주는 Annotation
* 이 Annotation에 설정한 이름에 해당하는 모델 정보를 자동으로 세션에 넣어준다.
* @ModelAttribute는 세션에 있는 데이터도 바인딩한다.
* 여러 화면(또는 요청)에서 사용해야 하는 객체를 공유할 때 사용한다.



**SessionStatus를 사용해서 세션 처리 완료를 알려 줄 수 있다.**

* 폼 처리 끝나고 세션을 비울 때 사용한다.



**Controller**

```java
@Controller
@SessionAttributes("event")
public class SampleController {
  
  @GetMapping("/events/form")
  public String eventsForm(Model model) {
    Event newEvent = new Event();
    newEvent.setLimit(50);
    model.addAttribute("event", newEvent);
    // httpSession.setAttribute("event", newEvent);
   	return "/events/form";
  }
  
  @PostMapping("/events")
  @ResponseBody
  public String createEvent(@Validated @ModelAttribute Event event, 
							BindingResult bindResult,
                            SessionStatus sessionStstus) {
    if(bindingReuslt.hasError()) {
      return "/events/form";
    }
    
    sessionStatus.setComplete();
    
   	List<Event> eventList = new ArrayList<>();
    eventList.add(event);
    model.addAttribute("eventList", eventList);
    // model.addAttribute(evecntList); 와 같은 코드
    
    return "/events/list";
  }
}
```



**Test**

```java
@RunWith(SpringRunner.class)
@WebMvcTest
public class SampleContollerTest {
  
  @Autowired
  MockMvc mockMvc;
  
  @Test
  public void eventForm() throws Exception {
    mockMvc.perform(get("/events/form"))
      			.andDo(print())
      			.andExpect(view().name("/events/form"))
      			.andExpect(model().attributeExists("event"))
      			.andExpect(request().sessionAttribute("event", notNullValue()))
                .andReturn().getRequest();
    Object event = request().getSession().getAttribute("event");
   	System.out.println(event);
  }
  
  @Test
  public void eventForm() throws Exception {
    ResultActions result = mockMvc.perform(get("/events")
                .param("name", "keesun")
                .param("limit", "-10"))
      			.andDo(print())
      			.andExpect(status().isOk)
      			.andExpect(model().hasErrors());
    ModelAndView mav = result.andReturn().getModelAndView();
    Map<String, Object> model = mav.getModel();
  }
}
```

