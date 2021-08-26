# hello-spring
**2021.08**
**인프런 강의: 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술**


**스프링부트 2.4, spring, java, H2, gradle, intellij, JDK 11(java11)**

## 수강평
스프링 책을 읽고 난 후 이 강의를 보니 전에 잘 와닿지 않던 개념들이 와닿았다. 특히 Testcode 작성하는 것, MVC와 정적 컨텐츠, API, DI 인터페이스로 Repository 설계 후 H2를 이용하였고 JDBC, JdbcTemplate, JPA, AO
P로 바꿔끼기만 하면 되는것에 대해 책에서는 붕뜨는 느낌이었다면 이번 강의를 통해 코드를 직접 만들면서 이해하게되었고 스피링에 대한 전반적인 밑그림을 그렸다.

## 예시 사진
![image](https://user-images.githubusercontent.com/76150392/131003738-a58fabcc-3de3-4c58-84e3-a9154e5f89af.png)

![image](https://user-images.githubusercontent.com/76150392/131004023-ad238493-f08e-4b96-ad8b-644b0bb22fd3.png)

![image](https://user-images.githubusercontent.com/76150392/131004084-c2ab7dc0-1671-492d-92af-a9815c47fa1d.png)

## 비즈니스 요구사항
* 데이터: 회원ID, 이름
* 기능: 회원 등록, 조회
* 아직 데이터 저장소가 선정되지 않음(가상의 시나리오)

## 중요한 부분 정리

### 정적 컨텐츠
1. 웹 브라우저에서 내장 톰캣 서버한테 localhost:8080/hello-static.html 보낸다.
2. 스프링 컨테이너에서 hello-static 관련 컨트롤러를 찾지만 없다.
3. resources: static/hello-static.html을 찾고 웹 브라우저한테 보내준다.

### MVC(Model, View, Controller)
1. 웹 브라우저에서 내장 톰캣 서버한테 localhost:8080/hello-mvc 보낸다.
2. 스프링 컨테이너에서 helloConroller를 찾아 실행한다.
3. return: hello-template, model(name: spring)
4. viewResolver에서 templates/hello-tempalte.html을 찾아 name:spring으로 변환후 HTML을 웹 브라우저로 보낸다.

* Controller

```
@Controller
public class HelloController {
 @GetMapping("hello-mvc")
 public String helloMvc(@RequestParam("name") String name, Model model) {
 model.addAttribute("name", name);
 return "hello-template";
 }
}
```

* View

```
<html xmlns:th="http://www.thymeleaf.org">
<body>
<p th:text="'hello ' + ${name}">hello! empty</p>
</body>
</html>
```

### API
* @ResponseBody 를 사용하면 뷰 리졸버( viewResolver )를 사용하지 않음
* 대신에 HTTP의 BODY에 문자 내용을 직접 반환(HTML BODY TAG를 말하는 것이 아님)
* viewResolver 대신에 HttpMessageConverter 가 동작
* 기본 문자처리: StringHttpMessageConverter
* 기본 객체처리: MappingJackson2HttpMessageConverter
* byte 처리 등등 기타 여러 HttpMessageConverter가 기본으로 등록되어 있음

```
@Controller
public class HelloController {
 @GetMapping("hello-string")
 @ResponseBody
 public String helloString(@RequestParam("name") String name) {
 return "hello " + name;
 }
}
```

### 컴포넌트 스캔 원리
* @Component 애노테이션이 있으면 스프링 빈으로 자동 등록된다.
* @Controller 컨트롤러가 스프링 빈으로 자동 등록된 이유도 컴포넌트 스캔 때문이다.
* @Component 를 포함하는 다음 애노테이션도 스프링 빈으로 자동 등록된다.
 * @Controller
 * @Service
 * @Repository

### @Transactional
@Transactional : 테스트 케이스에 이 애노테이션이 있으면, 테스트 시작 전에 트랜잭션을 시작하고, 테스트 완료 후에 항상 롤백한다. 이렇게 하면 DB에 데이터가 남지 않으므로 다음 테스트에 영향을 주지 않는다.

