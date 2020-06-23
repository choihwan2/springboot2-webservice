# Spring Boot And AWS Project:blue_book:

[스프링 부트와  AWS 로 혼자 구현하는 웹 서비스](http://www.yes24.com/Product/Goods/83849117) 책을 읽으며 진행하는 프로젝트의 저장소이며 책을 정리하는 공간이다.

## 왜 시작했는가?

- 실무에서 사용되고 있는 Framework 를 활용한 프로젝트 경험을 쌓기 위해
- 무중단 배포에 대한 관심
- AWS 를 활용한 프로젝트

## 목차

- 1장 인텔리제이로 스프링 부트 시작하기
- 2장 스프링부트에서 테스트 코드를 작성하자 
- 추가예정



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

- **빠른 피드백**
  - 내가 하던 예전의 개발 방식으로는 코드를 수정할 때 마다 반복 해야하는 상황이 발생한다. (ex : Tomcat을 재시작하는 상황) 이런 상황을 해결해준다.
- **눈으로 하지 않아도 되는 자동 검증**
  - 그전에 `System.out.println` 으로 매번 찍어가며 눈으로 검증하던것을 자동검증이 가능하게 한다.
- **개발자가 만든 기능을 안전하게 보호**
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

이 안의 `main`에서 실행하는 `SpringApplication.run` 으로 인해 내장 WAS(Web Application Server) 가 실행된다. 이렇게 되면 항상 서버에 톰캣을 설치할 필요가 없고, 스프링 부트로 만들어진 Jar 파일(실행가능한 Java 패키징 파일)로 실행하면 된다. 꼭 스프링 부트 내장 WAS를 사용할 수 있는 것은 아니지만, 그 이유는 **'언제 어디서나 같은 환경에서 스프링 부트를 배포'** 할수 있기 때문이다. 외장 WAS를 쓴다면.. 과거에 내가 했던 경험으로는 Tomca 서버 버전을 맞추고.. 등등 환경설정에서 꽤 많은 힘이들어가는데 이걸 줄여줄 수 있다고 설명할 수 있다.



이제 안에 컨트롤러를 위한 새로운 패키지를 만들고 컨트롤러 클래스를 만들어보고 테스트 해보겠다.



- HelloController

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

src/test/java 디렉토리에 앞에 생성했던 패키자를 그대로 다시 생성해보자.





## intelliJ 단축키들(Mac)

- Action 창 검색 : `Command + Shift + A`
- Commit 창 열기 : `Command + K`
- Push 창 열기 : `Command + Shift + K`