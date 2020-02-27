---
layout: post 
title: "핸들러 메소드 12부: Flash Attributes"
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

RedirectAttributes를 통해 전달하는 데이터는 Query Parameter에 붙을 수 있어야 하므로 전부 문자열로 변환 가능해야한다. 따라서 복합적인 객체를 전달하기는 어렵다.

Flash Attributes를 사용하면 객체를 전달할 수 있다. 객체는 HttpSession에 들어가고 Redirect된 곳에서 데이터가 사용되면 해당 데이터는 Session에서 제거된다. 

HttpSession을 통해 데이터가 전달되므로 URI에 데이터가 노출되지 않는다.



Flash Attributes로 넘긴 데이터는 Redirect된 곳에서 @ModelAttributes로 받을 수 있지만 Model 객체를 이용하여 데이터를 받을 수 있다.



**Controller**

```java
@Controller
@SessionAttributes("event")
public class SampleController {
  
  @GetMapping("/events/form/name")
  public String eventsForm(Model model) {
    Event newEvent = new Event();
    newEvent.setLimit(50);
    model.addAttribute("event", newEvent);
   	return "/events/form/name";
  }
  
  @PostMapping("/events/form/name")
  @ResponseBody
  public String eventsFormNameSubmit(@Validated @ModelAttribute Event event, 
													 BindingResult bindResult) {
    if(bindingReuslt.hasError()) {
      return "/events/form-name"
    }
    return "redirect:/events/form/limit";
  }
  
  @GetMapping("/events/form/limit")
  public String eventFromLimit(@ModelAttribute Event event, Model model) {
    model.addAttribute("event", event);
   	return "/events/form/limit";
  }
  
  @PostMapping("/events/form/limit")
  @ResponseBody
  public String eventsFormLimitSubmit(@Validated @ModelAttribute Event event, 
													 BindingResult bindResult,
                           SessionStatus sessionStatus,
                           RedirectAttributes attributes) {
    if(bindingReuslt.hasError()) {
      return "/events/form-limit"
    }
    
    sessionStatus.setComplete();
    
    // Flash Attributes를 이용한 Attribute 추가
    attributes.addFlashAttributes("newEvent", event());
    
    return "redirect:/events/list";
  }
  
  @GetMapping("events/list")
  public String getEvents(Model model) {
    Event event = new Event();
    event.setName("spring");
    event.setLimit(10);
    
    // Model 객체를 이용하여 데이터 받기
    Event newEvent = (Event) model.asMap().get("newEvent");
    
    List<Event> eventList = new ArrayList<>();
    eventList.add(event);
    eventList.add(newEvent);
    
    model.addAttribute(eventList);
    
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
  public void getEvents() throws Exception {
    Event newEvent = new Event();
    newEvent.setName("Winter is coming.");
    newEvent.setLimit(10000);
		
    mockMvc.perform(get("/events/list")
        .sessionAttr("visitTime", LocalDateTime.now())
        .flashAttr("newEvent", newEvent))
      				.andDo(print())
      				.andExpect(status().isOk);
    					.andExpect(xpath("//p").nodeCount(2));
  }
}
```



**Xpath 참고**

* [https://www.w3schools.com/xml/xpath_syntax.asp](https://www.w3schools.com/xml/xpath_syntax.asp)
* [https://www.freeformatter.com/xpath-tester.html#ad-output](https://www.freeformatter.com/xpath-tester.html#ad-output)

