---
layout: post
title: "SpringBoot Reference번역 - Part04:스프링 부트의 기능들"
author: "김성환"
categories: [Springboot]
tags: [springboot, spring, springboot reference]
description: 스프링 부트 part4 번역. 스프링부트 기능들 상세설명
published: true
comments: true
---
버전 : 2.0.0.M7<br>
저자: 
Phillip Webb, Dave Syer, Josh Long, Stéphane Nicoll, Rob Winch, Andy Wilkinson, Marcel Overdijk, Christian Dupuis, Sébastien Deleuze, Michael Simons, Vedran Pavić, Jay Bryant
번역: 김성환

__Copyright ⓒ 2012-2017__

Copies of this document may be made for your own use and for distribution to others, provided that you do not charge any fee for such copies and further provided that each copy contains this Copyright Notice, whether distributed in print or electronically.


[원본](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#boot-features)

* Kramdown table of contents
{:toc .toc}


# Part4 스프링부트 기능들
이번 섹션에서는 스프링 부트의 좀더 상세한 사항에 대해 볼 것이다. 여기서 사용하고 커스터마이징하길 원하는 주요 기능들에 대해 배울 것이다.  "[Part2 시작하기](/blog/2017/12/04/spring-boot-chapter02/#heading-part2-시작하기)"나 "[Part3 스프링부트 사용하기](/blog/2017/12/04/spring-boot-chapter03/#heading-part3-스프링-부트-사용하기)"를 아직 읽지 않았다면 기초지식을 얻기 위해 먼저 읽는 것이 좋을 것이다.

## 23. SpringApplication
`SpringApplication` 클래스는 스프링 어플리케이션을 `main()`을 사용해 시동하기 위한 편리한 방법들을 제공한다. 대부분의 상황에서, static 메소드인 `SpringApplication.run()` 메소드를  다음과 같이 사용한다. 
```java
public static void main(String[] args) {
	SpringApplication.run(MySpringConfiguration.class, args);
}
```
어플리케이션이 시작하면 다음과 비슷한 결과를 보게 될 것이다.
```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::   v2.0.0.BUILD-SNAPSHOT

2013-07-31 00:08:16.117  INFO 56603 --- [           main] o.s.b.s.app.SampleApplication            : Starting SampleApplication v0.1.0 on mycomputer with PID 56603 (/apps/myapp.jar started by pwebb)
2013-07-31 00:08:16.166  INFO 56603 --- [           main] ationConfigServletWebServerApplicationContext : Refreshing org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@6e5a8246: startup date [Wed Jul 31 00:08:16 PDT 2013]; root of context hierarchy
2014-03-04 13:09:54.912  INFO 41370 --- [           main] .t.TomcatServletWebServerFactory : Server initialized with port: 8080
2014-03-04 13:09:56.501  INFO 41370 --- [           main] o.s.b.s.app.SampleApplication            : Started SampleApplication in 2.992 seconds (JVM running for 3.658)
```
기본적으로 `INFO` 로그 메세지가 보여지는데 거기에는 어플리케이션을 실행한 사용자와 같은 관련된 시작 상세 메세지들이 포함되 있다.

### 23.1 시작 실패
만약 시작을 실패하면, 등록된 `FailureAnalyzers`<sub>실패 분석기</sub> 가 그 실패 이유를 알려주는 에러메세지와 그 문제를 해결하기 위한 구체적인 동작을 제공한다. 예를 들어 `8080` 포트번호로 웹어플리케이션을 실행하는데 그 포트번호가 이미 사용중인 경우 다음과 비슷한 메세지를 보게될 것이다.
```
***************************
APPLICATION FAILED TO START
***************************

Description:

Embedded servlet container failed to start. Port 8080 was already in use.

Action:

Identify and stop the process that's listening on port 8080 or configure this application to listen on another port.
```
>스프링 부트는 많은 `FailureAnalyzer` 구현를 제공하며 [당신이 만들어 추가할 수도 있다.](/blog/2017/12/19/spring-boot-chapter09/#heading-721-사용자-정의-failureanalyzer-생성하기)
발생한 예외를 처리할 실패분석기(FailureAnalyzer)가 없는 경우에도 무슨 문제가 일어났는지 알려 주는 조건보고서를 내보낼 수있다. 그렇게 하기 위해 `org.springframework.boot.autoconfigure.logging.ConditionEvaluationReportLoggingListener`을 위해 [`debug` 프라퍼티](#heading-24-외부설정) 또는 [`DEBUG` 로깅](#heading-264-로그레벨들)을 활성화해야 한다. 

예를들어 `java -jar`를 사용해 어플리케이션을 실행하는 경우 다음과 같이 `debug` 프라퍼티를 활성화 할 수있다.
```
$ java -jar myproject-0.0.1-SNAPSHOT.jar --debug
```
### 23.2 Banner를 내가 원하는 대로 바꾸기
시작할때 출력되는 배너를 클래스 패스상에 `banner.txt`파일을 추가하거나 `spring.banner.location` 프라퍼티에 그 파일의 위치를 설정해서 바꿀 수 있다. 만약 파일의 인코딩이 UTF-8이 아니면 `spring.banner.charset` 설정을 해야 한다. 텍스트 파일 이외에도 `banner.gif`, `banner.jpg` 또는 `banner.png` 이미지 파일을 클래스 피스상에 위치하거나 `spring.banner.image.location` 프라퍼티 설정을 이용해 추가할 수있다. 이미지들은 ASCII art로 변환되어 text 배너로 출력된다.

banner.txt 파일안에 다음과 같은 플레이스홀더를 사용할 수있다.

**표 23.1. 배너에서 사용하는 변수들**

|변수|설명|
|-|-|
|`${application.version}`|`MAINFEST.MF` 파일에 선언된 어플리케이션의 버전. 예를 들어 `Implementation-Version: 1.0` 은 `1.0`으로 출력된다.|
|`${application.formatted-version}`|`MANIFEST>MF`에 선언된 어플리케이션의 버전번호를 보여주기 위한 형식(소괄호로 감싸고 `v`를 접두어로 붙인다)으로 보여준다. 예 : `(v1.0)`|
|`${spring-boot.version}`|사용하고 있는 스프링 버전. 예: `2.0.0.BUILD-SNAPSHOT`|
|`${spring-boot.formatted-version}`|사용하고 있는 스프링 버전을 보여주기 위한 형식(소괄호로 감싸고 `v`를 접두어로 붙인다.)으로 보여준다. 예 : `(v2.0.0.BUILD-SNAPSHOT)`|
|`${Ansi.NAME}`(또는 `${AnsiColor.NAME}`, `${AnsiBackground.NAME}`, `${AnsiStyle.NAME}`)|`NAME`은 ANSI 이스케이프코드의 이름이다. 자세한 것은 `[AnsiPropertySource](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot/src/main/java/org/springframework/boot/ansi/AnsiPropertySource.java)`를 보시오.|
|`${application.title}`|`MANIFEST.MF`에 선언된 어플리케이션 제목. 예를 들어 `Implementation-Title: MyApp`은 `MyApp`으로 출력된다.|

>`SpringApplication.setBanner(…)` 메소드를 이용해 프로그램적으로 Banner를 생성할 수있다. `org.springframework.boot.Banner` 인터페이스를 상속받고 `printBanner()` 메소드를 구현하면된다.

또한 `spring.main.banner-mode` 프라퍼티를 사용해 배너가 `System.out`(`console`)로 출력되거나, 설정되 있는 logger(`log`)로 전달되거나 또는 생산되지 않도록(`off`) 설정할 수있다.

출력된 배너는 `springBootBanner` 란 이름의 싱글톤 빈으로 등록된다.

>YAML  은 `off`가 `false`를 의미 하므로 어플리케이션에서 배너를 사용 안하려면 따옴표를 추가해야만 한다.
><pre style='background-color:lightgray;font-size:1.1em;color:#7F007F'>
>      spring:
>               main:
>                       banner-mode: "off"
></pre>

### 23.3 SpringApplication 커스터마이징하기

만약`SpringApplication`의 기본 설정 들이 맘에들지 않으면 로컬객체를 생성해서 커스터마이징해 대신 사용할 수 있다. 예를 들어 배너를 끄고 싶으면 다음과 같이 쓸 수 있다. 
```java
public static void main(String[] args) { 	
   SpringApplication app = new SpringApplication(MySpringConfiguration.class);
   app.setBannerMode(Banner.Mode.OFF); 	
   app.run(args);
}
```
>`SpringApplication`로 전달 되는 생성자 인수들은 스프링 빈들을 위한 설정 소스들 이다. 대부분의 경우 `@Configuration` 클래스들을 참조 시키지만 XML설정이나 스캔될 패키지를 참조시킬 수도 있다. 

`SpringApplication`는 `application.properties`파일을 사용해서 설정 할 수도 있다. 자세한 것은  [24. 외부설정](#heading-24-외부설정)를 보시오

설정 옵션들의 전체목록은 [`SpringApplication` Javadoc](https://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/api/org/springframework/boot/SpringApplication.html)을 보시오.

### 23.4 Fluent Builder API
만약 계층관계 `ApplicationContext` [^1]를 생성해야 하거나 'fluent' builder[^2] API를 사용하는 것을 선호한다면 `SpringApplicationBuilder`를 사용할 수 있다.

`SpringApplicationBuilder`는 여러 메소드 호출을 연결해서 호출<sub>method chain</sub> 할 수있게 하고 계층관계를 만들수 있는 `parent`와 `child` 메소드를 포함하고 있다.<br>
예)
```java
new SpringApplicationBuilder()
               .sources(Parent.class)
               .child(Application.class)
               .bannerMode(Banner.Mode.OFF)
               .run(args);
```
>`ApplicationContext` 계층관계를 생성할 때 몇몇 제약이 있다. 예를 들어 웹 컴포넌트는 **반드시** 자식 context에 포함되 있어야 한다. 그리고 같은 `Environment`가 부모와 자식 컨텍스트들 양쪽에서 사용되어야 한다. 자세한 사항은 [`SpringApplicationBuilder` Javadoc](https://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/api/org/springframework/boot/builder/SpringApplicationBuilder.html)을 보시오.

### 23.5 어플리케이션 이벤트와 리스너들
 `SpringApplication`은 [`ContextRefreshedEvent`](https://docs.spring.io/spring/docs/5.0.2.RELEASE/javadoc-api/org/springframework/context/event/ContextRefreshedEvent.html)와 같은 스프링 프레임워크가 발생시키는 이벤트들과 더불어 몇몇 추가적인  어플리케이션 이벤트들을 발생시킨다.
> 몇몇 이벤트들은 `ApplicationContext`가 생성되기 전에 발생하기 때문에 리스너를 `@Bean`으로 등록할 수없다. 그런 리스너들은 `SpringApplication.addListeners(..)`나 `SpringApplicationBuilder.listeners(...)` 메소드들을 사용해 등록할 수있다.
어플리케이션이 생성되는 방식과 관계없이 이 리스너들이 **자동적으로** 등록되도록 하길 원하면, `META-INF/spring.factories` 파일을 프로젝트에 추가하고 거기에 리스너(들) `org.springframework.context.ApplicationListener` 키값을 사용해 다음과 같이 참조시킨다. 
```
org.springframework.context.ApplicationListener=com.example.project.MyListener
```

Application 이벤트들은 실행시 다음과 같은 순서대로 발생한다.
1. `ApplicationStartingEvent`은 실행은 시작했지만 등록된 리스너들과 Initializer들의 등록을 제외한 어떤 것도 진행 되기 전에 전송된다.
2. `ApplicationEnvironmentPreparedEvent`은 Context에서 사용될 `Environment`을 찾았지만 아직 Context가 생성되기 전에 전송된다.
3. `ApplicationPreparedEvent`은 정의된 빈들이 로딩되고 refresh가 시작되기 바로전에 전송된다. 
4. `ApplicationReadyEvent `는 refresh되고 관련 callback 들이 진행 된 이후에 전송된다. 이 시점은 어플리케이션이 요청에 대해서 서비스할 준비 되었음을 가리킨다.
5. `ApplicationFailedEvent`은 시작 도중 예외가 발생하면 전송된다.

>어플리케이션 이벤트들을 사용할 필요가 없는 경우가 자주있다. 그러나 이벤트들을 알고 있으면 훨씬 편리할 수있다. 내부적으로 스프링 부트는 많은 일들을 처리하기 위해 이벤트들을 사용한다.

어플리케이션 이벤트들은 스프링 프레임워크의 이벤트 발생 메카니즘을 사용해 전송된다. 이 메카니즘에서는 자식 컨텍스트의 리스너들에게 발생한 이벤트는 조상 컨텍스트들의 리스너에게도 발생되도록 한다. 이 결과로 만약 어플리케이션이 `SpringApplication` 객체의 계층관계를 사용한다면 리스너는 같은 타입의 어플리케이션 이벤트 객체들을 여러개 받게 된다.

리스너가 자신의 컨텍스트에서 발생한 이벤트인지 자식 컨텍스트에서 발생한 이벤트인지를 구별하게 하기 위해, 그것의 어플리케이션 컨텍스트를 주입받아서 이벤트가 발생한 Context와 비교해야 한다. 컨텍스트는 `ApplicationContextAware`를 구현하거나 리스너가 빈일 경우 `@Autowired`를 사용해서 주입받을 수있다.

### 23.6 웹 환경
`SpringApplication`은 어플리케이션에 맞는 타입의 `ApplicationContext`를 생성하기 위해 시도한다. 기본적으로, 웹어플리케이션인지 아닌지를 기반으로  `AnnotationConfigApplicationContext`나 `AnnotationConfigServletWebServerApplicationContext`가 사용된다

`웹 환경`인지를 결정하기 위해 사용하는 알고리즘은 꽤 단순하다. (이것은 몇몇 클래스들이 존재하는지에 기초한다.) 만약 기본설정을 재정의하려고 하면 `setWebEnvironment(boolean webEnvironment)` 메소드를 사용한다.

`setApplicationContextClass(...)`를 사용해서 사용할 `ApplicationContext` 타입을 직접 지정할 수도 있다.

>JUnit test에서 `SpringApplication`을 사용할 때 종종 `setWebEnvironment(false)`를 사용한다.

### 23.7 어플리케이션 인수들 접근하기.
`SpringApplication.run(...)` 을 이용해 전송된 어플리케이션 인수들에 접근하려면 `org.springframework.boot.ApplicationArguments` 빈을 주입 받으면 된다. `ApplicationArguments` 인터페이스가 인수의 원래 타입인 `String[]`의 인수나 그것을 정제한<sub>parsing</sub> `option` 과 `non-option` 타입의 인수 둘 다로 접근하는 방법을 제공한다. 다음은 그 예제코드다.
```java
import org.springframework.boot.*
import org.springframework.beans.factory.annotation.*
import org.springframework.stereotype.*

@Component
public class MyBean {

	@Autowired
	public MyBean(ApplicationArguments args) {
		boolean debug = args.containsOption("debug");
		List<String> files = args.getNonOptionArgs();
		// if run with "--debug logfile.txt" debug=true, files=["logfile.txt"]
	}
}
```
> 스프링 부트는 `CommandLinePropertySource`를 스프링 `Environment`에 등록할 수있다. 그렇게 하면 `@Value` 어노테이션을 사용해 하나의 어플리케이션 인수들을 주입할 수있다.


### 23.8 ApplicationRunner나 CommandLineRunner 사용

만약 SpringApplication이 시작할 때 특정 코드를 한번 실행 시킬 필요가 있으면 `ApplicationRunner` 나 `CommandLineRunner` 인터페이스를 implements 할 수 있다.
두 인터페이스는 같은 방식으로 작동하며 `SpringApplication.run(...)` 메소드 실행이 완료되기 직전에 호출 되는 `run` 메소드를 하나 제공 한다. 
`CommandLineRunner` 인테페이스는 어플리케이션 인수를 한개의 스트링 배열로 접근해 사용 하는 방법을 제공 하지만 `ApplicationRunner`는 이전에 우리가 봤던 `ApplicationArguments`인터페이스를 사용한다.
```java
import org.springframework.boot.*
import org.springframework.stereotype.* 
@Component 
public class MyBean implements CommandLineRunner { 
   	public void run(String... args) { 		
       // Do something... 	
   }
}
```
`CommandLineRunner`나 `ApplicationRunner`빈들을 여러개 정의 하고 그들의 실행 순서를 정의 해야 하는 경우 추가적으로 `org.springframework.core.Ordered` 인터페이스를 구현하거나 `org.springframework.core.annotation.Order` 어노테이션을 사용할 수 있다.


### 23.9 어플리케이션 종료
각 `SpringApplication`은 종료시 `ApplicationContext`를 정상적으로 close하는 것을 보장하도록 JVM에 shutdown hook을 등록한다. 모든 표준 스프링 생명주기 콜백들(`DisposableBean` 인터페이스나 `@PreDestroy`어노테이션)이 사용될 수 있다.

거기에 더해 `SpringApplication.exit()`이 호출 될 때 특정 종료코드를 반환하기를 원하면 빈들이 `org.springframework.boot.ExitCodeGenerator` 인터페이스를 구현하면 된다. 이 종료코드는  `System.exit()`으로 전달되어 `System.exit()`의 상태코드로 반환된다. 다음은 그 예이다.
```java
@SpringBootApplication
public class ExitCodeApplication {

	@Bean
	public ExitCodeGenerator exitCodeGenerator() {
		return () -> 42;
	}

	public static void main(String[] args) {
		System.exit(SpringApplication
				.exit(SpringApplication.run(ExitCodeApplication.class, args)));
	}

}
```
또한 `ExitCodeGenerator` 인터페이스는 예외<sub>Exception</sub>에서 구현할 수도 있다. 특정 예외가 발생하면 스프링 부트는 구현된 `getExitCode()` 메소드가 제공하는 종료코드를 반환한다.

### 23.10 관리자 기능
`spring.application.admin.enabled` 프라퍼티를 사용하면 어플리케이션에 관리자-관련 기능을 활성화 시킬 수 있다. 이것은 `MBeanServer` 플랫폼에 [`SpringApplicationAdminMXBean`](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot/src/main/java/org/springframework/boot/admin/SpringApplicationAdminMXBean.java)을 노출시킨다. 이것을 이용해 스프링 부트 어플리케이션의 관리자 기능을 원격으로 사용할 수있다. 이 기능은 서비스 어떤 랩퍼 구현에도 유용할 수있다.
> 만약 어플리케이션이 실행 중인 HTTP 포트번호를 알려면, `local.server.port` 키로 프라퍼티를 조회한다.
> > 주의 : 이 기능을 활성화 시킬때 MBean이 어플리케이션을 종료<sub>Shutdown</sub>하는 메소드를 노출하므로 주의해야 한다..

## 24. 외부설정
스프링 부트는 환경 설정을 외부에 할 수있게 해서 같은 어플리케이션 코드를 다른 환경에서 사용할 수있게 한다.  외부 환경 설정을 하기 위해 Properties 파일들, YAML 파일들, 환경변수들 그리고 Command-Line 인수들을 사용할 수 있다. 설정된 프라퍼티들은 `@Value`를 사용해 직접 빈들에 주입할 수도 있고, 스프링의 `Environment` 추상을 통해 접근하거나 `@ConfigurationProperties`를 통해 [구조화된 객체에 묶일 수도 있다.](#heading-247-Type-safe-설정-프라퍼티들)

스프링 부트는 값들을 합리적으로 재정의하도록 설계한 매우 특별한 `PropertySource` 순서를 사용한다. 프라퍼티들은 다음 순서대로 적용된다.
1. 홈 디렉토리상의 [Devtools의 전역설정 프라퍼티](/blog/2017/12/04/spring-boot-chapter03/#heading-204-전역설정들)(devtools가 실행 중일때 `~/.spring-boot-devtools.properties`)
2. 테스트 할 때 `@TestPropertySource` 어노테이션
3. 테스트 할 때 `@SpringBootTest#properties` 어노테이션
4. Command-Line 인수
5. `SPRING_APPLICATION_JSON`에 설정된 프라퍼티들(환경변수나 시스템 프라퍼티 내장된 inline JSON)
6. `ServletConfig` 초기파라미터
7. `ServletContext` 초기파라미터
8. `java:comp/env`로 부터의 JNDI 속성
9. Java 시스템 프라퍼티(`System.getProperties()`).
10. 운영체제 환경변수
11. `random.*` 형태의 프라퍼티들만 가지고 있는 `RandomValuePropertySource`
12. 패키지된 jar 파일 외부의 [프로파일 관련 어플리케이션 프라퍼티들](#heading-244-프로파일-관련-프라퍼티들)(`application-{profile}.properties`와 YAML 문서) 
13. 패키지된 jar 파일 내부의 [프로파일 관련 어플리케이션 프라퍼티들](#heading-244-프로파일-관련-프라퍼티들)(`application-{profile}.properties`와 YAML 문서)
14. 패키지된 jar 파일 외부의 어플리케이션 프라퍼티들 (`application.properties`와 YAML 문서)
15. 패키지된 jar 파일 내부의 어플리케이션 프라퍼티들 (`application.properties`와 YAML 문서)
16. `@Configuration`클래스에 선언한 `@PropertySource` 어노테이션
17. 기본 프라퍼티들 (`SpringApplication.setDefaultProperties`를 사용해 작성된)

구체적인 예제를 제공하기 위해, `name` 프라퍼티를 사용하는 `@Component`를 다음 예제와 같이 개발하고 있다고 가정한다.
```java
import org.springframework.stereotype.*;
import org.springframework.beans.factory.annotation.*;

@Component
public class MyBean {

	@Value("${name}")
	private String name;

    // ...
}
```
어플리케이션의 클래스패스(jar파일 내)상에 `application.properties` 파일이`name`에 대한 유용한 기본 프라퍼티 값을 제공할 수있다. 새로운 환경에서 실행될 때, `name`을 재정의한 `application.properties` 파일이 jar파일 외부에서 제공될 수도 있다. 일회성 테스트를 위해 시작할 때 Command-Line 인수를 이용해 전달 할 수 있다. (예를 들어 `java -jar app.jar --name="Spring"`).

>`SPRING_APPLICATION_JSON` 프라퍼티는 환경변수로 Command line상에서 제공될 수 있다. 예를 들어 다음 라인을 UNI*X 쉘에서 사용할 수있다.<br>
><strong> $ SPRING_APPLICATION_JSON='{"acme":{"name":"test"}}' java -jar myapp.jar</strong><br>
>위 예가 실행되면, 스프링 `Environment`에서는 `acme.name=test`로 된다. JSON을 시스템 프라퍼티에서 `spring.application.json`을 사용해 다음 예처럼 제공할 수도 있다.<br>
><strong> $ java -Dspring.application.json='{"name":"test"}' -jar myapp.jar</strong><br>
>JSON을 Command-line 인수를 사용해 다음 예 처럼 제공할 수도 있다.<br>
><strong> $ java -jar myapp.jar --spring.application.json='{"name":"test"}'</strong><br>
>다음과 같이 JNDI 변수를 이용해 JSON을 제공을 할 수도 있다. <br>
><strong> `java:comp/env/spring.application.json`</strong>

### 24.1 임의의 값들 설정 (Random Values)
`RandomValuePropertySource`는 임의의 값들을 주입할때 유용하다. (예를 들어 비밀값<sub>패스워드같은</sub>또는 테스트 케이스들에 ). 랜덤 값으로 Integer, long, uuid나 스트링 들을 다음 예와 같이 생성한다.
```
my.secret=${random.value}
my.number=${random.int}
my.bignumber=${random.long}
my.uuid=${random.uuid}
my.number.less.than.ten=${random.int(10)}
my.number.in.range=${random.int[1024,65536]}
```
`random.int*` 구문은 `OPEN value (,max) CLOSE`이다. `OPEN,CLOSE`는 어떤 문자든 올수있고 `value, max`는 정수들이다. 만약 `max`가 제공되면 `value`는 최소값이고 `max`는 최대값이된다. (배타적이다)

## 24.2 Command Line 설정 접근
기본적으로 `SpringApplication`은 모든 command line 옵션 인수(이 인수들은 '--'로 시작한다. 예를 들어 `--server.port=9000`)를 `property`로 변환한 뒤 Spring `Environment`에 추가한다.

Command line 프라퍼티들이 `Environment`에 추가 안되게 하려면 `SpringApplication.setAddCommandLineProperties(false)`를 사용해서 비활성화 시킬 수 있다.

### 24.3 어플리케이션 프라퍼티 파일들
`SpringApplication`은 다음 경로에 있는 `application.properties` 파일의 프라퍼티들을 가져와서 Spring `Environment`에 추가한다.
1. 현재 디렉토리의 하위디렉토리인 `/config`
2. 현재 디렉토리
3. 클래스패스 상의 `/config` 패키지
4. 루트 클래스패스

위의 목록은 우선순위 순이다 (목록의 상위에 정의한 프라퍼티들은 하위에 정의한 것을 우선한다.)
>'.properties'를 대신해 [YAML('.yml')파일을 사용](#heading-246-properties-대신-yaml-사용)할 수 있다.

파일명으로 `application.properties`말고 다른 것을 쓰려면 `spring.config.name` 환경 설정의 값으로 지정하면된다. 또 `spring.config.location` 환경설정을 사용해 명시적으로 위치를 지정할 수 있다. (디렉토리 위치나 파일 경로를 여러개 설정할 경우 구분자로 쉼표를 사용한다.)
다음 예제는 다른 파일명을 명시하는 방법을 보여준다.
```
$ java -jar myproject.jar --spring.config.name=myproject
```
다음 예제는 두개의 경로를 어떻게 지정하는지 보여준다.
```
$ java -jar myproject.jar --spring.config.location=classpath:/default.properties,classpath:/override.properties
```
>`spring.config.name`과 `spring.config.location'은 어떤 파일들을 로드할지 결정해야 하기 때문에 일찍 사용된다. 그래서 환경 설정<sub>Property</sub>으로 정의해야만 한다. (일반적으로 OS 환경변수, 시스템 프라퍼티 또는 Command line 인수)

`spring.config.location`가 디렉토리들(파일의 반대의 의미로)을 지정하면 `/`로 끝나도록 해야 한다. (그렇게 실행하면 로딩전에 프로필-관련 파일이름을 포함한 `spring.config.name`의 파일명이 뒤에 붙는다.) `spring.config.location`에 명시한 파일명은 프로파일 관련 변경 지원 없이 그대로 사용하고 모든 프로파일 관련 프라퍼티들을 덮어쓴다.

설정 위치는 작성된 역순으로 찾는다. 기본적인 설정 위치는 `classpath:/, classpath:/config/, file:./, file:./config/` 이다. 검색 순서 결과는 다음과 같다.
1. file:./config/
2. file:./
3. classpath:/config/
4. classpath:/

`spring.config.location`을 사용해 사용자 정의 위치를 설정하면 그 설정이 기본 위치를 대신한다. 예를 들어 `spring.config.location`에 `classpath:/custom-config/,file:./custom-config/`을 설정하면, 검색 순서는 다음과 같다.
1. file:./custom-config/
2. classpath:custom-config/

`spring.config.additional-location`을 사용해 사용자정의 설정 위치를 지정하면, 기본 위치에 추가되어 사용된다. 추가 위치는 기본 위치 전에 검색된다. 예를 들어 `classpath:/custom-config/,file:./custom-config/`을 추가 위치로 설정하면, 검색 순서는 다음과 같다.
1. file:./custom-config/
2. classpath:custom-config/
3. file:./config/
4. file:./
5. classpath:/config/
6. classpath:/

이 검색순서를 이용하면 하나의 설정파일에 기본 설정 값들을 명시해서 선택적으로 다른 값으로 대체할 수 있다. 어플리케이션을 위한 기본값들은 기본 경로중 한 곳에 있는 `application.properties`파일(또는 `spring.config.name`으로 선택한 다른 기반이름<sub>basename</sub>)로 제공할 수있다. 이 기본값들은 실행시 사용자정의 경로 중 하나에 있는 다른 파일로 대체될 수 있다.

>시스템 프라퍼티 말고 환경변수를 사용하는 경우, 대부분의 운영체제는 키 이름을 `.`으로 구분하는 것을 허용하지 않는다. 그래서 `.` 대신 `_`을 사용한다. (예를 들어 `spring.config.name`은 대신 `SPRING_CONFIG_NAME`으로 한다.

>만약 어플리케이션이 컨테이너에서 실행된다면 jndi 프라퍼티(`java:comp/env` 안의)나 Servlet Context 초기파라미터가 환경변수나 시스템 프라퍼티 대신(또는 뿐만 아니라) 사용될 수 있다.

### 24.4 프로파일 관련 프라퍼티들
`application.properties` 파일에 더해서 프로파일 관련 프라퍼티들을 `application-{profile}.properties` 이름 관례를 사용해 정의할 수있다. `Environment`는 프로파일들이 설정되 있지 않은 경우 사용할 기본 프로파일들을(기본적으로 `[default]`) 가지고 있다. 프로파일들이 명시적으로 활성화 되있지 않으면 `application-default.properties`가 읽어들여 진다. 프로파일 관련 프라퍼티들은 표준 `application.properties`와 같은 위치로 부터 읽혀진다. 프로파일 관련 파일들이 package.jar 안에 있던 밖에 있던 프로파일 관련 아닌<sub>non-specific</sub>[^3] 파일들의 설정보다 항상 우선한다.

프로파일 관련 파일들이 여러 개일 경우 마지막 것이 이기는 전략이 적용된다.<sub>last-wins strategy</sub> 예를 들어 `spring.profiles.active` 프라퍼티에 의해 명시된 프로피일들은 `SpringApplication` API를 통해 설정된 것 뒤에 추가 되어서 우선권을 얻는다.

>`spring.config.location`에 어떤 파일이라도 기술했다면 그 파일들중 프로파일 관련 것들은 대상이 되지 않는다. 프로파일 관련 프라퍼티들도 같이 사용하려면 `spring.config.location`에 디렉토리를 사용해야 한다.

### 24.5 프라퍼티들에서 플레이스홀더들
`application.properties` 에 설정된 값들은 사용될 때 `Environment`로 들어간다. 그래서 전에 정의한 값들을 참조할 수있다. (예를 들어 시스템 프라퍼티들로 부터)
```
app.name=My App
app.description=${app.name} is a Spring Boot application
```
>이 기술은 이미 있는 Spring Boot 프라퍼티들의 이름을 짧게 만드는데 사용할 수있다. 자세한 것은 [73.4 "'짧은' Command Line 인수들 사용하기](http://localhost:4000/blog/2017/12/19/spring-boot-chapter09/#heading-734-짧은-command-line-인수들-사용하기)를 보시오.

### 24.6 Properties 대신 YAML 사용
[YAML](http://yaml.org/) 은 
### 24.6.1




### 24.7 Type-safe 설정 프라퍼티들


### 26.4 로그레벨들

#### 27.1.9 템플릿 엔진들

### 46.4 사용자 정의 스타터 만들기


[^1]: 부모/자식 관계의 여러 Context들을 사용하는 방식. 자식 Context는 부모 Context의 설정을 상속받아 사용할 수 있다.
[^2]: 객체를 생성할 때 Builder의 메소드들을 이용해 만들려는 instance의 속성 값을 설정하고 instance 생성메소드를 통해 instance를 만드는 방식. <br>예) `Instance inst = builder.setAttr1(attr1).setAttr2(attr2).setAttr3(attr3).build();` (역자주)
[^3]: application-myprofile.properties에 설정한 내용이 application.properties에 설정 된 것 보다 우선한다.