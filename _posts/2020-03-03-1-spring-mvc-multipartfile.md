---
layout: post 
title: "핸들러 메소드 13부: MultipartFile"
date: 2020-03-03
excerpt: "백기선 강의 - 스프링 웹 MVC"
tags: 
- Spring
- 백기선 강의
- 스프링 웹 MVC
comments: true 
---

>백기선님의 스프링 웹 MVC 강의 내용을 정리한 내용입니다.
---

**MultipartFile**

* 파일 업로드시 사용하는 메소드 아규먼트
* MultipartResolver 빈이 설정 되어 있어야 사용할 수 있다.
  * SpringBoot 자동 설정이 해준다.
* POST multipart/form-data 요청에 들어있는 파일을 참조할 수 있다.
* List<MultipartFile> 아규먼트로 여러 파일을 참조할 수도 있다.



**파일 업로드 관련 스프링 부트 설정**

* MultipartAutoConfiguration
* MultipartProperties


**파일 업로드 폼**

```html
<!-- index.html -->

<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
	<meta charset="UTF-9"/>
  <title>File Upload</title>
</head>
<body>
  
<div th:if="${message}">
  <h2 th:text="${message}"/>
</div>
<form method="POST" entype="multipart/form-data" action="#" th:action="@(/file)">
  File: <input type="file" name="file"/>
  <input type="submit" value="Upload"/>
</form>
</body>
</html>
```



**Controller**

```java
@Controller
public class FileController {
  
  @GetMapping("/file")
  public String fileUploadForm() {
    return "files/index";
  }
  
  @PostMapping("/file")
  public String fileUpload(@RequestParam MultipartFile file) {
    // @RequestParam("file") MultipartFile file 과 같은 코드
    // 파일 업로드 폼에서 file이라는 이름으로 보내므로 이름을 file로 설정
    
    // save
    String message = file.getOriginalFileName() + "is uploaded";
    // message가 세션에 등록되고 redirect된 곳에서 사용 후 세션에서 제거된다.
    attributes.addFlashAttribute("message", message);
    return "redirect:/file";
  }
}
```



**Test**

```java
@RunWith(SpringRunner.class)
@SpringBootTest // SpringBootApplication 기준으로 모든 Bean을 등록하여 Application 전반에 걸친 Test를 하는 경우
@AutoConfigureMockMvc
public class FileControllerTest {
  
  @Autowired
  private MockMvc mockMvc;
  
  @Test
  public void fileUploadTest() throws Exception {
    // Test를 위한 파일 생성
    MockMultipartFile file = new MockMultipartFile("file", "test.txt", "text/plain", "hello file".getBytes());
    
    this.mockMvc.perform(multipart("/file").file(file))
      			.andDo(print())
      			.andExpect(status().is3xxRedirection());
  }
}

```


**참고**
* [https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-multipart-forms](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-multipart-forms)
* [https://spring.io/guides/gs/uploading-files/](https://spring.io/guides/gs/uploading-files/)
