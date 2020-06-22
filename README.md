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

### 1장 인텔리제이로 스프링 부트시작하기

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
sourceCompatibility = 1.8

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