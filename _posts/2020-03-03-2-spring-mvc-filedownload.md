---
layout: post 
title: "핸들러 메소드 14부: 파일 다운로드, @ResponseEntity"
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

**@ResponseEntity**

다음과 같은 정보를 수정할 수 있다.

* 응답 상태 코드
* 응답 헤더
* 응답 본문



**Tika**

* 파일의 종류(MediaType)을 알아내기 위한 라이브러리
* 의존성 등록이 필요하다.
* [http://tika.apache.org/](http://tika.apache.org/)



```java
@Controller
public class FileController {
  
  @Autowired
  private ResourceLoader resourceLoader;
  
  @GetMapping("/file")
  public String fileUploadForm() {
    return "files/index";
  }
  
  @PostMapping("/file")
  public String fileUpload(@RequestParam MultipartFile file) {
    String message = file.getOriginalFileName() + "is uploaded";
    attributes.addFlashAttribute("message", message);
    return "redirect:/file";
  }
  
  @GetMapping("/file/{filename}")
  public ResponseEntity<Resource> fileDownload(@PathVariable String filename) throws IOException{
    Resource resource = resourceLoader.getResource("classpath:" + filename);
    File file = resource.getFile();
    
    Tika tika = new Tika();
    String mediaType = tika.detect(file);
    
    return ResponseEntity.ok()
      			.header(HttpHeaders.CONTENT_DISPOSITION, "attachment; filename=\"" + resource.getFilename() + "\"")
      			.header(HttpHeaders.CONTENT_TYPE, mediaType)
      			.header(HttpHeaders.CONTENT_LENGTH, file.length() = "")
      			.body(resource);
  }
}
```



**참고**

* [https://spring.io/guides/gs/uploading-files/](https://spring.io/guides/gs/uploading-files/)
* [https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Disposition](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Disposition)
* [https://www.baeldung.com/java-file-mime-type](https://www.baeldung.com/java-file-mime-type)
