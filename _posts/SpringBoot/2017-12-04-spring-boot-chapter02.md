---
layout: post
title: "SpringBoot Reference번역 - Part02:시작하기(Getting Started)"
author: "김성환"
categories: [Springboot]
tags: [springboot, springboot Reference, 스프링 부트시작하기]
description: 스프링 부트 part2 번역. 시작하기(Getting started) 챕터
comments: true
---
버전 : 2.0.0.M7
저자: 
Phillip Webb, Dave Syer, Josh Long, Stéphane Nicoll, Rob Winch, Andy Wilkinson, Marcel Overdijk, Christian Dupuis, Sébastien Deleuze, Michael Simons, Vedran Pavić, Jay Bryant
번역: 김성환

__Copyright ⓒ 2012-2017__
Copies of this document may be made for your own use and for distribution to others, provided that you do not charge any fee for such copies and further provided that each copy contains this Copyright Notice, whether distributed in print or electronically.

[원본](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#getting-started)
* Kramdown table of contents
{:toc .toc}

# Part2 시작하기
만약 당신이 스프링 부트나 스프링을 처음 접한다면, 이 장을 먼저 읽으시오.  이번 장에서는 "무엇?", "어떻게?", "왜?" 라는 기본적인 질문들에 대한 답을 할 것이다.또 스프링 부트에 대한 소개와 함께 설치 가이드도 포함될 것이다. 그리고 나서 당신이 첫번째 스프링 부트 어플리케이션을 구현하는 것을 당신에게 알려줄 것이며 몇몇 핵심적인 원칙들에 대해 논의 할 것이다.

## 8. 스프링부트 소개

스프링부트는 실행하고자 하는 단독실행(stand-alone)이 가능한 제품 수준의 스프링 기반 어플리케이션 개발을 쉽게 만들어 준다. 우리는 스프링 플랫폼과 서드파티 라이브러리들의 고정된 견해를 취함으로써 개발자들이 최소한의 복잡함을 가지고 시작할 수 있도록 한다. 대부분의 스프링 부트 어플리케이션은 매우 적은양의 스프링 설정이 필요하다.

스프링 부트는 `java -jar` 또는 좀더 전통적인 war 배치에 의해 실행될 수 있는 자바 어플리케이션을 만드는데 사용된다. 또한 우리는 "Spring scripts" 실행하는 커맨드라인 툴(command line tool)을 제공한다.

우리의 주요 목적은 다음과 같다.
+ 모든 스프링 개발을 위한 급진적으로 빠르고 폭넓게 접근할수 있는 시작 경험을 제공한다.
+ 기본적인 고정 틀을 제공하되 요구사항이 기본설정과 달라질 때 빨리 적용할 수 있다.
+ 큰 프로젝트에서 일반적인 비기능적 특징들의 범위를 제공한다. 그런 비기능적 특징에는 내장형 서버, 보안, 측정, 헬스 체크,  외부설정등이 있다.
+ 무엇보다도 코트 생성과 XML 설정에 대한 요구를 절대 하지 않도록 한다.

## 9. 시스템 요구사항
스프링 부트 2.0.0.BUILD-SNAPSHOT 은 [Java 8](https://www.java.com) 버전과 스프링 프레임워크 5.0.2.RELEASE나 그 상위 버전을 요구한다. 빌드는 Maven 3.2 이상과 그래이들 4버전을 지원한다.

### 9.1 Servlet 컨테이너들
다음에 나오는 내장 서블릿 컨테이너를 지원한다.

|Name | Servelt 버전 |
|-|-|
|Tomcat 8.5|3.1|
|Jetty 9.4|3.1|
|Undertow 1.3|3.1|

스프링 부터 어플리케이션은 Servlet 3.0이상과 호환되는 어떤 컨테이너에도 배치할 수있다.

## 10. 스프링부트 설치(Install)
스프링 부트는 "대표적인" 자바개발 툴들에 의해 사용될 수있고 커맨드라인 툴을 이용해 설치 될 수 있다. 두 방식 모두 [Java SDK v1.8 또는 그 상위버전](https://www.java.com) 이 필요하다. 시작 하기 전에 설치된 Java의 버전을 체크해라. 체크 명령어는 다음과 같다.
```java 
$ java -version
```
[만약 당신이 자바언어로 개발을 처음 하거나 스프링 부트를 실험 해 보고 싶다면, Spring Boot CLI](#heading-102-spring-boot-cli-설치) 를 먼저 시도해 볼수 있을 것이다. 그렇지 않다면 먼저 "전형적" 설치 방법을 읽으시오.

### 10.1 자바 개발자를 위한 설치 방법
스프링 부트는 표준 자바 라이브러리와 같은 방식으로 사용할 수 있다. 그렇게 하기 위해서는 적절한 spring-boot=*.jar 파일을 classpath에 포함시킨다. 스프링 부트는 어떤 특별한 툴도 요구하지 않기 때문에 어떤 IDE나 텍스트 에디터든 사용할 수 있다. 또한 스프링 부트 어플리케이션은 특별한 것이 없기 때문에 실행하고 디버깅 하는 방식은 다른 Java Program 과 동일하다.

스프링 부트 jar 라이브러리들을 카피해서 사용할 수 있지만, 우리는 일반적으로 의존관계 관리를 지원하는 메이븐이나 그래이들과 같은 빌드 툴을 사용하는 것을 추천한다.

### 10.1.1 메이븐 설치
스프링 부트는 아파치 메이븐 3.2 또는 그 상위 버전와 호환된다. 만약 메이븐을 설치 하지 았았다면 [maven.apache.org](https://maven.apache.org/) 의 설명을 따라 설치한다.
> 많은 운영체체에서 패키지 관리자를 통해 Maven을 설치할 수 있다. OSX Homebrew에서는 `brew install maven` 을 사용한다. 우분투에서는 `sudo apt-get install maven`을 사용한다. 윈도우의 Chocolatey을 사용하는 경우 `choco install maven` 을 관리자 권한 프롬프트에서 실행 할 수있다.

스프링 부트 의존관계는 `org.springframework.boot` `groupId`를 사용한다. 일반적으로 당신이 만드는 POM 파일은 `spring-boot-starter-parent` 프로젝트를 상속하고 하나이상의 ["스타터들"](/blog/2017/12/04/spring-boot-chapter03/#heading-135-스타터-들starters)의 의존관계를 선언한다. 스프링 부트는 실행 Jar 생성을 위해 선택적으로 사용할 수있는 [메이븐 플러그인](#챕터8의 67번)을 제공한다.

다음 코드는 일반적인 `pom.xml` 파일이다.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>myproject</artifactId>
	<version>0.0.1-SNAPSHOT</version>

	<!-- 스프링 부트의 기본설정을 상속받는다. -->
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.0.BUILD-SNAPSHOT</version>
	</parent>

	<!-- 웹 어플리케이션을 위한 일반적인 의존관계를 추가한다. -->
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
	</dependencies>

	<!-- P실행 jar를 위한 플러그인 패키지 -->
	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

	<!-- 스프링 저장소를 추가한다. -->
	<!-- 만약 .RELEASE (최종 배포버전) 버전을 사용하면 저장소 추가는 필요 없다.-->
	<repositories>
		<repository>
			<id>spring-snapshots</id>
			<url>http://repo.spring.io/snapshot</url>
			<snapshots><enabled>true</enabled></snapshots>
		</repository>
		<repository>
			<id>spring-milestones</id>
			<url>http://repo.spring.io/milestone</url>
		</repository>
	</repositories>
	<pluginRepositories>
		<pluginRepository>
			<id>spring-snapshots</id>
			<url>http://repo.spring.io/snapshot</url>
		</pluginRepository>
		<pluginRepository>
			<id>spring-milestones</id>
			<url>http://repo.spring.io/milestone</url>
		</pluginRepository>
	</pluginRepositories>
</project>
```
> `spring-boot-starter-parent`는 스프링 부트를 사용하기 위한 최선의 방법이지만 모든 상황에 알맞지 않을 수 있다. 어떤 경우는 다른 부모 POM을 상속해야 할 때도 있고 기본 설정을 따르고 싶지 않을 수도 있다. 이런 경우는 [13.2.2 "부모 POM 없이 스프링 부트 사용하기"](/blog/2017/12/04/spring-boot-chapter03/#heading-1322-부모-pom-없이-스프링-부트-사용하기)를 보면 `import` 범위(scope)를 사용하는 다른 해결책이 설명되 있다.

### 10.1.2 그래이들(Gradle) 설치
스프링 부트는 그래이들 4와 호환된다. 만약 그래이들이 설치되 있지 않다면 [www.gradle.org](https://gradle.org/)의  설명을 따라 설치한다.

스프링 부트 의존관계는 `org.springframework.boot` `group`을 이용해 설정할 수 있다. 일반적으로 프로젝트에서는 한개 이상의 ["스타터들"](/blog/2017/12/04/spring-boot-chapter03/#heading-135-스타터-들starters)을 선언한다. 스프링 부트는 의존관계 선언을 간단히 처리해하고 실행 jar를 만들때 사용할 수 있는 유용한 [그래이들 플러그인](#챕터8의 68번)을 제공한다.

>**<font color="brown">Gradle Wrapper</font>**
>
>Gradle Wrapper는 프로젝트를 빌드해야 할때 그래이들을 "얻는" 훌륭한 방법을 제공한다. 이것은 작성된 코드의 빌드가 진행되도록 처리해주는 작은 스크립트와 라이브러리다. 자세한 것은 [docs.gradle.rog/4.2.1/userguide/gradle_wrapper.html](https://docs.gradle.org/4.2.1/userguide/gradle_wrapper.html)을 보시오.

다음 예제는 일반적인 `build.gradle` 파일이다.
```gradle
buildscript {
	repositories {
		jcenter()
		maven { url 'http://repo.spring.io/snapshot' }
		maven { url 'http://repo.spring.io/milestone' }
	}
	dependencies {
		classpath 'org.springframework.boot:spring-boot-gradle-plugin:2.0.0.BUILD-SNAPSHOT'
	}
}

apply plugin: 'java'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'

jar {
	baseName = 'myproject'
	version =  '0.0.1-SNAPSHOT'
}

repositories {
	jcenter()
	maven { url "http://repo.spring.io/snapshot" }
	maven { url "http://repo.spring.io/milestone" }
}

dependencies {
	compile("org.springframework.boot:spring-boot-starter-web")
	testCompile("org.springframework.boot:spring-boot-starter-test")
}
```

### 10.2 Spring Boot CLI 설치
Spring Boot CLI(Command Line Interface) 는 빠르게 스프링으로된 기본프로젝트 구조(prototype) 을 사용할 수 있도록 해주는 커맨드 라인툴이다. 이것은 많은 상용구코드(boilerplate code)대신 Java와 비슷한 구문의 [그루비-Groovy](http://groovy-lang.org/) 코드로 작성된 스크립트를 실행 시킨다.

스프링 부트로 일하기 위해 반드시 CLI를 사용할 필요는 없지만 이것은 스프링 어플리케이션을 시작할 수있는 가장 빠른 방법이다.

### 10.2.1 수동 설치
Spring CLI 배포판은 스프링 소프트웨어 저장소에서 다운로드 할 수있다.
+ [spring-boot-cli-2.0.0.BUILD-SNAPSHOT-bin.zip](https://repo.spring.io/snapshot/org/springframework/boot/spring-boot-cli/2.0.0.BUILD-SNAPSHOT/spring-boot-cli-2.0.0.BUILD-SNAPSHOT-bin.zip)
+ [spring-boot-cli-2.0.0.BUILD-SNAPSHOT-bin.tar.gz](https://repo.spring.io/snapshot/org/springframework/boot/spring-boot-cli/2.0.0.BUILD-SNAPSHOT/spring-boot-cli-2.0.0.BUILD-SNAPSHOT-bin.tar.gz)

[최신 스탭샷 배포판](https://repo.spring.io/snapshot/org/springframework/boot/spring-boot-cli/)도 사용가능하다.

다운로드 받은뒤 압축을 풀면 있는 [INSTALL.txt](https://raw.githubusercontent.com/spring-projects/spring-boot/master/spring-boot-project/spring-boot-cli/src/main/content/INSTALL.txt) 설명에 따라한다. 요약하면, `.zip`의 `bin/` 디렉토리안에  `spring` 스크립트 (윈도우용 `spring.bat`)이 있다. 그것 대신 `java -jar`를 이용햐 `.jar` 파일을 사용할 수있다. (이 스크립트 는 classpath가 정확히 설정되도록 도와준다.)

### 10.2.2 SDKMAN으로 설치
SDKMAN! (소프트웨어 개발 툴킷-sdk- 매니저)는 여러 버전의 다양한 SDK들(Groovy와 Spring Boot CLI를 포함한)을 관리하는데 사용된다.  [sdkman.io](http://sdkman.io/)에서 SDKMAN!받아 다음 명령문을 이용해 스프링부트를 설치하라.
```
$ sdk install springboot
$ spring --version
Spring Boot v2.0.0.BUILD-SNAPSHOT
```
만약 CLI를 위한 기능들을 개발중이고 그 개발중인 버전에 쉽게 접근하고 싶으면 다음 명령문을 사용하시오.
```
$ sdk install springboot dev /path/to/spring-boot/spring-boot-cli/target/spring-boot-cli-2.0.0.BUILD-SNAPSHOT-bin/spring-2.0.0.BUILD-SNAPSHOT/
$ sdk default springboot dev
$ spring --version
Spring CLI v2.0.0.BUILD-SNAPSHOT
```
앞의 설명은 `dev` 라고 불리우는 `spring`의 로컬 객체를 인스톨한다. 이것은 당신의 타겟빌드위치를 가르기고 있어서 스프링부트를 재 빌드할 때 마다 `spring`을 최신버전이 된다.
실행하면 다음 명령문어들을 볼 수 있다.
```
$ sdk ls springboot

==============================================================================
Available Springboot Versions
==============================================================================
> + dev
* 2.0.0.BUILD-SNAPSHOT

==============================================================================
+ - local version
* - installed
> - currently in use
==============================================================================

```

### 10.2.3 OSX Homebrew 설치
만약 당신이 Mac과 [Homebrew](https://brew.sh)를 사용하면 다음 명령문을 이용해 Spring Boot CLI를 설치하시오.
~~~
$ brew tap pivotal/tap
$ brew install springboot
~~~
Homebrew 는 `spring`을 `/user/local/bin` 에 설치 한다.
>만약 실행후 처리결과 문구(formula)를 볼 수 없다면 설치된 brew가 예전 버전일 수 있다. 이런 경우 `brew update`를 하고 다시 시도하라.

### 10.2.4 MacPorts 설치
만약 Mac과 [MacPorts](https://www.macports.org/) 를 사용하고 있다면 다음 명령어를 이용해 Spring Boot CLI를 설치할 수 있다.
```
sudo port install spring-boot-cli
```

### 10.2.5 명령라인(Command-Line) 완성
Spring Boot CLI는 [BASH](https://en.wikipedia.org/wiki/Bash_%28Unix_shell%29)나 [zsh](https://en.wikipedia.org/wiki/Z_shell) 쉘을 위한 명령어 자동완성을 제공하는 스크립트가 포함되어 있다. 그 스크립트(또한 이름이 `spring`이다.)를 어떤 쉘에서든 `source`로 실행하거나 개인 똔느 시스템 공통 bash 자동완성 초기화 경로에 놓으면 된다. 데비안 시스템에서는 시스템 공통 스크립트들은 `/shell-completion/bash`에 있고 그 디렉토리의 모든 스크립트들은 새로운 쉘이 시작할때 실행된다. 예를 들어 만약 SDKMAN!이 설치되어 있는상태에서 스크립트를 수동으로 실행하기 위해서는 다음 명령어를 사용한다.

```
$ . ~/.sdkman/candidates/springboot/current/shell-completion/bash/spring
$ spring <HIT TAB HERE>
  grab  help  jar  run  test  version
```

> 만약 당신이 Spring Boot CLI를 Homebrew나 MacPorts로 설치했다면 명령어 자동완성(command-line completion)은 쉘에 자동으로 등록된다.

### 10.2.6 Spring CLI 예제 빠르게 시작하기.
Spring Boot CLI 설치 테스트를 위해 다음 웹 어플리케이션을 사용할 수 있다. 시작하려면 app.groovy란 이름으로 파일을 생성하고 다음 코드를 넣는다.
```groovy
@RestController
class ThisWillActuallyRun {

@RequestMapping("/")
	String home() {
		"Hello World!"
	}
}
```
그리고 다음과 같이 shell에서 위 파일을 실행한다.
```
$ spring run app.groovy
```
>어플리케이션은 처음 실행은 위존라이브러리들을 다운 받기 때문에 느리다. 그 다음부터는 훨씬 빠르게 실행된다.

웹브라우져에서 [localhost:8080](http://localhost:8080)으로 연다. 그럼 다음과 같은 결과를 볼 것이다.
```
Hello World
```

### 10.3 이전 버전의 어플리케이션 업그레이드
만약 이전 버전의 스프링 부트를 업그레이 할 경우에는 [프로젝트 위키](https://github.com/spring-projects/spring-boot/wiki)에서 제공되는 "릴리즈 노트" 를 확인하시오. 거기에서 업그레이드 설명과 새로운 버전의 "새로운것과 주목할 만한 것" 기능 목록을 찾을 수 있다.

CLI 를 이용한 업그레이드는 적당한 패키지 관리 명령(예: `brew upgrade`)을 사용하거나 CLI를 수동으로 설치한 경우에는 [표준 설명](#heading-1021-수동-설치)의 설명을 따라 하고 `PATH` 환경변수의 예전버전 설정을 모두 지우고 새버전으로 변경한다.

## 11. 첫번째 스프링부트 어플리케이션 개발하기
이 부분에서는 스프링 부트의 몇몇 핵심 기능을 알려주는 간단한 "Hello World!" 웹어플리케이션을 어떻게 개발하는지 설명한다. 이 프로젝의 빌드는 대부분의 IDE가 지원하는 메이븐으로 할 것이다.
>[spring.io](https://spring.io/) 사이트에 스프링 부트를 사용하는 많은 "시작하기" [가이드들](https://spring.io/guides) 이 있다. 만약 특정 분야의 문제를 해결하려고 한다면 거기를 먼저 확인해 보시오. [start.spring.io](https://start.spring.io/)에 가서 Search for dependencies 에 "Web" 을 선택해라. 그러면 새로운 프로젝트 구조가 생성되고 거기에 [바로 스프링 코드](#heading-113-코드-작성하기)를 작성 할 수 있다. 좀더 자세한 것은 [Spring Initializr 문서](https://github.com/spring-io/initializr)를 확인하라.

시작하기 전에 Java와 Maven의 버전이 맞게 설치되었는지 확인 하기 위해 터미널을 열고 다음 명령문들을 실행하라.
```
$ java -version
java version "1.8.0_102"
Java(TM) SE Runtime Environment (build 1.8.0_102-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.102-b14, mixed mode)
```
```
$ mvn -v
Apache Maven 3.3.9 (bb52d8502b132ec0a5a3f4c09453c07478323dc5; 2015-11-10T16:41:47+00:00)
Maven home: /usr/local/Cellar/maven/3.3.9/libexec
Java version: 1.8.0_102, vendor: Oracle Corporation
```
>이 예제는 자체 폴더를 만들어야 한다. 이어지는 내용에서는 적당한 폴더를 만들고 그 폴더가 "현재 디렉토리" 라는 가정하에서 설명하겠다.

### 11.1 POM 생성하기
먼저 메이븐 `pom.xml` 파일을 생성한다. `pom.xml`는 프로젝트 빌드를 어떻게 할 지를 설명하기 위해 사용된다. 당신이 사용하는 아무 텍스트 에디터나 열고 다음 코드를 넣으시오.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>myproject</artifactId>
	<version>0.0.1-SNAPSHOT</version>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.0.BUILD-SNAPSHOT</version>
	</parent>

	<!-- 나중에 이 자리에 내용이 추가될 이다.-->

	<!-- (.RELEASE 버전의 라이브러리들을 사용하면 아래 설정을 필요없다.) -->
	<repositories>
		<repository>
			<id>spring-snapshots</id>
			<url>http://repo.spring.io/snapshot</url>
			<snapshots><enabled>true</enabled></snapshots>
		</repository>
		<repository>
			<id>spring-milestones</id>
			<url>http://repo.spring.io/milestone</url>
		</repository>
	</repositories>
	<pluginRepositories>
		<pluginRepository>
			<id>spring-snapshots</id>
			<url>http://repo.spring.io/snapshot</url>
		</pluginRepository>
		<pluginRepository>
			<id>spring-milestones</id>
			<url>http://repo.spring.io/milestone</url>
		</pluginRepository>
	</pluginRepositories>
</project>
```
위의 코드는 빌드를 실행 할 수있게 해준다. `mvn package` 를 실행해서 테스트 할 수 있다. (지금은 "jar will be empty - no content was marked for inclusion! jar가 비었습니다. - 어떤 내용도 포함되도록 표시되지 않았습니다." 라는 경고를 보게 될 텐데 무시해도 된다.)
>이 시점에서 IDE에 이 프로젝트를 import 할 수 있다. (대부분의 Java IDE들은 Maven 지원 빌트인을 제공한다.) 간단하게 하기 위해, 우리는 일반 텍스트 에디터를 사용해 이 예제를 계속 할 것이다.

### 11.3 코드 작성하기
이 어플리케이션을 마무리 히가 위해 하나의 Java 파일을 작성할 것이다. 기본적으로 Maven은 `src/main/java` 디렉토리의 소스들을 컴파일 한다. 그래서 그 구조의 폴더를 만들고나서 `src/main/java/Example.java`파일을 추가한 뒤 다음 코드를 작성한다.
```java
import org.springframework.boot.*;
import org.springframework.boot.autoconfigure.*;
import org.springframework.web.bind.annotation.*;

@RestController
@EnableAutoConfiguration
public class Example {

	@RequestMapping("/")
	String home() {
		return "Hello World!";
	}

	public static void main(String[] args) throws Exception {
		SpringApplication.run(Example.class, args);
	}
}
```
위의 코드에는 많은 내용이 있지는 않지만 상탕이 많은 부분이 진행된다. 우리는 문서의 다음 몇 섹션에서 중요한 부분들을 살펴볼 것이다.

### 11.3.1 @RestController 와 @RequestMapping 어노테이션
우리 `Example`의 첫번째 어노테이션은 `@RestController`이다. 이것은 ~stereotype~ 어노테이션이다. 이것은 코드를 읽는 사람들과 스프링을 위해 이 클래스를 특정 역할로 실행시키기 위한 힌트를 제공한다. 이 경우, 이 클래스는 Web `@Controller` 이다. 그래서 스프링은 들어오는 웹 요청을 처리할 때 사용하도록 고려한다.

`@RequestMapping` 어노테이션은 "전달-routing" 정보를 제공한다. 위의 설정은 스프링에게 `/` 경로의 HTTP 요청은 `home` 메소드와 연결하도록 한다.`@RestController` 어노테이션은 스프링이 결과 string(문자열)을 바로 호출자에게 전달하도록 만든다.
>`@RestController`와 `@RequestMapping` 어노테이션은 Spring MVC 어노테이션들이다. (스프링 부트 특유의 것은 아니다.) 좀더 자세한 사항은 스프링 참조 문서의 [MVC 섹션](https://docs.spring.io/spring/docs/5.0.2.RELEASE/spring-framework-reference/web.html#mvc)을 확인하라.

### 11.3.2 @EnableAutoConfiguration 어노테이션
두 번째는 클래스단위의 어노테이션인 @EnableAutoConfiguration 이다. 이 어노테이션은 추가된 의존관계 jar 라이브러리들을 바탕으로 스프링을 어떻게 설정할지에 대한 추측을 스프링 부트에게 알려준다. `spring-boot-starter-web`은 Tomcat과 Spring MVC 관련 jar들을 추가 하기 때문에 자동-설정(auto-configuration)은 이 프로젝트가 web appcliation이라고 추측하고 그에 맞춰 스프링을 설정한다.
> <font color="brown">**스타터들과 자동-설정**</font>
> 자동 설정은 "스타터"들에 맞춰 잘 작동하도록 설계되 있지만 그 두 개념이 직접 연결되 있지는 않다. 스타터 이외의 jar 의존관계를 선택하고 고를 수 있고 스프링 부트는 최선의 자동설정을 어플리케이션에 적용한다.

### 11.3.3 "main" 메소드
우리 프로젝트의 마지막 부분은 `main` 메소드이다. 이것은 어플리케이션의 시작점으로 사용되는 자바의 표준 메소드다. main 메소드에서 스프링 부트의 `SpringApplication` 클래스의 `run` 을 호출한다. `SpringApplication` 우리의 어플리케이션을 시동(bootstrap) 하는데 스프링과 자동설정된 톰켓 웹서버를 차례 차례 실행시킨다. `run`메소드의 매개변수에 `Example.class`를 전전달해서 기본 스프링 컴포넌트인 `SpringApplication`에게 알려준다. `args` 배열은 알려진 어떠한 커맨드라인 전달인자(Command-Line argument)라도 전달할 수 있다. `SpringApplication.run(Example.class, args);`

### 11.4 예제 실행하기
이 시점에서 이 어플리케이션은 실행될 것이다. `spring-boot-starter-parent` POM을 사용했으므로 어플리케이션을 실행하기 위한 `run` 골(goal)을 사용할 수있다. 어플리케이션을 실행하기 위해 `mvn spring-boot:run`을 프로젝트 루트 디렉토리에서 입력해라. 다음과 비슷 출력결과를 볼 수있을 것이다.
```
$ mvn spring-boot:run

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::  (v2.0.0.BUILD-SNAPSHOT)
....... . . .
....... . . . (log output here)
....... . . .
........ Started Example in 2.222 seconds (JVM running for 6.514)
```
웹브라우저에 [localhost:8080](http://localhost:8080) url을입력하면 다음과 같은 결과를 볼 수 있다.
```
Hello World!
```
`ctrl-c`를 눌려 어플리케이션을 빠져나온다.

### 11.5 실행가능 Jar 생성하기
우리는 제품을 완벽하게 실행할 수있는 실행가능 jars(Executable Jar)를 생성하는 것으로 이 예제를 마무리하겠다. 실행가능 jars ("fat jars" 라고도 한다.)는 컴파일된 클래스들과 어플리케이션을 실행하는데 필요한 모든 의존 jar들을 포함하고 있는 압축파일이다.

>**<font color="brown" size="2.5em">Executable Jars와 자바</font>**  
>자바는 jar에 포함된 jar파일들을 로드하는 표준방법을 제공하지 않는다. (jar 파일들 그 자신이 jar안에 포함되어져 있을 수 있다.) 만약 자체포함된(self-contained) 어플리케이션 배포를 생각하는 경우 이것은 문제가 발생할 소지가 있다.
>
>이 문제를 해결하기 위해 많은 개발자들은 "uber" jars를 사용한다. uber는 어플리케이션의 모든 의존 라이브러리의 클래스들을 하나의 압축으로 묶는다. 이 방식의 단점은 어플리케이션안에 어느 라이브러리들이 있는지 확인하기가 어렵다는 것이다. 이것도 만약 여러 jar들에서 같은 이름의 파일이 있는 경우 문제가 될 수있다.
>
>스프링 부트는 [다른 접근 법](/blog/2017/12/20-boot-appendix/#heading-e-실행가능-jar-포멧)을 사용해서 포함되 있는 jar에 직접 접근할 수있게 한다.

실행가능 Jar를 만들기 위해서는 `pom.xml`에 `spring-boot-maven-plugin`을 추가해야 한다. 그렇게 하기 위해 다음 라인들을 `dependencies` 아래 넣는다.
```xml
<build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
		</plugin>
	</plugins>
</build>
```
>`spring-boot-starter-parent` POM 은 `repackage` 골을 설정에 연결하기 위해 `<executions>`를 퐇함한다. 만약 부모 POM을 사용하지 않으면, 그 설정을 직접 선언해야 한다. 상세사항은 [플러그인문서](https://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/maven-plugin//usage.html)를 확인하시오.

커맨드 라인에서 `pom.xml` 파일을 저장하고 `mvn package`를 실행한다.
```
$ mvn package

[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building myproject 0.0.1-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] .... ..
[INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ myproject ---
[INFO] Building jar: /Users/developer/example/spring-boot-example/target/myproject-0.0.1-SNAPSHOT.jar
[INFO]
[INFO] --- spring-boot-maven-plugin:2.0.0.BUILD-SNAPSHOT:repackage (default) @ myproject ---
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
```
타겟 디렉토리를 보면 `myproject-0.0.1-SNAPSHOT.jar` 파일이 있을 것이다. 그 파일은 10MB 정도 크기이다. 만약 압축파일내의 구조를 보려면 다음과 같이 `jar tvf`를 사용할 수있다.
```
$ jar -tvf target/myproject-0.0.1-SNAPSHOT.jar
```
`target` 디렉토리에 `myproject-0.0.1-SNAPSHOT.jar.original` 파일도 볼수 있다. 이것은 스프링 부트에 의해 묶이기 전에 Maven이 생성한 최초의 jar 파일이다.

이 어플리케이션을 실행하기 위해, `java -jar` 명령어를 다음과 같이 사용한다.
```
$ java -jar target/myproject-0.0.1-SNAPSHOT.jar

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::  (v2.0.0.BUILD-SNAPSHOT)
....... . . .
....... . . . (log output here)
....... . . .
........ Started Example in 2.536 seconds (JVM running for 2.864)
```
이전과 같이 어플리케이션을 끝내려면 `ctrl-c`를 누른다.

## 12. 다음에 읽을 것
이 섹션이 스프링 부트의 기본적인 내용을 제공하고 당신이 자신의 어플리케이션들을 작성할 수 있도록 도왔기 희망한다. 만약 당신이 과제-지향(task-oriented)의 개발자라면 [spring.io](https://spring.io/)로 넘어가서 "스프링으로 어떻게 하지" 문제에 대한 분명한 해결책을 제시하는 몇몇 [시작하기](https://spring.io/guides/)항목을 확인하라. 우리는 또흔 스프링 부트에 맞는 ["How-TO"](/blog/2017/12/15/spring-boot-chapter07/#heading-7장-how-to-가이드) 문서도 제공한다.

[스프링 부트 저장소](https://github.com/spring-projects/spring-boot)에는 실행해 볼 수있는 [많은 샘플](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-samples) 들이 있다. 그 샘플들은 나모지 코드들과 독립적이다.(당신은 원하는 샘플을 빌드 또는 실행하기 위해 나머지를 빌드할 필요가 없다.)

그 외에 순서대로 다음 스텝인 [*Part 03 "스프링 부트 사용하기*](/blog/2017/12/04/spring-boot-chapter03/)를 읽어도 좋다. 만약 당신이 정말 참을성이 없다면 다 뛰어 넘은 뒤 [*스프링 부트 기능들*](/blog/2017/12/10/spring-boot-chapter04/#heading-part4-스프링부트-특징들)을 읽을 수 있다. 

 