# Spring Boot And AWS Project:blue_book:

[스프링 부트와  AWS 로 혼자 구현하는 웹 서비스](http://www.yes24.com/Product/Goods/83849117) 책을 읽으며 진행하는 프로젝트의 저장소이며 책을 정리하는 공간이다.

## 왜 시작했는가?

- 실무에서 사용되고 있는 Framework 를 활용한 프로젝트 경험을 쌓기 위해
- 무중단 배포에 대한 관심
- AWS 를 활용한 프로젝트

## 목차

- 1장 인텔리제이로 스프링 부트 시작하기
- 2장 스프링부트에서 테스트 코드를 작성하자 
- 3장 스프링 부트에서 JPA로 데이터베이스 다뤄보기



## 1장 인텔리제이로 스프링 부트시작하기

평소에 나는 `Eclipse`를 사용하다가 `intelliJ`로 넘어오면서 가장 어색했던 부분이 `Eclipse` 안에 있던 `workspace` 의 개념이 없던 것이였다.
대신에 `intelliJ`에는 `project` 와 `module` 이라는 개념만 존재하고 가능하면 서로 연관이 없는 프로젝트는 다른 화면에서 여는 것이 좋다는 것이다.
이에 추가적인 설명은 저자의 블로그 글인 [이걸](http://bit.ly/2orXeGl) 참고하자.


gradle 프로젝트를 spring boot 프로젝트로 전환

build.gradle

```groovy
buildscript{
    ext{
        springBootVersion = '2.1.7.RELEASE'
    }
    repositories {
        mavenCentral()
        jcenter()
    }
    dependencies{
        classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
    }
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'


group 'org.choihwan2.springboot'
version '1.0-SNAPSHOT'

repositories {
    mavenCentral()
}
dependencies {
    compile('org.springframework.boot:spring-boot-starter-web')


    testCompile('org.springframework.boot:spring-boot-starter-test')
    testCompile group: 'junit', name: 'junit', version: '4.12'
}
```
- 먼저 위의 `buildscript` 부분에서 `ext` 라는 키워드는 build.gradle 에서 사용하는 전연변수를 설정하고 후에 나오는 스프링 부트 그레이들 플러그인의 버전을 `2.1.7RELEASE`를 의존성으로 받겠다는 코드이다.
- 그리고 후의 apply pugin.. 들은 앞서 선언한 플로그인 의존성들을 적용할 것인지 결정
- `repositories` 는 각종 의존성들을 어떤 원격 저장소에서 받을지 정하는 것이다.
  - `mavenCentral` 은 이전부터 많이 사용하던 저장소이지만, 본인이 만든 라이브러리를 업로드 하기 위해 많은 과정과 설정이 필요. 
  - => 이런 문제점을 개선하고자 `jcenter` 가 나오게되었고 개발자들의 라이브러리가 점점 `jcenter` 로 이동중이라고 한다. 그러니 둘다 사용하자!
- `dependencies` 는 프로젝트 개발에 필요한 의존성들을 선언하는 곳
  - 안의 `compile` 과 `testCompile` 은 좀 더 공부가 필요해 보인다.


> 가장 의문이였던 것이 gradle이란 대체 무엇을 하는건가..? 였다. 아직까지도 빌드자동화를 도와준다고 하는데 이해가 부족하다.더 알아볼 필요가 있을것 같고 [링크1](https://medium.com/@goinhacker/%EC%9A%B4%EC%98%81-%EC%9E%90%EB%8F%99%ED%99%94-1-%EB%B9%8C%EB%93%9C-%EC%9E%90%EB%8F%99%ED%99%94-by-gradle-7630c0993d09) [링크2](https://araikuma.tistory.com/463) 좀 더 공부하자.





## 2장 스프링 부트에서 테스트 코드를 작성하자

과거에는 테스트 코드를 진행하는 비율이 많지 않았지만 최근의 추세는 그렇지 않다! **대부분의 서비스 회사가 테스트 코드에 관해 요구**하고 있는 추세이다.



먼저 한 가지 짚고 갈 것은 TDD(Test Driven Development)와 단위 테스트(Unit Test) 는 다른 이야기이다. TDD는 **테스트가 주도하는 개발** 즉, 테스트 코드를 먼저 작성하는 것 부터 시작한다. 

![redgreencycle](images/redgreencycle.gif)

- 항상 실패하는 테스트를 먼저 작성하고(Red)
- 테스트가 통과하는 프로덕션 코드를 작성(Green)
- 테스트가 통과하면 프로덕션 코드를 리팩토링합니다.(Refactor)



반면 단위 테스트는 TDD의 첫 번째 단계인 **기능 단위의 테스트 코드를 작성**하는 것을 말한다. TDD와 달리 테스트 코드를 꼭 먼저 작성해야 하는 것도 아니고, 리팩토링도 포함되지 않는다. 순수하게 **테스트 코드 작성** 하는 것을 말한다. 그렇다면 테스트 코드는 왜 작성해야 할까?? 위키피디아에 있는 테스트 코드의 이점은 이렇다.

- 단위 테스트는 개발 단계 초기에 문제를 발견하게 도와준다.
- 단위 테스트는 개발자가 나중에 코드를 리팩토링하거나 라이블리 업그레이드 등에서 기존 기능이 올바르게 작동하는지 확인할 수 있습니다.(예, 회귀 테스트)
- 단위 테스트는 기능에 대한 불확실성을 감소시킨다.
- 단위 테스트는 시스템에 대한 실제 문서를 제공한다. 즉, 단위 테스트 자체가 문서로 사용할 수 있다.

이에 덧붙여 저자의 경험담은..

**빠른 피드백**

- 내가 하던 예전의 개발 방식으로는 코드를 수정할 때 마다 반복 해야하는 상황이 발생한다. (ex : Tomcat을 재시작하는 상황) 이런 상황을 해결해준다.

**눈으로 하지 않아도 되는 자동 검증**

- 그전에 `System.out.println` 으로 매번 찍어가며 눈으로 검증하던것을 자동검증이 가능하게 한다.

**개발자가 만든 기능을 안전하게 보호**

- 기존에 잘되던 A라는 기능에 B기능이 잘되어 있는지 테스트해보고 오픈했더니  A기능에 문제가 생긴것을 발견. 이런 문제는 규모가 큰 서비스에서 빈번하게 일어나는 일이다. 하나의 기능을 추가할때마다 서비스의 모든 기능을 테스트 할 수는 없다. 이런 새로운 기능이 추가될 때, 기존 기능이 잘 작동되는 것을 보장해주는것이, 테스트 코드이다.



이제 Package를 만들고 본격적으로 Spring boot 프로젝트 코드를 작성해보자.



- Application.java

```java
package com.choihwan2.book.springboot2;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```



이 `Applicaton` 클래스는 앞으로 만들 프로젝트의 **메인 클래스**가 된다. `@SpringBootApplication` 으로 인해 스프링 부트의 자동설정, 스프링의 `Bean` 읽기와 생성을 모두 자동으로 설정된다. 특히나 `@SpringBootApplication` **이 있는 위치부터 설정을 읽어** 나가기 때문에! 이 클래스는 항상 **프로젝트의 최상단에 위치**해야한다.



이 안의 `main`에서 실행하는 `SpringApplication.run` 으로 인해 내장 WAS(Web Application Server) 가 실행된다. 이렇게 되면 항상 서버에 톰캣을 설치할 필요가 없고, 스프링 부트로 만들어진 Jar 파일(실행가능한 Java 패키징 파일)로 실행하면 된다. 꼭 스프링 부트 내장 WAS를 사용할 수 있는 것은 아니지만, 그 이유는 **'언제 어디서나 같은 환경에서 스프링 부트를 배포'** 할수 있기 때문이다. 외장 WAS를 쓴다면.. 과거에 내가 했던 경험으로는 톰켓 서버 버전을 맞추고.. 등등 환경설정에서 꽤 많은 힘이들어가는데 이걸 줄여줄 수 있다고 설명할 수 있다.



이제 안에 컨트롤러를 위한 새로운 패키지를 만들고 컨트롤러 클래스를 만들어보고 테스트 해보겠다.



- HelloController.java

```java
package com.choihwan2.book.springboot2.web;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @GetMapping("/hello")
    public String hello(){
        return "hello";
    }
}
```



- `RestController`
  - 컨트롤러를 JSON을 반환하는 컨트롤러로 만들어 준다.
  - 예전에는 `@ResponseBody` 를 각 메소드마다 선언했던 것을 한번에 사용할 수 있게 도와준다고 생각하면된다.



- `GetMapping`
  - HTTP method 인 `Get` 의 요청을 받을 수 있는 API를 만들어 준다.
  - 과거에는 `@RequestMapping(method = RequestMethod.GET)`으로 사용되던 것이다.



컨트롤러를 작성했으니 이제 이 코드가 제대로 작동하는지 테스트를 해보자. **WAS를 실행하지 않고 테스트 코드로 검증**해 보겠다.

src/test/java 디렉토리에 앞에 생성했던 패키지를 그대로 다시 생성해보자.



- HelloControllerTest.java

```java
package com.choihwan2.book.springboot2.web;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@RunWith(SpringRunner.class)
@WebMvcTest(controllers = HelloController.class)
public class HelloControllerTest {
    @Autowired
    private MockMvc mvc;

    @Test
    public void hello가_리턴된다() throws Exception{
        String hello = "hello";

        mvc.perform(get("/hello")).andExpect(status().isOk()).andExpect(content().string(hello));
    }
}
```



- `@RunWith(SpringRunner.class)`
  - 테스트를 진행할 때 JUnit 에 내장된 실행자 외에 다른 실행자를 실행시킨다.
  - 여기서는 SpringRunner 라는 스프링 실행자를 사용
  - 즉, 스프링 부트 테스트 와 JUnit 사이의 연결자 역활을 한다고 볼 수 있다.



- `WebMvcTest`
  - 여러 스프링 테스트 어노테이션 중, Web(Spring MVC)에 집중 할 수 있는 어노테이션 이다.
  - 선언할 경우 `@Controller`, `@ControllerAdvice` 등을 사용할 수 있음
  - 단, `@Service, @Component, @Repository` 등은 사용할 수 없다.
  - 여기서는 컨트롤러만 사용하기 떄문에 선언



- `@AutoWired`
  - 스프링이 관리하는 빈(Bean)을 주입 받는다.



- `private MockMVC mvc`
  - 웹 API를 테스트할 때 사용
  - 스프링 MVC 테스트의 시작점
  - 이 클래스를 통해 HTTP GET, POST 등에 대한 API 테스트를 할 수 있다.



- `mvc.perform(get("/hello"))`
  - MockMvc를 통해 /hello 주소로 HTTP GET 요청을 한다.
  - 체이닝이 지원되어 아래와 같이 여러 검증을 이어서 선언 가능하다.



- `.andExpect(status().isOk())`
  - mvc.perform 의 결과를 검증
  - HTTP Header의 Status를 검증한다. (ex 200,404,500등의 상태를 검증)
  - 여기서는 isOk()로 200인지 아닌지를 검증한다.



- `.andExpect(content().string(hello))`
  - mvc.perform의 결과를 검증한다.
  - 응답 본문의 내용을 검증
  - Controller 에서 "hello" 를 리턴하기 때문에 이 값이 맞는지 검증한다.



이제 테스트 코드로 검증해보고 정 의심이 간다면 브라우저로 잘 진행되는지 확인해본다.



#### 롬복(lombok)

롬복은 자바 개발을 할때 자주 사용하는 코드 Getter, Setter, 기본 생성자, toString 등을 어노테이션으로 자동 생성해주는 라이브러리이다. 인텔리제이에선 플러그인으로 쉽게 설정 가능하니 추가해보자!!

build.gradle에 `compile('org.projectlombok:lombok')`

한줄 추가와 plugins 에서 lombok을 찾아 설치하고 롬복에 대한 설정으로 인텔리제이에서 추천해주는 설정을 하면 완료!



이제 이 lombok 을 사용해보고 그것을 테스트 하는 코드를 짜보겠다



- HelloResponseDto.java

```java
package com.choihwan2.book.springboot2.web.dto;

import lombok.Getter;
import lombok.RequiredArgsConstructor;

@Getter
@RequiredArgsConstructor
public class HelloResponseDto {
    private final String name;
    private final int amount;
}
```



- `@Getter`
  - 선언된 모든 필드의 get 메소드를 생성해 준다.



- `@RequiredArgsConstructor`
  - 선언된 모든 final 필드가 포함된 생성자 생성
  - final 이 없는 필드는 생성자에 포함되지 않는다.



- HelloResonseDtoTest.java

```java
package com.choihwan2.book.springboot2.web.dto;

import org.junit.Test;

import static org.assertj.core.api.Assertions.assertThat;

public class HelloResponseDtoTest {

    @Test
    public void 룸복_기능_테스트() {
        //given
        String name = "test";
        int amount = 1000;

        //when
        HelloResponseDto dto = new HelloResponseDto(name,amount);

        //then
        assertThat(dto.getName()).isEqualTo(name);
        assertThat(dto.getAmount()).isEqualTo(amount);
    }
}
```



- `assertThat`
  - `assertj` 라는 테스트 검증 라이브러리의 검증 메소드이다.
  - 검증하고 싶은 대상을 메소드 인자로 받음
  - 메소드 체이닝이 지원되어 `isEqualTo` 와 같이 메소드를 이어서 사용 가능



- `isEqualTo`
  - `assertj` 의 동등 비교 메소드이다.
  - `assertThat`에 있는 값과 `isEqualTo`의 값을 비교해서 같을때만 성공이다

>여기서 라이브러리를 보면 Junit의 기본 assertThat 이 아닌 assetj의 assertThat 을 사용하고 있다. 저자는 Junit의 assertThat을 쓰게되면 is()와 같이 CoreMatchers 에 있는 추가적인 라이브러리가 필요하지 않다는 것과 자동완성이 좀 더 확실하게 지원되는 장점을 뽑는데 더 자세한 내용은 [이것](http://bit.ly/30vm9Lg) 를 참고하자.



이 상태에서 테스트를 실행했을때 동작한다면 다행이지만 아니라면 그레이들 버전이 4.10.2인지 확인이 필요하다. 테스트 실패 원인 파악과 어떻게 해결하는지 보려면 [이곳](http://bit.ly/382Q7d7) 을 참고하자.



그 후에 HelloController 도 새로 만든 ResponseDto를 사용하도록 코드를 추가해보겠다.

- HelloController.java 안에 추가

```java
@GetMapping("/hello/dto")
public HelloResponseDto helloDto(@RequestParam("name") String name, @RequestParam("amount") int amount) {
   return new HelloResponseDto(name, amount);
}
```

- `RequestParam`
  - 외부에서 API 로 넘긴 파라미터를 가져오는 어노테이션이다.
  - 여기서 외부 name(@RequestParam("name")) 이란 이름으로 넘긴 파라미터를 메소드 파리미터 name(String name) 에 저장하게 된다.



이제 추가된 API를 테스트하는 코드를 HelloControllerTest에 추가해보자.



- HelloControllerTest

```java
package com.choihwan2.book.springboot2.web;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;

import static org.hamcrest.Matchers.is;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

@RunWith(SpringRunner.class)
@WebMvcTest(controllers = HelloController.class)
public class HelloControllerTest {
    @Autowired
    private MockMvc mvc;

    @Test
    public void hello가_리턴된다() throws Exception {
        String hello = "hello";

        mvc.perform(get("/hello")).andExpect(status().isOk()).andExpect(content().string(hello));
    }


    @Test
    public void helloDto가_리턴된다() throws Exception {
        String name = "hello";
        int amount = 1000;

        mvc.perform(get("/hello/dto").param("name", name).param("amount", String.valueOf(amount))).andExpect(status().isOk())
                .andExpect(jsonPath("$.name", is(name))).andExpect(jsonPath("$.amount", is(amount)));

    }
}
```

- `.param`
  - API 테스트 할때 사용될 요청 파라미터를 설정한다.
  - 값은 `String` 만 허용된다.
  - 숫자/날짜 등의 데이터도 등록할 때는 문자열로 변경해야한다. 



- `jsonPath`
  - JSON 응답값을 필드별로 검증할 수 있는 메소드 이다.
  - `$`를 기준으로 필드명을 명시
  - 여기서 name 과 amount 를 검증하니 $.name , $.amount 로 검증한다.

## 3장 스프링 부트에서 JPA로 데이터베이스 다뤄보기

웹 서비스를 개발하고 운영하다 보면 결국 데이터베이스를 다루는 일을 하게 된다. 스프링으로 개발을 하다보면 `MyBatis` 와 같은 SQL 매퍼를 이용해 데이터베이스의 쿼리를 작성했다. 그러다 보니 실제 개발하는 시간 < SQL 을 다루는 시간 인걸 발견하게됨. **객체지향 프로그래밍**을 배워서 객체 모델링보다는 **테이블 모델링**에만 집중하게 되고, 객체를 단순히 테이블에 맞추어 데이터 전달 역활만 하는 개발이 이상하게 생각됨. 관계형 데티어베이스를 이용하면서 프로젝트에서 객체지향 프로그램ㅇ의 해답 => **JPA 라는 자바 표준 ORM(Object Relational Mapping** 을 발견하게됨.



현 SI 환경에서는 Spring & MyBatis 를 많이 사용하지만, 쿠팡, 우아한형제들, NHN 등은 SpringBoot & JPA 를 전사 표준으로 사용하고 있고 점점 더 많은 회사가 이 추세로 가고있다.



#### JPA가 필요한 이유

현 웹 애플리케이션에서 관계형 데이터베이스는 필수! => 객체를 관계형 데이터베이스에서 관리하는 것이 중요하게 되었다. 

- 기본적인 CRUD(Create, Read, Update, Delete) SQL 을 매번 생성해야함.(반복과 유지보수의 힘듬)

- 패러다임의 불일치

  - 객체지향 프로그래밍 : 메시지를 기반으로 **기능과 속성을 한 곳에서 관리** 
  - 관계형 데이터베이스: **어떻게 데이터를 저장**할지에 초점이 맞춰져있음.

  관계형 데이터에비읏로 객체지향(추상화,캡슐화,정보은닉,다형성 등) 을 표현할 수 있을까?

  => 어렵다.. 

이런 문제들, 서로 지향하는 바가 다른 2개 영역(객체지향 과 관계형 데이터베이스)을 중간에서 패러다임 일치 시키기 위한 기술

=> **JPA**

즉, 개발자는 객체지향적으로 프로그래밍 하고 JPA가 이를 관계형 데이터베이스에 맞게 SQL 대신 생성해서 실행한다. 이로 개발자는 SQL에 종속적인 개발을 하지 않아도 된다!



#### Spring Data JPA

JPA는 인터페이스로 자바 표준명세서이다. 인터페이스 사용을 위한 구현체로는 Hibrernate, Eclipse Link 등을 활용등이 있다. 하지만 Spring 에서는 구현체들을  쉽게 사용하고자 추상화시킨 Spring Data JPA 라는 모듈을 이용하여 JPA 기술을 다룬다. 

- JPA ← HiberNate ← Spring Data JPA

이렇게 하는 이유는

- 구현체 교체의 용이성
- 저장소 교체의 용이성

을 말할 수 있다. '구현체 교체의 용이성' 이란 **Hibernate 외에 다른 구현체로 쉽게 교체하기 위함**이라고 할 수 있다.

'저장소 교체의 용이성' 이란 **관계형 데이터베이스 외에 다른 저장소로 쉽게 교체하기 위함**이라고 할 수 있다. 더 자세한 내용들은

[이곳]([https://velog.io/@adam2/JPA%EB%8A%94-%EB%8F%84%EB%8D%B0%EC%B2%B4-%EB%AD%98%EA%B9%8C-orm-%EC%98%81%EC%86%8D%EC%84%B1-hibernate-spring-data-jpa](https://velog.io/@adam2/JPA는-도데체-뭘까-orm-영속성-hibernate-spring-data-jpa))을 참고하면 좋을 것 같다. 



#### 프로젝트에 Spring Data Jpa 적용하기

먼저 build.gradle 에 `'org.springframework.boot:spring-boot-starter-data-jpa'`  과 `com.h2database:h2`를 등록하자. 

- `spring-boot-starter-data-jpa`
  - 스프링 부트용 Spring Data Jpa 추상화 라이브러리
  - 스프링 부트 버전에 맞춰 자동으로 JPA 관련 라이브러리들의 버전을 관리해준다.
- `h2`
  - 인메모리 관계형 데이터베이스
  - 별도 설치 필요 x 프로젝트 의존성만으로 관리 가능
  - 메모리에서 실행 => 애플리케이션 재실행마다 초기화되어 테스트 용도로 많이 사용



의존성을 등록되었다면, 본격적으로 JPA 기능을 사용해 보겠다. 먼저 domain 패키지를 만들고 시작한다.

여기서 도메인이란 소프트웨어데 대한 요구사항 혹은 문제영역이라고 생각하면 된다. 기존에 MyBatis 같은걸로 쿼리 매퍼를 사용했다면 dao 패키지를 떠올리겠지만, dao 와 조금 결이 다르다고 생각하면 된다. 용어가 어색하겠지만 진행하면서 어떤건지 좀 더 체감할 수 있다고 한다.



domain 패키지 안에 posts 패키지와 Posts 클래스를 만든다.

- Posts.java

```java
package com.choihwan2.book.springboot2.domain.posts;

import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

import javax.persistence.*;

@Getter
@NoArgsConstructor
@Entity
public class Posts {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(length = 500, nullable = false)
    private String title;

    @Column(columnDefinition = "TEXT", nullable = false)
    private String content;

    private String author;

    @Builder
    public Posts(String title, String content, String author) {
        this.title = title;
        this.content = content;
        this.author = author;
    }
}
```

> 여기서 꽤 많은 어노테이션들이 상용되는데 어노테이션의 순서를 **주요 어노테이션을 클래스에 가깝게** 적는 습관을 들이면 좋다고 한다. 후에 유지보수등의 문제에서 유용함이 많다.

- `@Entity`
  - 테이블과 링크될 클래스를 나타냄
  - 기본값으로는 클래스의 카멜케이스 이름을 언더스코어 네이밍으로 테이블 이름을 매칭한다. (ex SalesManager => sales_manager table)



- `@Id`
  - 해달 테이블의 PK 필드를 나타낸다.



- `@GeneratedValue`
  - PK의 생성 규칙을 나타낸다.
  - 스프링 부트 2.0 에서는 `Genteration Type.IDENTITY` 옵션을 추가해야만 auto_increment가 된다.
  - 과거 스프링부트와 차이점은 [여기](http://jojoldu.tistory.com/295)에서 참고하자.
- `@Column`
  - 테이블의 칼럼을 나타내며 굳이 선언하지 않아도 해당 클래스의 필드는 모두 칼럼이 된다.
  - 그렇다면 사용하는 이유는? 기본값 외에 추가로 변경이 필요한 옵션이 있으면 사용!
  - 문자열 경우 `VARCHAR(255)` 가 기본값인데, 사이즈를 500으로 늘리거나 타입을 `TEXT`로 변경하고 싶거나 하는 등 경우 사용된다.

> https://ra2kstar.tistory.com/82 VARCHAR와 TEXT의 차이 링크이다. 그리고 웬만하면 Entity의 PK는 Long 타입의 Auto_increment를 추천한다. 

밑으로는 lombok 어노테이션들이다.

- `@NoArgsConstructor`
  - 기본 생성자 자동추가
  - public Posts(){} 와 같은 효과
- `@Getter`
  - 클래스 내 모든 필드의 Getter 메소드를 자동 생성
- `@Builder`
  - 해당 클래스의 빌더 패턴 클래스 생성
  - 생성자 상단에 선언 시 생성자에 포함된 필드만 빌더에 포함



여기서 보면 **Setter** 메소드가 없다는 것을 알 수 있다. 기존의 자바빈 규약과 다르게 **getter/setter**를 무작정 생성하는 경우가 있는데, 이럴경우 해당 클래스의 인스턴스 값들이 언제 어디서 변하는지 코드상으로 명확하게 구분할 수가 없어, 차후 기능 변경시 복잡해질 수 있기 때문이다. 그래서 **Entity 클래스에서는 절대 Setter 메소드를 만들지 않는다.** 대신 해당 필드의 값 변경이 필요하면 명확히 그 목적과 의도를 나타낼 수 있는 메소드를 추가해야한다. 예를들어 주문 취소 메소드가 있다면



- 잘못된 사용

```java
public class Order{
   public void setStatus(boolean status){
      this.status = status;
   }
}

public void 주문서비스의_취소이벤트(){
   order.setStatus(false);
}
```



- 올바른 사용

```java
public class Order{
   public void cancelOrder(){
      this.status = false;
   }
}

public void 주문서비스의_취소이벤트(){
   order.cancelOrder();
}
```

이렇게 사용하는 것이다. 여기서 또 의문은 **Setter가 없는 상황에서 어떻게 값을 채워 DB에 삽입**하냐 이다. 여기서는 `@Builder`를 통해 제공되는 빌더 클래스를 사용한다. 생성자나 빌더나 값을 채워주는 역활을 똑같지만 빌더클래스일 경우 지금 채워야할 필드가 어떤건지 명확하게 지정할 수 있는 장점이있다.

```java
public Example(String a, String b){
   this.a = a;
   this.b = b;
}
```

예를 들어 new Example(b,a) 처럼 **a와 b의 위치를 변경해도 코드를 실행하기 전까지는 문제를 찾을 수 없다.**

그러나 builder를 사용한다면

```java
Example.builder().a(a).b(b).build();
```

와 같이 **어느 필드에 어떤 값을 채워야 할지 명확하게 인지가 가능**하다!



이제 Posts 클래스로 Database 접근하게 해주는 JpaRepository를 생성한다.

- JpaRepository.java

```java
package com.choihwan2.book.springboot2.domain.posts;

import org.springframework.data.jpa.repository.JpaRepository;

public interface PostsRepository extends JpaRepository<Posts,Long> {
}
```

단순하게 인터페이스 생성 후, JpaRepository<Entity 클래스, PK 타입> 를 상속하면 기본적인 CRUD 메소드가 자동으로 생성된다. 여기서 가장 중요한 점은 **Entity 클래스와 기본 Entity Repository 는 함께 위치**해야 하는 점이다. 둘은 아주 밀접한 관계이고 Entity 클래스는 **기본 Repository 없이 제대로 역활을 할 수가 없다.** 후에 프로젝트가 커져 도메인별로 프로젝트를 분리해야하면 둘은 꼭 같이 움직여야 하므로 도메인 패키지에서 함께 관리한다.



이 코드들을 테스트하는 코드를 작성해보자

```java
package com.choihwan2.book.springboot2.web.domain.posts;

import com.choihwan2.book.springboot2.domain.posts.Posts;
import com.choihwan2.book.springboot2.domain.posts.PostsRepository;
import org.junit.After;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import java.util.List;

import static org.assertj.core.api.Assertions.assertThat;

@RunWith(SpringRunner.class)
@SpringBootTest
public class PostsRepositoryTest {

    @Autowired
    PostsRepository postsRepository;

    @After
    public void cleanup(){
        postsRepository.deleteAll();
    }

    @Test
    public void 게시글저장_불러오기() {
        //given
        String title = "테스트 게시글";
        String content = "테스트 본문";

        postsRepository.save(Posts.builder().title(title).content(content).author("choihwan2@naver.com").build());

        //when
        List<Posts> postsList = postsRepository.findAll();

        //then
        Posts posts = postsList.get(0);
        assertThat(posts.getTitle()).isEqualTo(title);
        assertThat(posts.getContent()).isEqualTo(content);
    }
}
```



- `@After`
  - Junit에서 단위 테스트가 끝날 대마다 수행되는 메소드 지정
  - 보통 배포 전 전체 테스트를 수행할 때 테스트간 데이터 침법을 막기 위해 사용
  - 여러 테스트가 동시에 수행되면 테스트용 데이터베이스인 H2에 데이터가 남아있어 후의 테스트를 실패 할 수도 있다.
- `postsRepository.save`
  - 테이블 posts에 insert/update 쿼리를 실행한다.
  - id 값이 있다면 update가, 없다면 insert 쿼리가 실행된다.
- `postsRepository.findAll`
  - 테이블 posts에 있는 모든 데이터를 조회해오는 메소드이다.



#### 등록/수정/조회 API 만들기

API를 만들기 위한 3개의 클래스

- Request 데이터를 받을 Dto
- API 요청을 받을 Controller
- 트랜잭션, 도메인 기능 간의 순서를 보장하는 Service



Service에서 비지니스 로직을 처리해야한다?? 그렇지 않다. Service는 **트랜잭션, 도메인 간 순서 보장의 역할**만 처리한다.

> 트랜잭션?? 도메인??

- Spring 웹 계층

![spring_web_layerr](images/spring_web_layerr.png)



- Web Layer
  - 흔히 사용하는 컨트롤러와 JSP/Freemarker 등의 뷰 템플릿 영역이다.
  - 이외에도 필터, 인터셉터, 컨트롤러 어드바이스 등 외부 요청과 응답에 대한 전반적인 영역을 말한다.



- Service Layer
  - @Service 에 사용되는 서비스 영역이다.
  - 일반적으로 Controller 와 Dao 의 중간 영역
  - @Transactional이 사용되어야 하는 영역이다.



- Repository Layer
  - **Database**와 같이 데이터 저장소에 접근하는 영역
  - 기존의 Dao(Data Access Object) 의 영역으로 이해하면 된다.



- Dtos
  - Dto(Data Transfer Object) 는 **계층 간에 데이터 교환을 위한 객체**를 이야기하며 Dtos는 이들의 영역이다.
  - 예를 들어 뷰에서 사용될 객체나 Repository Layer에서 결과로 넘겨준 객체 등이다.



- Domain Model
  - 도메인이라 불리는 개발 대상을 모든 사람이 동일한 관점에서 이해할 수 있고 공유할 수 있도록 단순화 시킨 것을 도메인 모델이라고 한다. (ex 택시 앱이라면 배차, 탑승, 요금 등이 모두 도메인)
  - @Entity를 사용했다면 @Entity가 사용된 영역 역시 도메인 모델이라고 이해해도 된다.
  - 하지만! 무조건 데이터베이스의 테이블과 관계가 있어야하는건 아니다
  - VO처럼 값 객체들도 이 영역에 해당하기 때문.



이 5가지 레이어에서 비즈니스 처리를 담당해야할 곳은?

=> **Domain**



이제 등록, 수정, 삭제 기능을 도메인 모델을 다루는 코드로 작성해 보겠다.

- PostsApiController

```java
package com.choihwan2.book.springboot2.web;

import com.choihwan2.book.springboot2.service.posts.PostsService;
import com.choihwan2.book.springboot2.web.dto.PostsSaveRequestDto;
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

@RequiredArgsConstructor
@RestController
public class PostsApiController {
    private final PostsService postsService;

    @PostMapping("/api/v1/posts")
    public Long save(@RequestBody PostsSaveRequestDto requestDto){
        return postsService.save(requestDto);
    }
}
```



- PostsService

```java
package com.choihwan2.book.springboot2.service.posts;

import com.choihwan2.book.springboot2.domain.posts.PostsRepository;
import com.choihwan2.book.springboot2.web.dto.PostsSaveRequestDto;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@RequiredArgsConstructor
@Service
public class PostsService {
    private final PostsRepository postsRepository;

    @Transactional
    public Long save(PostsSaveRequestDto requestDto){
        return postsRepository.save(requestDto.toEntity()).getId();
    }
}
```



책에서는 스프링을 어느 정도 썼다면 Controller 와 Service에서 @Autowired가 없는 것이 어색할 것 같다고 적혀져있지만.. 나는 어리둥절... 아무튼 스프링에서 Bean을 주입 받는 방식으로는

- @Autowired
- setter
- 생성자

이 중 가장 권장 하는 방식은 **생성자로 주입** 받는 방식이다. 

> 그 이유는? [여기](https://yaboong.github.io/spring/2019/08/29/why-field-injection-is-bad/)서 학습하자.

그렇다면 생성자는? 바로 `@RequiredArgsConstructor`에서 해결해 준다. final이 선언된 모든 필드를 인자값으로 하는 생성자를 롬복의 `@RequiredArgsConstructor`가 대신 생성해 준 것이다. 이제 Controller 와 Service가 사용할 Dto 클래스를 만들어보자

- PostsSaveRequestDto

```java
package com.choihwan2.book.springboot2.web.dto;

import com.choihwan2.book.springboot2.domain.posts.Posts;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

@Getter
@NoArgsConstructor
public class PostsSaveRequestDto {
    private String title;
    private String content;
    private String author;

    @Builder
    public PostsSaveRequestDto(String title, String content, String author) {
        this.title = title;
        this.content = content;
        this.author = author;
    }

    public Posts toEntity() {
        return Posts.builder().title(title).content(content).author(author).build();

    }
}
```

기존에 있던 Entity 클래스와 매우 유사하다는걸 알수있다.



- 유사한 Entity 클래스인 Post

```java
package com.choihwan2.book.springboot2.domain.posts;

import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

import javax.persistence.*;

@Getter
@NoArgsConstructor
@Entity
public class Posts {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(length = 500, nullable = false)
    private String title;

    @Column(columnDefinition = "TEXT", nullable = false)
    private String content;

    private String author;

    @Builder
    public Posts(String title, String content, String author) {
        this.title = title;
        this.content = content;
        this.author = author;
    }
}
```

하지만 Dto 클래스를 추가로 생성했다. 왜일까? 먼저 **Entity 클래스를 Request/Response 클래스로 사용해서는 안된다**라는 걸 명심하고 들어가자. Entity 클래스는 데이터베이스와 맞닿은 핵심 클래스로 Entity 클래스를 기준으로 테이블 생성, 스키마가 변경되기도 한다. 화면 변경은 자주 일어나는 사소한 기능 변경인데, 이를 위해 테이블과 연결된 Entity 클래스를 변경하는 건 너무 큰 변경이다. 그래서 자주 변경이 일어나는 View를 위한, Request 와 Response 용 Dto를 생성하는 것이다. 실제로 Controller 에서 결과값으로 여러 테이블을 조인해서 줘야 할 경욱 빈번하니 Entity 클래스 만으로 표현하기 어려운 경우가 많다. 그러니 꼭 Entity 클래스와 Controller에서 쓸 Dto는 분리해서 사용하는 것이 중요하다.



이제 테스트를 진행해보자.



- PostsApiControllerTest

```java
package com.choihwan2.book.springboot2.web;

import com.choihwan2.book.springboot2.domain.posts.Posts;
import com.choihwan2.book.springboot2.domain.posts.PostsRepository;
import com.choihwan2.book.springboot2.web.dto.PostsSaveRequestDto;
import org.junit.After;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.boot.web.server.LocalServerPort;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.test.context.junit4.SpringRunner;

import java.util.List;

import static org.assertj.core.api.Assertions.assertThat;

@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class PostsApiControllerTest {

    @LocalServerPort
    private int port;

    @Autowired
    private TestRestTemplate restTemplate;

    @Autowired
    private PostsRepository postsRepository;

    @After
    public void tearDown() throws Exception{
        postsRepository.deleteAll();
    }

    @Test
    public void Posts_등록된다() throws Exception{
        //given
        String title = "title";
        String content = "content";
        PostsSaveRequestDto requestDto = PostsSaveRequestDto.builder().title(title).content(content).author("author").build();

        String url = "http://localhost:" + port + "/api/v1/posts";

        //when
        ResponseEntity<Long> responseEntity = restTemplate.postForEntity(url, requestDto, Long.class);

        //then

        assertThat(responseEntity.getStatusCode()).isEqualTo(HttpStatus.OK);
        assertThat(responseEntity.getBody()).isGreaterThan(0L);

        List<Posts> all = postsRepository.findAll();
        assertThat(all.get(0).getTitle()).isEqualTo(title);
        assertThat(all.get(0).getContent()).isEqualTo(content);
    }
}
```



Api Controller를 테스트 하는데 HelloController 와 다르게 `@WebMvcTest`를 사용하지 않았다. `@WebMvcTest` 의 경우 JPA기능이 작동하지 않기 때문이다. 지금과 같이 JPA 기능까지 한번에 테스트 할때는 `@SpringBootTest`와 TestRestTemplate을 사용하면 된다. 등록 기능을 완성했으니 수정/조회 기능을 만들어보자



- PostsApiController

```java
@RequiredArgsConstructor
@RestController
public class PostsApiController {
   ///...
   
    @PutMapping("/api/v1/posts/{id}")
    public Long update(@PathVariable Long id, @RequestBody PostsUpdateRequestDto requestDto){
        return postsService.update(id, requestDto);
    }

    @GetMapping("/api/v1/posts/{id}")
    public PostsResponseDto findById (@PathVariable Long id){
        return postsService.findById(id);
    }
}

```



- PostsResponseDto

```java
package com.choihwan2.book.springboot2.web.dto;

import com.choihwan2.book.springboot2.domain.posts.Posts;
import lombok.Getter;

@Getter
public class PostsResponseDto {
    private Long id;
    private String title;
    private String content;
    private String author;

    public PostsResponseDto(Posts entity){
        this.id = entity.getId();
        this.title = entity.getTitle();
        this.content = entity.getContent();
        this.author = entity.getAuthor();
    }
}
```

PostsResponseDto 는 Entity의 필드중 일부만 사용하니 생성자로 entity를 받아 필드에 값을 넣습니다.



- PostsUpdateRequestDto

```java
package com.choihwan2.book.springboot2.web.dto;

import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

@Getter
@NoArgsConstructor
public class PostsUpdateRequestDto {
    private String title;
    private String content;

    @Builder
    public PostsUpdateRequestDto(String title, String content){
        this.title = title;
        this.content = content;
    }
}
```



- Posts

```java
@Getter
@NoArgsConstructor
@Entity
public class Posts {
   ///...
    public void update(String title, String content){
        this.title = title;
        this.content = content;
    }
}

```



- PostsService

```java
@RequiredArgsConstructor
@Service
public class PostsService {
   ///...

    @Transactional
    public Long update(Long id, PostsUpdateRequestDto requestDto) {
        Posts posts = postsRepository.findById(id).orElseThrow(() -> new IllegalArgumentException("해당 게시글이 없습니다. id = " + id));

        posts.update(requestDto.getTitle(), requestDto.getContent());

        return id;
    }

    public PostsResponseDto findById(Long id) {
        Posts entity = postsRepository.findById(id).orElseThrow(() -> new IllegalArgumentException("해당 게시글이 없습니다. id = " + id));

        return new PostsResponseDto(entity);
    }
}

```



여기서 신기한 건 update 기능에서 **쿼리를 날리는 부분이 없다**는 것이다. 이게 가능한 이유는 JPA의 **영속성 컨텍스트** 때문이다. 영속성 컨텍스트란, 엔티티를 영구저장하는 환경이다. 일종의 논리적 개념이며 JPA의 핵심은 **엔티티가 영속성 컨텍스트에 포함되어 있냐 없냐**이다.



JPA의 엔티티 매니저가 활성된 상태로 **트랜잭션 안에서 데이터베이스에서 데이터를 가져오면** 이 데이터는 영속성 컨텍스트가 유지된 상태이다. 이 상태에서 값을 변경하면 **트랜잭션이 끝나는 시점에 해당 테이블에 변경분을 반영**한다. 이 개념을 **더티 체킹** 이라고 한다.

> 좀 더 설명이 필요할 것 같으면 https://jojoldu.tistory.com/415 로 가자!



이제 이 코드를 테스트해보자!



- PostsApiControllerTest

```java
package com.choihwan2.book.springboot2.web;

import com.choihwan2.book.springboot2.domain.posts.Posts;
import com.choihwan2.book.springboot2.domain.posts.PostsRepository;
import com.choihwan2.book.springboot2.web.dto.PostsSaveRequestDto;
import com.choihwan2.book.springboot2.web.dto.PostsUpdateRequestDto;
import org.junit.After;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.boot.web.server.LocalServerPort;
import org.springframework.http.HttpEntity;
import org.springframework.http.HttpMethod;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.test.context.junit4.SpringRunner;

import java.util.List;

import static org.assertj.core.api.Assertions.assertThat;

@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class PostsApiControllerTest {
   //***

    @Test
    public void Posts_수정된다() throws Exception{
        //given
        Posts savedPosts = postsRepository.save(Posts.builder().title("title").content("content").author("author").build());

        Long updateId = savedPosts.getId();
        String expectedTitle = "title2";
        String expectedContent = "content2";

        PostsUpdateRequestDto requestDto = PostsUpdateRequestDto.builder().title(expectedTitle).content(expectedContent).build();

        String url = "http://localhost:" + port + "/api/v1/posts/" + updateId;

        HttpEntity<PostsUpdateRequestDto> requestEntity = new HttpEntity<>(requestDto);

        //when
        ResponseEntity<Long> responseEntity = restTemplate.exchange(url, HttpMethod.PUT, requestEntity, Long.class);

        //then
        assertThat(responseEntity.getStatusCode()).isEqualTo(HttpStatus.OK);
        assertThat(responseEntity.getBody()).isGreaterThan(0L);

        List<Posts> all = postsRepository.findAll();

        assertThat(all.get(0).getTitle()).isEqualTo(expectedTitle);
        assertThat(all.get(0).getContent()).isEqualTo(expectedContent);

    }
}
```



사용해보면 예전  MyBatis를 사용했을 때 보다 JPA를 씀으로 좀 더 객체지향적으로 코딩을 할 수 있음을 느낄 수 있다. 조회 기능을 테스트하기 위해 application.properties에 `spring.h2.console.enabled = true` 를 추가해보자.



http://localhost:8080/h2-console 로 접속하여 JDBC:URL 부분을 `jdbc:h2:mem:testdb` 로 바꾸어 접속한다음 쿼리문들을 실행하여 조회할 수 있다. 데이터를 추가한다음 브라우저로 API를 조회해 보기도 해보자!





## intelliJ 단축키들(Mac)

- Action 창 검색 : `Command + Shift + A`
- Commit 창 열기 : `Command + K`
- Push 창 열기 : `Command + Shift + K`
- Test관련 창 띄우기 : `Command + Shift + T`

