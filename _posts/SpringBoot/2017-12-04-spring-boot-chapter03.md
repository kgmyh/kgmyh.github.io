---
layout: post
author: "김성환"
title: "SpringBoot Reference번역 - Part03:스프링 부트 사용하기"
description: 스프링 부트 part3 번역. 스프링 부트 사용하기
categories: [Springboot]
tags: [springboot, spring, springboot reference]
comments: true
---
버전 : 2.0.0.M7<br>
저자: 
Phillip Webb, Dave Syer, Josh Long, Stéphane Nicoll, Rob Winch, Andy Wilkinson, Marcel Overdijk, Christian Dupuis, Sébastien Deleuze, Michael Simons, Vedran Pavić, Jay Bryant
번역: 김성환

__Copyright ⓒ 2012-2017__

Copies of this document may be made for your own use and for distribution to others, provided that you do not charge any fee for such copies and further provided that each copy contains this Copyright Notice, whether distributed in print or electronically.

[원본](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#using-boot)

* Kramdown table of contents
{:toc .toc}

# Part3 스프링 부트 사용하기
이 섹션은 스프링 부트를 어떻게 사용하는지 좀더 상세한 내용들을 다룬다. 앞으로 빌드시스템, 자동-설정 그리고 어플리케이션 실행하기와 같은 주제를 다룰 것이다. 또한 스프링 부트의 모범 사례<sub>Best Practice</sub>들을 다룰 것이다. 스프링 부트는 당신이 사용하는 다른 라이브러리들과 특별히 다를 것이 없지만 개발 진행을 좀더 쉽게 할 수 있는 몇몇 추천되는 방법들이 있다.

만약 스프링 부트를 처음 접한다면 먼저 [시작하기](/blog/2017/12/04/spring-boot-chapter02/#heading-part2-시작하기)를 이 다음으로 넘어가기 전에 먼저 읽는 편이 좋다.

## 13. 빌드 시스템
스프링 부트 개발에는 [의존라이브러리 관리](#heading-131-의존성관리)를 지원하고 "Maven 중앙저장소"에 배포된 artifact(라이브러리들)사용이 가능한 빌드 시스템을 사용하는 것을 강력히 추천한다. 우리는 메이븐(Maven)이나 그래이들(Gradle)을 추천한다. 스프링 부트는 Ant와 같은 다른 빌드 시스템과도 연동이 되기는 하지만 특별히 지원하지 않는다. 

### 13.1 의존성관리
각각의 스프링 부트 배포판은 그것이 지원하는 선별된 의존성들의 리스트를 제공한다. 실제로 스프링 부트가 시스템에서 사용하는 의존 라이브러리들의 버전들을 관리하기 때문에 설정파일에 버전을 따로 지정할 필요가 없다. 그래서 스프링 부트를 업그레이드 하면 그 의존라이브러리들도 같은 방식으로 업그레이드 된다.
> 만약 필요하다면 버전을 명시할 수 있다. 그러면 스프링 부트가 추천하는 버전은 무시 된다.

선택된 목록에는 스프링 부트에서 사용되는 스프링 모듈뿐만 아니라 제3자 라이브러리들 <sub>Third party libraries</sub>들도 포함 되 있다. 이 목록은 메이븐과 그래이들 모두에서 사용할 수 있는 표준 [구성 명세서(Bills of Material-BOM ) (spring-boot-dependencies)](#heading-1322-부모-POM-없이-스프링-부트-사용하기) [^1] 에서 확인 할 수 있다.
> 스프링 부트의 각 배포판은 스프링 프레임워크의 기본 버전과 연결되 있다. 우리는 각 라이브러리의 버전을 직접 설정하지 **않는** 것을 **강력 추천한다.**

### 13.2 메이븐 (Maven)
메이븐 유저들은 `spring-boot-starter-parent` 프로젝트를 상속해서 필요한 기본설정들을 받을 수 있다. 부모 프로젝트는 다음 기능들을 제공한다.
+ 기본 컴파일 버전으로 Java 1.8
+ UTF-8 소스 인코딩
+ [의존성관리 섹션](#heading-131-의존성관리)에서 나왔듯, spring-boot-dependencies pom을 상속하면 공통 의존라이브러리들의 버전을 관리한다. 그래서 의존 라이브러리 설정시 버전을 생략할 수있게 해준다.
+ 적절하게 자원들을 걸러낸다.
+ 적절한 플러그인들을 설정한다. ([실행 플러그인](http://www.mojohaus.org/exec-maven-plugin), [Git 커밋 ID](https://github.com/ktoso/maven-git-commit-id-plugin) 와 [shade 플러그인](https://maven.apache.org/plugins/maven-shade-plugin/) [^2])
+ `application.properties`와 `application.yml`와 특정 프로필 파일 (예를 들어 `application-foo.properties`와 `application-foo.yml`)을 위한 적절한 자원 필터링을 한다.

주의할 것은 `application.properties`와 `application.yml` 파일은 `${..}` 스프링 스타일의 자리표시자(Placeholder)를 사용하기 때문에 메이븐 필터링에서는 `@...@` 자리표시자로 변경되었다. (이것은 메이븐 프라퍼티인 `resource.delimiter`에서 설정을 바꿀 수 있다.)

### 13.2.1 Starter Parent 상속
프로젝트를 `spring-boot-starter-parent` 를 상속받아  설정하려면 `parent` 설정을 다음과 같이 한다.
```xml
<!-- 스프링 부트로 부터 기본 설정을 상속한다.-->
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>2.0.0.BUILD-SNAPSHOT</version>
</parent>
```
>스프링 부트 버전은 위의 의존관계 설정에서만 하면 된다. 추가 스타터들을 설정할 경우 버전을 생략할 수 있다.

프로젝트에서 위에 설정으로 자동 셋업되는 의존관계 라이브러리가 아니라 특정 의존라이브러리를 쓸 경우 재정의 하면된다. 예를 들어 다른 Spring Data release train로 업그레이드 할 경우 다음을 `pom.xml`에 추가한다.
```xml
<properties>
  <spring-data-releasetrain.version>Fowler-SR2</spring-data-releasetrain.version>
</properties>
```
>지원되는 속성 목록은 [`spring-boot-dependencies` pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml)에서 확인하시오.

### 13.2.2 부모 POM 없이 스프링 부트 사용하기
모두가 `spring-boot-starter-parent` POM 을 상속받기 원하는 것은 아니다. 당신이 당신 회사에서 반드시 사용하도록 하는 표준 Parent를 사용해할 수도 있고 그냥 메이븐 설정을 명시적으로 선언하는 것을 좋아할 수도 있다.

당신이 `spring-boot-starter-parent`의 사용을 원하지 않으면 `scope=import` 의존을 사용해 의존관계 관리를 받을 수 있다. (단 이것은 플러그인 관리는 안된다.) 메이븐 설정은 다음과 같다.
```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <!-- 스프링 부트로 부터 dependency management를 import 한다. -->
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-dependencies</artifactId>
      <version>2.0.0.BUILD-SNAPSHOT</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```
위의 설정 예제에서 개별 의존라이브러리를 property 설정을 이용해 재정의 할 수 없다. 그렇게 하기 위해서는 프로젝트의 `dependencyManagement`안에 `spring-boot-dependencies` 설정을 선언하기 전에 원하는 의존관계의 설정을 먼저 추가해야 한다. 예를 들면, 다른 Spring Data release train로 업그레이드 하려면 `pom.xml`에 다음과 같이 요소를 추가한다.
```xml
<dependencyManagement>
  <dependencies>
    <!-- 스프링 부트가 제공하는 Spring Data release train 을 재정의 하기-->
    <dependency>
      <groupId>org.springframework.data</groupId>
      <artifactId>spring-data-releasetrain</artifactId>
      <version>Fowler-SR2</version>
      <scope>import</scope>
      <type>pom</type>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-dependencies</artifactId>
      <version>2.0.0.BUILD-SNAPSHOT</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```
>위 예제에서, 우리는 BOM[^2]을 추가했는데, 다른 의존 타입도 같은 방식으로 재정의 할 수 있다.

### 13.2.3 스프링 부트 메이븐 플러그인 사용하기
스프링 부트는 프로젝트를 실행가능 jar<sub>executable jar</sub>로 패키징하는 [스프링 부트 메이븐 플러그인](/blog/2017/12/17/spring-boot-chapter08/#heading-67-스프링-부트-메이븐-플러그인)을 포함하고 있다. 이 플러그인을 사용하기 위해서는 `<plugins>` 부분에 다음과 같이 plugin 설정을 추가한다.
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
>스프링 부트 스타터 부모 pom<sub>Spring Boot starter parent</sub>을 사용하고 있다면 그냥 plugin만 추가한다. 부모에 정의된 설정을 안 바꿀 경우에는 다른 설정은 필요 없다.

### 13.3 그래이들 (Gradle)
그래이들을 이용해 스프링 부트를 사용하는 것을 배우고자 한다면 다음링크의 스프링 부트의 그래이들 플러그인 문서를 참조하시오.
+ 참조 ([HTML](https://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/gradle-plugin//reference/html/)와 [PDF](https://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/gradle-plugin//reference/pdf/spring-boot-gradle-plugin-reference.pdf))
+ [API](https://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/gradle-plugin//api/)

### 13.4 앤트 (Ant)
스프링 부트는 Apache Ant+Ivy를 사용해 프로젝트를 빌드 할 수 있다. `spring-boot-antlib` "AntLib" 모듈을 이용하면 앤트로 실행가능 jar를 생성할 수 있다. 의존관계를 선언하기 위한 `ivy.xml` 파일은 다음 예제와 같이 작성한다.
```xml
<ivy-module version="2.0">
  <info organisation="org.springframework.boot" module="spring-boot-sample-ant" />
  <configurations>
    <conf name="compile" description="everything needed to compile this module" />
    <conf name="runtime" extends="compile" description="everything needed to run this module" />
  </configurations>
  <dependencies>
    <dependency org="org.springframework.boot" name="spring-boot-starter" rev="${spring-boot.version}" conf="compile" />
  </dependencies>
</ivy-module>
```
`build.xml`은 다음예제와 같이 작성한다.
```
<project
	xmlns:ivy="antlib:org.apache.ivy.ant"
	xmlns:spring-boot="antlib:org.springframework.boot.ant"
	name="myapp" default="build">

	<property name="spring-boot.version" value="2.0.0.BUILD-SNAPSHOT" />

	<target name="resolve" description="--> retrieve dependencies with ivy">
		<ivy:retrieve pattern="lib/[conf]/[artifact]-[type]-[revision].[ext]" />
	</target>

	<target name="classpaths" depends="resolve">
		<path id="compile.classpath">
			<fileset dir="lib/compile" includes="*.jar" />
		</path>
	</target>

	<target name="init" depends="classpaths">
		<mkdir dir="build/classes" />
	</target>

	<target name="compile" depends="init" description="compile">
		<javac srcdir="src/main/java" destdir="build/classes" classpathref="compile.classpath" />
	</target>

	<target name="build" depends="compile">
		<spring-boot:exejar destfile="build/myapp.jar" classes="build/classes">
			<spring-boot:lib>
				<fileset dir="lib/runtime" />
			</spring-boot:lib>
		</spring-boot:exejar>
	</target>
</project>
```
만약 `spring-boot-antlib` 모듈을 사용자 않을 경우, "How-to" 파트의 [spring-boot-antlib를 사용하지 않고 앤트를 이용해 실행가능 압축파일 빌드하기](/blog/2017/12/19/spring-boot-chapter09/#heading-859-spring-boot-antlib를-사용하지-않고-앤트-이용해-실행가능압축파일-빌드하기) 를 확인한다.

### 13.5 스타터 들(Starters)

스타터들은 당신의 어플리케이션에 포함시킬 수 있는 간편한 의존관계 설명자들이다. 샘플코드들을 뒤져 보거나 가지고 있는 의존관계 설명서(설정파일)들을 뒤져 복사 붙이기를 하지 않아도 필요한 모든 스프링과 관련 기술들을 한번에 가져올 수 있다.  예를 들어, 스프링과 데이터베이스 접근을 위해서 JPA를 사용해 프로젝트를 시작하길 원하면,  `spring-boot-starter-data-jpa` 의존을 프로젝트에 포함시키면 된다.

스타터들은 프로젝트를 시작시 필요하고 프로젝트를 빠르고 안정적으로 실행시키기 위한 많은 의존라이브러리들을 포함하고 있다. 그리고 의존관계들의 전이도 지원한다.[^3]
><strong>스타터 이름형식</strong><br>
> 모든 **공식** 스타터들은 비슷한 이름 패턴을 따른다. `spring-boot-starter-*` 형식이고 `*`에 어플리케이션의 특정 타입이 들어간다. 이 이름 구조는 필요한 스타터를 찾는 것을 도와준다. 메이븐과 연동하는 많은 IDE들은 이름으로 의존라이브러리들을 찾을 수 있게 한다. 예를 들어 이클립스나 STS에 적절한 플러그인이 설치 되있으면 POM 에디터에서 `ctrl-space` 치고 "spring-boot-starter"를 입력하면 나머지를 완성할 수 있는 목록이 나온다. [사용자 정의 스타터 만들기](/blog/2017/12/10/spring-boot-chapter04/#headeing-464-사용자-정의-스타터 만들기)섹션에서 설명한 대로, 비공식 스타터들(스프링 부트에서 제공하는 것이 아니니 3자 제공자가 만든)들은 스프링 부트 아티팩트와 처럼 `spring-boot`로 시작 하지 않을 수 있다. 3자 제공<sub>Third-party</sub> 스타터들은 프로젝트의 이름으로 시작하는 경우가 많다. 예를 들어, `thirdpartyproject` 라는 이름의 3자 제공 스타터 프로젝트가 있다면 일반적으로 `thirdpartyproject-spring-boot-starter` 로 이름을 정한다.

다음은 `org.springframework.boot` group[^4] 의 스프링 부트가 제공하는 스타터들이다.

**표 13.1. 스프링 부트 어플리케이션 스타터들**

|이름|설명|POM|
|----|---|---|
|`spring-boot-starter`|핵심 스타터로 자동설정을 지원과 로깅과 YAML을 포함하고 있다.|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter/pom.xml)|
|`spring-boot-start-activemq`|아파치 ActiveMQ를 사용한 JMS 메세징을 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-activemq/pom.xml)|
|`spring-boot-starter-amqp`|스프링 AMQP와 Rabbit MQ를 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-amqp/pom.xml)|
|`spring-boot-starter-aop`|Spring AOP와 AspectJ을 이용한 관점지향 프로그래밍을 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-aop/pom.xml)|
|`spring-boot-starter-artemis`|아파치 Artemis를 사용한 JMS 메세징을 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-artemis/pom.xml)|
|`spring-boot-starter-batch`|스프링 배치를 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-batch/pom.xml)|
|`spring-boot-starter-cache`|스프링 프레임워크의 캐싱을 지원하기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-cache/pom.xml)|
|`spring-boot-starter-cloud-connectors`|Cloud Foundry나 Heroku와 같은 클라우드 플랫폼 서비스에 연결을 간단하게 해주는 스프링 클라우드 연결자 사용을 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-cloud-connectors/pom.xml)|
|`spring-boot-starter-data-cassandra`|카싼드라에서 배포한 데이터와 스프링 데이터 카산드라 사용을 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-data-cassandra/pom.xml)|
|`spring-boot-starter-data-cassandra-reactive`|카싼드라에서 배포한 데이터와 스프링 데이터 카산드라 Reactive 사용을 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-data-cassandra-reactive/pom.xml)|
|`spring-boot-starter-data-couchbase`|문서지향 데이터베이스인 Couchbase와 Spring Data Couchbase를 사용하기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-data-couchbase/pom.xml)|
|`spring-boot-starter-data-couchbase-reactive`|문서지향 데이터베이스인 Couchbase와 Spring Data Couchbase Reactive를 사용하기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-data-couchbase-reactive/pom.xml)|
|`spring-boot-starter-data-elasticsearch`|Elasticsearch 검색과 분석엔진과 Spring Data Elasticsearch 을 사용하기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-data-elasticsearch/pom.xml)|
|`spring-boot-starter-data-jpa`|Spring Data JPA와 하이버네이트(Hibernate)를 사용하기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-data-jpa/pom.xml)|
|`spring-boot-starter-data-ldap`|Spring Data LDAP를 사용하기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-data-ldap/pom.xml)|
|`spring-boot-starter-data-mongodb`|문서지향 데이터베이스인 몽고디비와 Spring Data MongoDB 사용을 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-project/spring-boot-starters/spring-boot-starter-data-mongodb/pom.xml)|
|`spring-boot-starter-data-mongodb-reactive`|문서지향 데이터베이스인 몽고디비와 Spring Data MongoDB Reactive 사용을 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-data-mongodb-reactive/pom.xml)|
|`spring-boot-starter-data-neo4j`|Neo4j 그래프 데이터베이서와 Spring Data Neo4j를 사용하기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-data-neo4j/pom.xml)|
|`spring-boot-starter-data-redis`|Spring Data Redis와 Lettuce client를 이용해 key-value 데이터 저장소인 Redis를 사용하기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-data-redis/pom.xml)|
|`spring-boot-starter-data-redis-reactive`|Spring Data Redis reactive와 Lettuce client를 이용해 key-value 데이터 저장소인 Redis를 사용하기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-data-redis-reactive/pom.xml)|
|`spring-boot-starter-data-rest`|Spring Data REST를 사용해 REST로 Spring Data 리포지토리들을 나타나도록 하기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-data-rest/pom.xml)|
|`spring-boot-starter-data-solr`|아파치 Solr 검색 플렛폼을 Spring Data Solr로 사용하기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-data-solr/pom.xml)|
|`spring-boot-starter-freemarker`|FreeMarker View를 이용해 MVC 웹 어플리케이션을 구축하기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-freemarker/pom.xml)|
|`spring-boot-starter-groovy-templates`|Groovy Templates View 이용해 MVC 웹 어플리케이션을 구축하기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-groovy-templates/pom.xml)|
|`spring-boot-starter-hateoas`|Spring MVC와 Spring HATEOAS를 이용해 하이퍼미디아 기반의 RESTful 웹어플리케이션 구축을 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-hateoas/pom.xml)|
|`spring-boot-starter-integration`|Spring Integration 사용을 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-integration/pom.xml)|
|`spring-boot-starter-jdbc`|Tomcat JDBC 커텍션 풀을 이용해서 JDBC를 사용하기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-jdbc/pom.xml)|
|`spring-boot-starter-jersey`|JAX-RS와 Jersey를 사용해 RESTful 웹어플리케이션을 구축하기 위한 스타터. `spring-boot-starter-web`의 대체제.|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-jersey/pom.xml)|
|`spring-boot-starter-jooq`|JOOQ를 이용해 SQL 데이터베이스에 접근하기 위한 스타터. `spring-boot-starter-data-jpa` 나 `spring-boot-starter-jdbc`의 대체제|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-jooq/pom.xml)|
|`spring-boot-starter-json`|json을 읽고 쓰기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-json/pom.xml)|
|`spring-boot-starter-jta-atomikos`|Atomikos를 이용해 JTA 트랜잭션 처리를 하기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-jta-atomikos/pom.xml)|
|`spring-boot-starter-jta-bitronix`|Bitronix를 이용해 JTA 트랜잭션 처리를 하기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-jta-bitronix/pom.xml)|
|`spring-boot-starter-jta-narayana`|스프링부트 Narayana JTA 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-jta-narayana/pom.xml)|
|`spring-boot-starter-mail`|Java Main과 스프링 프레임워크의 email sending 지원을 사용하기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-mail/pom.xml)|
|`spring-boot-starter-mustache`|Mustache View를 이용해 웹어플리케이션을 구축기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-mustache/pom.xml)|
|`spring-boot-starter-quartz`|스프링 부트 Quarts 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-quartz/pom.xml)}
|`spring-boot-starter-security`|스프링 시큐ㅜ리티를 사용하기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-security/pom.xml)|
|`spring-boot-starter-test`|JUnit, Hamcrest그리고 Mockito가 포함된 라이브러리를 이용해 스프링 부트 어플리케이션을 테스트 하기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-test/pom.xml)|
|`spring-boot-starter-thymeleaf`|Thymeleaf View를 사용해 MVC 웹 어플리케이션을 구축하기 위한 스타터.|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-thymeleaf/pom.xml)|
|`spring-boot-starter-validation`|Hibernate Validator로 Java 빈 검증(Validation)을 하기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-validation/pom.xml)|
|<span id="spring-boot-starter-web">`spring-boot-starter-web`</span>|스프링 MVC를 이용해 RESTful을 포함한 웹 어플리케이션을 구축하기 위한 스타터. 기본 내장 컨테이너로 톰켓을 사용한다.|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-web/pom.xml)|
|`spring-boot-starter-web-services`|스프링 웹서비스를 사용하기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-web-services/pom.xml)|
|`spring-boot-starter-webflux`|스프링 프레임워크의 반응형 웹 지원(Reactive Web support)을 이용해 WebFlux 어플리케이션을 구축하기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-webflux/pom.xml)|
|`spring-boot-starter-websocket`|스프링 프레임워크의 웹소켓 지원(WebSocket supoort)를 이용해 WebSocket 어블리케이션을 구축하기 위한 스타터|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-websocket/pom.xml)|

어플리케이션 스타더들에 더해서, 다음 스타터들은 [제품준비완료](/blog/2017/12/11/spring-boot-chapter05/#heading-v-spring-boot-actuator-제품준비완료-기능들) 기능들을 추가하는데 사용된다.

**표 13.2. 스프링 부트 제품 스타터(production starters)**

|이름|설명|Pom|
|-|-|-|
|`spring-boot-starter-actuator`|스프링 부트의 Actuator를 사용하기 위한 스타터. 스프링 부트의 Actuator는 어플리케이션 관리와 모니터링을 도와주는 생산 준비 기능을 제공한다.|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-actuator/pom.xml)|

마지막으로 스프링 부트는 당신이 특정 기술적 측면을 배제하거나 변경하고자 할때 사용할 수 있는 다음과 같은 스타터들을 포함하고 있다.

 ** 표 13.3 스프링 부트 기술적 스타터들 **
 
 |이름|설명|Pom|
 |-|-|-|
 |`spring-boot-starter-jetty`|내장 서블릿 컨테이너로 Jetty를 사용하기 위한 스타터. [`spring-boot-starter-tomcat`](#spring-boot-starter-tomcat)를 대신한다.|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-jetty/pom.xml)|
|`spring-boot-starter-log4j2`|로깅을 위해 Log4j2를 사용하기 위한 스타터. [`spring-boot-starter-logging`](#spring-boot-starter-logging)을 대신한다.|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-log4j2/pom.xml)|
|<span id="spring-boot-starter-logging">`spring-boot-starter-logging`</span>|로그백(Logback)을 이용한 로깅을 위한 스타터. 기본 로깅 스타터이다.|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-logging/pom.xml)|
|`spring-boot-starter-reactor-netty`|Reactor Netty를 내장된 반응형 HTTP 서버로 사용할 경우 이용하는 스타터.|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-reactor-netty/pom.xml)|
|<span id="spring-boot-starter-tomcat">`spring-boot-starter-tomcat`</span>|내장형 서블릿 컨테이너로 톰켓을 사용할 경우를 위한 스타터. [`spring-boot-starter-web`](#spring-boot-starter-web)의 기본 서블릿 컨테이너 스타터이다.|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-tomcat/pom.xml)|
|`spring-boot-starter-undertow`|내장형 서블릿 컨테이너로 [언더토우(Undertow)](http://undertow.io/)를 사용하기 위한 스타터. [`spring-boot-starter-tomcat`](#spring-boot-starter-tomcat)의 대신한다.|[Pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/spring-boot-starter-undertow/pom.xml)|

>스프링 공동체에서 공헌하는 추가적인 스타터들은 깃허브의 `spring-boot-starters` 모듈의 [README 파일](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/README.adoc) 을 확인하시오.

## 14. 코드 구조
스프링 부트는 작업을 위한 특별한 코드 배치을 요구하지 않지만 몇가지 모범 사례<sub>Best Practices</sub>들이 있다.

### 14.1 "default" 패키지 사용하기
클래스에 `package` 선언문이 없으면 "디폴트 패키지(default package)" 안에 있다고 말한다. "디폴트 패키지" 는 일반적으로 사용하지 말라고 이야기 하며 피하는 것이 좋다. 스프링 부트에서는 디폴트 패키지를 이용해 빈을 만들고  `@ComponentScan`, `@EntityScan` 또는 `@SpringBootApplication` 어노테이션을 사용하면 모든 jar내의 모든 클래스를 읽기 때문에 문제를 발생시킬 수 있다.
>자바의 패키지 이름 관례를 따르고 도메인 이름을 꺼꾸로 사용해서 만드는 것을 추천한다. (예를들어 `com.example.project`)

### 14.2 Main Application 클래스 위치
Main Applcation 클래스는 모든 클래스들의 최상위인 루트 패키지(Root Package)에 위치시키는 것을 추천한다. `@EnableAutoConfiguration` 어노테이션은 보통 Main 클래스에 위치시킨다. 그리고 특정 아이템들을 찾기 위한 기본 "검색 패키지"를  묵시적으로 정의한다. 예를 들어, JPA 어플리케이션을의 경우,  `@EnableAutoConfiguration` 어노테이션이 설정된 클래스의 패키지는 `@Entity` 아이템들을 찾을 때 사용된다.

또 루트 패키지를 사용하면 `@ComponentScan` 어노테이션의 `basePackage` 속성을 작성하지 않고 사용할 수 있다. 또 만약 Main 클래스가 루트패키지에 있다면, `@SpringBootApplication` 어노테이션을 사용할 수 있다.

다음은 전형적인 배치(레이아웃)형태를 보여준다.
```
com
 +- example
     +- myapplication
         +- Application.java
         |
         +- customer
         |   +- Customer.java
         |   +- CustomerController.java
         |   +- CustomerService.java
         |   +- CustomerRepository.java
         |
         +- order
             +- Order.java
             +- OrderController.java
             +- OrderService.java
             +- OrderRepository.java
```
`Application.java` 파일에 `main` 메소드선언하고 `@Configuration` 을 선언한다. 코드는 다음과 같다.

```java
package com.example.myapplication;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@EnableAutoConfiguration
@ComponentScan
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}
}
```
## 15. 설정 클래스 (Configuration Classes)

스프링 부트는 Java-기반 설정의 사용을 추천한다. XML 소스로 `SpringApplication`을 설정하는 것도 가능하지만, 일반적으로 주요 소스로 하나의 `@Configuration`클래스를 가지는 것을 추천한다. 보통 `main` 메소드를 정의한 클래스가 기본 `@Configuration` 가 되기에 좋은 후보이다.
> 인터넷 상에서 공개된 많은 스프링 설정 예제이 XML 설정을 사용하고 있다. 가능하다면, 동일한 설정을 한  Java-기반 설정도 같이 사용하도록 한다. `Enable*` 어노테이션을 검색하는 것이 좋은 시작점이 될 수 있다.

### 15.1 중요한 추가설정 클래스들 가져오기(Import)
모든 `@Configuration`을 클래스 하나에 다 넣을 필요는 없다. `@Import` 어노테이션은 추가 설정클래스들을 가져오기(import) 위해 사용된다. 또다른 방법으로, `@ComponentScan`을 사용해서 `@Configuration`클래스를 포함한 모든 스프링 컴포넌트들을 자동으로 선택할 수 있다.

### 15.2 XML 설정 가져오기

반드시 XML 기반의 설정을 사용해야 한다 한다면  일단  `@Configuration` 클래스를 만들고 시작한 뒤 `@ImportResource` 어노테이션을 사용해서 XML 설정파일을 읽어 적재<sub>load</sub>할 수 있다.

## 16. 자동 설정 (Auto-Configuration)
스프링 부트의 자동 설정은 스프링 어플리케이션에 추가된 jar 의존관계에 기반해서 자동 설정을 시도한다. 예를 들어, `HSQLDB`가 클래스 패스 상에 있고 데이터베이스 연결 빈들에 어떤 설정도 하지 않은 경우, 스프링 부트는 인메모리 데이터 베이스[^5] 를 자동 설정한다.

`@EnableAutoConfiguration`이나 `@SpringBootApplication` 어노테이션을 `@Configuration` 중 하나에 추가해야 자동-설정을 사용할 수 있다.

>당신은 `@EnableAutoConfiguration` 어노테이션만 추가하면 된다. 우리는 그것을 기본 `@Configuration` 클래스에 추가하는 것을 추천한다.

### 16.1 서서히 자동 설정을 대체하기
자동설정은 비 침습성이다.[^6] . 자동으로 설정된 것의 일부분을 대체하기 위한 자신만의 설정의 정의를 언제라도 할 수 있다. 예를 들어 만약 당신이 자신만의 `DataSource` 빈을 추가한다면, 기본 내장 데이터베이스 지원은 사라진다.

만약 현재 적용된 자동-설정이 무엇이며 무슨이유로 적용됐는지 알아보려면, 어플리케이션을 시작할때 `--debug` 옵션을 준다. 그러면 선택된 핵심로그의 디버그 로그들을 사용할 수 있게 되고 콘솔에 조건들의 보고서를 기록한다.

### 16.2 특정 자동-설정 클래스들을 사용하지 못하게 하기
만약 적용되지 않았으면 하는 자동-설정 클래스들이 있으면, `@EnableAutoConfiguration`의 exculde 솔성을 사용할 수 있다. 코드는 다음과 같다.
```java
import org.springframework.boot.autoconfigure.*;
import org.springframework.boot.autoconfigure.jdbc.*;
import org.springframework.context.annotation.*;

@Configuration
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
public class MyConfiguration {
}
```
만약 그 클래스가 클래스패스 상에 없으면, `@EnableAutoConfiguration`의 `excludeName` 속성을 사용할 수 있다. 그래서 전체 이름(Fully Qualified Name) 을 대신 명시할 수 있다. 마지막으로 `spring.autoconfigure.exclude` 프라퍼티를 사용해 자동-설정 클래스들 목록을 제어할 수도 있다.
>어노테이션레벨과 프라퍼티를 사용하는 두가지 방식 모두를 이용해 제외를 정의할 수 있다.

## 17. 스프링 빈들과 의존관계 주입 
빈들을 정의 하고 의존성을 주입 받기위해 당신은 어떠한 표준 스프링 프레임워크 기술이든지 사용할 수 있다. 간단하게, `@ComponentScan`(빈을 찾기 위해 사용)과 `@Autowired`(생성자 주입을 하기위해 사용) 을 이용하여 처리할 수 있다.

만약 코드 구조가 위에서 제안한 구조를 따랐다면(Application를 루트 패키지에 위치시킴), `@ComponentScan`을 어떠한 전달인자 값 없이 추가할 수 있다. 어플리케이션의 모든 컴포넌트들(`@Component`, `@Service`, `@Repository`, `@Controller` 등)이 스프링 빈으로 자동으로 등록된다.

다음 예제는 `RiskAssessor` 빈을 생성자 주입을 통해 얻는 `@Service` 빈을 보여준다.
```java
package com.example.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class DatabaseAccountService implements AccountService {

	private final RiskAssessor riskAssessor;

	@Autowired
	public DatabaseAccountService(RiskAssessor riskAssessor) {
		this.riskAssessor = riskAssessor;
	}

	// ...

}
```
만약 빈이 한개의 생성자를 가지고 있으면 `@Autowired`를 생략 할 수 있다. 다음은 그 코드의 예를 보여준다.
```java
@Service
public class DatabaseAccountService implements AccountService {

	private final RiskAssessor riskAssessor;

	public DatabaseAccountService(RiskAssessor riskAssessor) {
		this.riskAssessor = riskAssessor;
	}

	// ...

}
```
>`final` field인 `riskAssessor`에 생성자 주입을 사용하는 것은, 나중에 바꿀수 없다는 것을 나타낸다.

## 18. @SpringBootApplication 어노테이션 사용하기
많은 스프링 부트 개발자들이 항상 메인클래스에 `@Configuration`, `@EnableAutoConfiguration`과 `@ComponentScan` 어노테이션을 추가한다. 이 어노테이션들은 너무 자주 같이 사용되기 때문에(특히 당신이 위의 [모범사례](#heading-14-코드-구조)따라왔다면) 스프링 부트는 그것들을 대신할 수 있는 편리한 `@SpringBootApplication`을 제공한다.

`@SpringBootApplication` 어노테이션은 `@Configuration`, `@EnableAutoConfiguration`과 `@ComponentScan`을 기본속성으로 사용하는 것과 동일하다. 다음은 그 예제를 보여준다.
```java
package com.example.myapplication;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication // @Configuration @EnableAutoConfiguration @ComponentScan 들을 선언한 것과 같다.
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}

}
```
>`@SpringBootApplication`은 `@EnableAutoConfiguration`과 `@ComponentScan`의 속성을 변경할 수 있는 별칭(Alias)를 제공한다.

## 19. 어플리케이션 실행하기
어플리케이션을 jar로 패키징하고 내장 HTTP 서버를 사용했을 때 가장 큰 이점 중 하나는 어플리케이션을 다른 것과 같은 방법으로  실행시킬 수 있다는 것이다. 스프링 부트 어플리케이션을 디버깅하는 것 또한 쉽다. 어떤 특별한 IDE 플러그인이나 확장도 필요 없다.
>이 섹션에서는 jar 기반 패키징만 다룰 것이다. 만약 war 파일을 패키징으로 선택했다면 당신의 서버와 IDE 문서를 참조해야 한다.

### 19.1 IDE에서 실행하기
간단한 Java 어플리케이션과 마찬가지로 스프링 부트는 IDE를 이용해 실행할 수 있다. 그러나 프로젝트 가져오기(import)를 먼저 해야한다. 가져오기 단계는 IDE나 빌드 시스템마다 다양하다. 대부분 IDE들은 메이븐 프로젝트를 바로 가져오온다. 예를 달어 이클립스 유저는 `File` 메뉴에서 `Import...`→`Existing Maven Projects` 를 선택해서 할 수 있다.

만약 프로젝트를 IDE로 가져오기를 바로 할 수 없는 경우, 빌드 플러그인으로 IDE 메타데이터를 생성할 수 있다. 메이븐은 [이클립스](https://maven.apache.org/plugins/maven-eclipse-plugin/)와 [IDEA](https://maven.apache.org/plugins/maven-idea-plugin/)를 위한 플러그인을 포함하고 있다. 그래이들은 [다양한 IDE들](https://docs.gradle.org/4.2.1/userguide/userguide.html)을 위한 플러그인들을 제공한다.
>만약 잘못해서 웹어플리케이션을 두번 실행하면 "Port already in use(포트가 이미 사용중입니다.)" 에러를 보게 된다. STS 사용자는 실행중인 프로그램을 확실히 끝내고 시작 하기 위해 `Run` 버튼 보다 `Relaunch` 버튼을 사용할 수 있다.

### 19.2 패키지화한 어플리케이션으로 실행하기
만약 당신이 스프링 부트 메이븐 또는 그래이들의 플러그인을 이용해 실행가능 jar를 사용한다면, 어플리케이션을 `java -jar` 를 사용해 다음과 같이 실행할 수 있다.
```
$ java -jar target/myapplication-0.0.1-SNAPSHOT.jar
```
묶인(packaged) 어플리케이션을 자바 원격 디버깅 (remote debugging support enabled)을 이용해 실행하는 것 또한 가능하다. 그렇게 하면 어플리케이션에 디버거를 붙일 수 있다. 다음은 그 예제이다.
```
$ java -Xdebug -Xrunjdwp:server=y,transport=dt_socket,address=8000,suspend=n -jar target/myapplication-0.0.1-SNAPSHOT.jar
```
### 19.3 메이븐 플러그인 사용하기
스프링 부트 메이븐 플러그인은 어플리케이션을 빠르게 컴파일하고 실행하는 `run` 골(goal)을 포함한다. 어플리케이션은 IDE에서처럼 분해된 형태[^7]로 실행된다.
```
$mvn spring-boot:run
```
`MAVEN_OPTS` 운영체제 환경변수를 사용하고자 한다면 다음과 같이 실행한다.
```
$ export MAVEN_OPTS=-Xmx1024m
```
### 19.4 그래이들 플러그인 사용하기
스프링 부트 그래이들 플러그인도 어플리케이션을 분해된 형태로 실행할 때 사용할 수 있는 `bootRun` 태스크를 포함하고 있다. `bootRun` 태스크는 `org.springframework.boot`와 `java` 플러그인들을 적용할 때 마다 추가된다. 실행구문은 다음과 같다.
```
$ gradle bootRun
```
`JAVA_OPTS` 운영체제 환경변수를 사용하고자 한다면 다음과 같이 실행한다.
```
$ export JAVA_OPTS=-Xmx1024m
```
### 19.5 Hot Swapping<sub>핫 스와핑</sub> [^8] 
스프링 부트 어플리케이션들은 일반 자바 어플리케이션이므로 JVM 핫 스와핑 바로 사용할 수 있다. JVM 핫 스와핑은 대체될 때  bytecode에 약간의 제한이 있다. 좀더 완벽한 해결책으로 [JReble](http://zeroturnaround.com/software/jrebel/)을 사용할  수 있다.

`spring-boot-devtools` 모듈 빠른 어플리케이션 재시작을 위한 지원을 포함하고 있다. 자세한 내용은 아래 [챕터20, 개발자 툴들](#heading-20-개발자-툴들)과 [핫스와핑](/blog/2017/12/19/spring-boot-chapter09/#heading-84-핫스와핑)을 보시오.

## 20. 개발자 툴들
스프링 부트는 어플리케이션 개발을 좀 더 편하게 만들어주는 추가 툴들을 포함하고 있다.  `spring-boot-devtools` 모듈은  개발때 사용할 수 있는 추가적인 기능들을 제공한다. 필요한 어떤 프로젝트에도 추가될 수 있다. Devtool들 지원을 포함시키려면 빌드의 모듈 의존(Dependency)울 다음과 같이 추가하면 된다.

**메이븐**
```xml
<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-devtools</artifactId>
		<optional>true</optional>
	</dependency>
</dependencies>
```
**그래이들**
```gradle
dependencies {
	compile("org.springframework.boot:spring-boot-devtools")
}
```
>개발자 툴은 완제품 어플리케이션<sub>Full Packaged application</sub>을 실행할 때는 자동적으로 비활성화 된다. 만약 어플리케이션을 `java -jar`를 사용 또는 특별한 클래스로더로 부터 실행시키면, "제품 어플리케이션(Production Application)" 으로 간주된다. 의존관계설정에 `optional`이라고 표시하는 것이 devtool들이 당신의 프로젝트가 사용하는 다른 모듈에 전이 되어 적용되는 것을 막기 위한 최선이다. 그래이들은 `optional` 의존관계를 지원하지 않는다. 그래서 [`propdeps-plugin`](https://github.com/spring-projects/gradle-plugins/tree/master/propdeps-plugin)을 사용해야 한다. 

>다시묶인 압축<sub>Repackaged archives</sub>은 devtools를 기본적으로 포함하지 않는다. 만약 [어떤 원격 devtools 기능](#heading-205-원격-어플리케이션)을 사용하길 원한다면 `excludeDevtools` 빌드 프라퍼티를 사용하지 못하게하는 설정을 포함시켜야 한다. 이 프라퍼티는 메이븐과 그래이들 모두 지원한다.

### 20.1 프라퍼티 기본값들   
스프링 부트가 지원하는 몇몇 라이브러리들은 성능향상을 위해 캐쉬(cache)들을 이용한다. 예를 들어 [템플릿 엔진](/blog/2017/12/10/spring-boot-chapter04/#heading-2719-템플릿-엔진들)은 템플릿 파일들을 계속 파싱하지 않기 위해  컴파일된 템플릿들을 캐쉬에 저장한다. Spring MVC는 정적 자원을 서비스 할 때 HTTP 캐싱 헤더들을 응답에 추가할 수 있다.

캐싱은 제품에서는 장점이 많지만 개발 중에는 변경한 내용을 바로 바로 확인 못할 수 있기 때문에 비 효율적일 수 있다. 그런 이유로 spring-boot-devtools은 디폴트로 캐싱 옵션을 쓰지 못하게 한다.

캐쉬 옵션은 보통 `application.properties` 파일에 설정한다. 예를 들어 Thymeleaf는 `spring.thyleleaf.cache` 프라퍼티를 제공한다. `spring-boot-devtools` 모듈은 프라퍼티를 수동 설정을 하는 것 대신에 적절한 개발시점 설정을 자동으로 적용한다.
>devtools가 적용하는 전체 프라퍼티 목록은 [DevToolsPropertyDefaultsPostProcessor](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-devtools/src/main/java/org/springframework/boot/devtools/env/DevToolsPropertyDefaultsPostProcessor.java)를 보시오.

### 20.2 자동 재시작
`spring-boot-devtools`를 사용하는 어플리케이션은 클래스패스상의 파일이 바뀔때 마다 자동으로 재시작 한다. IDE로 작업할 때 코드 변경에 대한 빠른 피드백을 제공하기 때문에 유용한 기능이다. 기본적으로 폴더를 가리키는 클래스패스 상의 시작점들이 변경에 대해 모니터링 된다. 정적인 자원과 뷰 템플릿과 같은 특정 자원들은 [어플리케이션 재시작이 필요 없다.](#heading-2022-자원들-제외시키기)
>**재시작의 발생점(Triggering)**<br>DevTools는 클래스패스의 자원들을 관찰하기 때문에 재시작을 발생시키는 것은 클래스패스의 수정뿐이다. 클래스패스의 수정을 초래하는 것은 IDE에 따라 다르다. 이클립스에서는 파일저장이 클래스패스 수정과 재시작의 원인이된다. IntelliJ IDEA에서는, 프로젝트 빌드(`Build -> Make Project`)가 그 원인이된다.

>DevTools는 적절히 운영하기 위해 고립된 어플리케이션 클래스로더가 필요하기 때문에 forking 이 활성화되 있는 한 지원하는 빌드 플러그인(메이븐과 그래이들)을 사용해 어플리케이션을 시작 할 수 있다.

>LiveReload를 사용하면 자동 재시작이 잘 작동한다. 자세한 사항은 [LiveReload 부분](#heading-203-LiveReload)를 보시오. JRebel을 사용하면, 동적 클래스 로딩을 위해 자동 재시작은 비활성화 된다. devtools의 다른 기능들(LiveReload나 프라퍼티 재정의와 같은)은 여전히 사용할 수 있다.

>DevTools는 재시작하는 동안 어플리케이션을 종료 하기 위해 Application Context의 셧다운 후크(Shutdown hook)을 의지한다. 그래서 `springApplication.setRegisterShutdownHook(false)`로 셧다운 후크를 비활성화 시키면 정상적으로 작동하지 않는다.

>클래스패스의 시작점을 변경시 리스타트의 발생점<sub>trigger</sub>으로 결정하면 DevTools는 자동적으로 `spring-boot`, `spring-boot-devtools`, `spring-boot-autoconfigure`, `spring-boot-actuator` 그리고 `spring-boot-starter` 이름의 프로젝트들을 무시한다.

>DevTools는 `ApplicationContext`가 사용하는 `ResourceLoader`를 상황에 맞게 변경할[^9] 필요가 있다. 만약 당신의 어플리케이션이 하나를 미리 제공하면 그것이 감싸 실행시키는 클래스를 제공할 것이다.[^10] `getResource` 메소드를 ApplicationContext에서 직접 재정의<sub>Overriding</sub>하는 것은 지원하지 않는다.

><span id="restart_reload"><strong>Restart vs Reload</strong></span><br>
스프링 부트가 제공하는 재시작 기술은 두개의 클래스로더들에 의해 작동된다. 변경되지 않는 클래스들(예를 들어 제3자 제공 jar들)은 기본 클래스로더가 적재<sub>load</sub>시킨다. 개발시에 계속 변경하는 클래스들은 *restart* 클래스로더가 적재시킨다. 어플리케이션이 재시작 되면 실행 중이던 *restart* 클래스로더는 버려지고 새로운 것이 생성된다. 이런 접근은 *기본* 클래스 로더는 이미 사용가능해 이동만 시키면 되기 때문에 일반적으로 어플리케이션 재시작이 "cold starts[^11]" 보다 훨씬 빠르다는 것을 의미한다. 만약 어플리케이션의 재시작이 너무 느리거나 클래스리딩시 문제가 발생하면 ZeroTurnaround의 [JRebel](https://zeroturnaround.com/software/jrebel/)과 같은 리로딩 기술들을 생각해 볼 수 있다. 이것들은 로딩될 때 클래스를 리로딩할때 쉽게 변경할 수 있도록 재작성하는 방식으로 작동한다.

### 20.2.1 조건평가로 변경사항 기록하기<sub>Logging</sub>
기본적으로 어플리케이션이 재 시작할 때마다  조건평가델타<sub>condition evaluation delta</sub> 보고서가 기록된다. 이 보고서는 당신이 빈들을 추가 또는 삭제했거나 설정 프라퍼티등을 변경했을 때 어플리케이션의 자동구성에서 변경된 것들을 보여준다. 

이 보고서 기록을 비활성화 시키려면 다음과 같이 프라퍼티를 설정한다.
```
spring.devtools.restart.log-condition-evaluation-delta=false
```

### 20.2.2 자원들 제외시키기
변경되더라도 프로그램을 재시작 할 필요없는 특정 자원들이 있다. 예를 들어, Thyleleaf<sub>타임리프</sub>는 그 자리에서 편집할 수 있다. 기본적으로 `/META-INF/mave`, `/META-INF/resources`, `/resources`,`/static`,`/public`, `/templates` 의 자원들은 변경되어도 리스타트 없이 [바로 리로드](#heading-203-livereload) 될 수 있다. 만약 제외시키는 리소스들을 커스터마이징 하려면 `spring.devtools.restart.exclude` 프라퍼티를 사용할 수 있다. 예를 들어 `/static`과 `public`만 제외하고자 한다면 다음과 같이 프라퍼티를 설정할 수 있다.
```
spring.devtools.restart.exclude=static/**,public/**
```
>만약 기본 설정은 유지하면서 추가적으로 자원들을 제외하려면 `spring.devtools.restart.additional-exclude`를 대신 사용한다.

### 20.2.3 추가적 경로 보기
클래스 패스 상에 없는 파일을 수정했을때 어플리케이션이 리스타트나 리로드 되길 원한면 `spring.devtools.restart.additional-paths` 프라퍼티를 이용해서 변경사항을 감시할 추가적인 경로를 설정할 수 있다. [위에 설명한](#heading-2022-자원들-제외시키기) `spring.devtools.restart.exclude`프라퍼티를 사용해 완전한 재시작 또는 [live reload](#heading-203-livereload)할 추가 경로를 설정할 수 있다.

### 20.2.4 재시작 비활성화<sub>Disable</sub>
재시작 기능을 사용 안하려면 `spring.devtools.restart.enabled` 프라퍼티를 사용해 비활성화 시킬 수 있다. 대부분의 경우, `application.properties` 파일에 설정한다. (이렇게 하면 재시작 클래스로더는 계속 초기화되지만 파일 변경을 감시하지 않는다.)

만약 재시작 지원을 완전히 비활성화 시켜야 하면 (재시작이 특정 라이브러리를 사용할 경우 작동하지 않는 경우와 같이) `SpringApplication.run(…​)`메소드를 호출 하기 전에 `System`의 `spring.devtools.restart.enabled` 프라퍼티를 `false`로 설정한다. 그 코드는 다음과 같다.

```java
public static void main(String[] args) {
	System.setProperty("spring.devtools.restart.enabled", "false");
	SpringApplication.run(MyApp.class, args);
}
```

### 20.2.5 트리거파일<sub>Trigger File</sub> 사용하기
변경된 파일들을 계속 컴파일하는 IDE를 이용해 작업할때 특정 시점에만 재시작이 일어나는 것을 선호할 수 있다. 이런 경우 진짜 재시작이 필요할 때만 수정되야 하는 특별한 파일을 사용할 수 있다. 이 파일을 "trigger file"이라고 한다. 이 파일을 변경시킬 경우만 재시작 검사를 촉발시키고 DevTool 이 무언가 해야함을 감지하면 재시작이 발생한다. 트리거 파일은 수동 또는 IDE 플러그인으로 갱신될 수 있다.

트리거파일을 사용하기 위해서는 `spring.devtools.restart.trigger-file` 프라퍼티에 트리거 파일의 경로를 설정해야 한다.

> [전역설정](#heading-204-전역설정들)인 `spring.devtools.restart.trigger-file` 를 이용해 설정해도 같은 방식으로 동작된다. 

### 20.2.6 재시작 클래스로더 프로젝트에 맞게 설정하기<sub>Customizing</sub>
위의 [Restart vs Reload](#restart_reload)에서 설명한대로 재시작 기능은 두개의 클래스도더들을 사용해 구현되었다. 대부분의 어플리케이션에서 이 방식은 잘 작동한다. 하지만 가끔 이것이 클래스 로딩 문제의 원인이 될 수도 있다.

기본적으로 IDE에 열려 있는 프로젝트들은 "restart" 클래스로더로 적재<sub>load</sub>되고 `.jar` 파일은 'base' 클래스로더로 적재된다. 만약 당신이 여러 모듈 프로젝트 상에서 작업하고 모든 모듈이 IDE로 가져오기<sub>Imort</sub>되 있지 않으면 이것을 커스터마이징 할 필요가 있다. 그렇게 하기 위해 `META-INF/spring-devtools.properties` 파일을 생성한다.

`spring-devtools.properties` 파일은 `restart.exclude`와 `restart.include` 접두어로 시작하는 프라퍼티들을 가질 수 있다. `include` 요소는 "restart" 클래스 로더안으로 넣을 것들을 `exclude` 요소는 "base" 클래스로더에 넣을 것들을 설정한다. 이 프라퍼티의 값들은 정규식 패턴을 이용해 클래스 패스를 정의한다. 다음은 그 예제이다.
```
restart.exclude.companycommonlibs=/mycorp-common-[\\w-]+\.jar
restart.include.projectcommon=/mycorp-myproj-[\\w-]+\.jar
```
>프라퍼티의 키는 반드시 유일해야 한다. 프라퍼티들이 모두 `restart.include.` 또는 `restart.exclude.` 로 시작하기 때문에 주의를 요한다.

>클래스패스 상의 모든 `META-INF/spring-devtools.properties`는 적재<sub>load</sub>된다. 프로젝트안이나 프로젝트가 사용하는 라이브러리 안에 넣을 수 있다.

### 20.2.7 알려진 제한사항들
재시작 기능은 표준 IO 클래스인 `ObjectInputStream`를 사용해 역직렬화된 객체에는 작동되지 않는다. 만약 데이터를 역직렬화할 필요가 있으면 스프링의 `ConfigurableObjectInputStream`을 `Thread.currentThread().getContextClassLoader()`와 함께 사용한다.

몇몇 제 3자<sub>Third-party</sub> 라이브러리들은 Context 클래스로더에 대한 고려없이 역직렬화 한다. 만약 그런 문제점을 발견하면 그 라이브러리 제작자들에게 수정을 요구할 필요가 있다.

### 20.3 LiveReload
`spring-boot-devtools` 모듈은 자원이 변경되었을때 웹브라우져 새로고침을 촉발시키는 내장 LiveReload를 포함하고 있다. LiveReload 브라우저 확장은 [livereload.com](http://livereload.com/extensions/)에서 크롬, 파이어폭스 그리고 사파리용을 무료로 제공한다.

어플리케이션이 실행되는 동안 LiveReload 서버가 시작되지 않게 하려면 `spring.devtools.livereload.enabled` 프라퍼티는를 `false`로 설정한다.

>한번에 하나의 LiveReload 서버만 실행할 수 있다. 어플리케이션을 시작하기 전에 다른 LiveReload 서버가 실행중인지 확인하라. 만약 IDE에서 여러개의 어플리케이션들을 시작하면 첫번째 어플리케이션만 LiveReload가 지원된다.

### 20.4 전역설정들
devtools의 전역설정은 당신의 `$HOME` 폴더에 `.spring-boot-devtools.properties` 파일에 추가해서 설정할 수 있다.(파일명이 . 으로 시작하는 것을 주의해라.) 이 파일의 모든 설정은 devtools를 사용하는 모든 스프링 부트 어플리케이션들에 적용된다. 예를 들어 [trigger file](#heading-2025-트리거파일trigger-file-사용하기)을 사용해 재시작하는 설정을 하려면 다음 프라퍼티를 추가한다.

**~/.spring-boot-devtools.properties**
```
spring.devtools.reload.trigger-file=.reloadtrigger
```

### 20.5 원격 어플리케이션
스프링 부트 개발자 툴들은 로컬에서의 개발로 제한되어 있지 않다. 어플리케이션을 원격으로 실행할때도 몇몇 기능들을 사용할 수 있다. 원격지원은 옵트인이다.[^12] 사용하기 위해서는 `devtools`가 다시묶인 압축<sub>Repackaged archives</sub>안에 포함되있는지 다음을 확인할 필요가 있다. 
```xml
<build>
  <plugins>
    <plugin>
      <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
        <configuration>
          <excludeDevtools>false</excludeDevtools>
        </configuration>
     </plugin>
  </plugins>
</build>
```
그리고 나서 `spring.devtools.remote.secret` 프라퍼티를 다음과 같이 설정한다.
```
spring.devtools.remote.secret=mysecret
```
원격 devtools 지원은 연결을 받아는 서버쪽 끝점<sub>endpoint</sub>과 IDE에서 실행시킨 클라이언트 어플리케이션 두 부분이 제공된다. 서버 컴포넌트는 `spring.devtools.remote.secret` 프라퍼티가 설정되면 자동으로 활성화된다. 클라이언트 컴포넌트는 수동으로 시작 시켜야 한다.

### 20.5.1 원격 클라이언트 어플리케이션 실행
원격 클라이언트 어플리케이션은 IDE가 실행시킨다. 연결하려는 원격 프로젝트와 같은 클래스패스에 있는 `org.springframework.boot.devtools.RemoteSpringApplication`를 실행할 필요가 있다. 어플리케이션에서 요구하는 한개의 인수는 연결하려는 원격 URL이다.

예를 들어, 이클리스나 STS를 사용하고 [Cloud Foundry](https://www.cloudfoundry.org)에 배치된 프로젝트 이름이 `my-app`이면 다음과 같이 한다.
+ `Run` 메뉴에서 `Run Configurations...`를 선택
+ 새로운 `Java Application` "launch configuration"을 생성
+ `my-app` 프로젝트안을 둘러본다.
+ `org.springframework.boot.devtools.RemoteSpringApplication` 를 메인클래스로 사용한다.
+ `https://myapp.cfapps.io`를 `Program arguments` 에 추가한다. (또는 당신의 원격 URL이 무엇이든간에 추가한다.)

원격 클라이언트를 실행하면 다음과 비슷하게 결과가 나올 것이다.
```
 .   ____          _                                              __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _          ___               _      \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` |        | _ \___ _ __  ___| |_ ___ \ \ \ \
 \\/  ___)| |_)| | | | | || (_| []::::::[]   / -_) '  \/ _ \  _/ -_) ) ) ) )
  '  |____| .__|_| |_|_| |_\__, |        |_|_\___|_|_|_\___/\__\___|/ / / /
 =========|_|==============|___/===================================/_/_/_/
 :: Spring Boot Remote :: 2.0.0.BUILD-SNAPSHOT

2015-06-10 18:25:06.632  INFO 14938 --- [           main] o.s.b.devtools.RemoteSpringApplication   : Starting RemoteSpringApplication on pwmbp with PID 14938 (/Users/pwebb/projects/spring-boot/code/spring-boot-devtools/target/classes started by pwebb in /Users/pwebb/projects/spring-boot/code/spring-boot-samples/spring-boot-sample-devtools)
2015-06-10 18:25:06.671  INFO 14938 --- [           main] s.c.a.AnnotationConfigApplicationContext : Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@2a17b7b6: startup date [Wed Jun 10 18:25:06 PDT 2015]; root of context hierarchy
2015-06-10 18:25:07.043  WARN 14938 --- [           main] o.s.b.d.r.c.RemoteClientConfiguration    : The connection to http://localhost:8080 is insecure. You should use a URL starting with 'https://'.
2015-06-10 18:25:07.074  INFO 14938 --- [           main] o.s.b.d.a.OptionalLiveReloadServer       : LiveReload server is running on port 35729
2015-06-10 18:25:07.130  INFO 14938 --- [           main] o.s.b.devtools.RemoteSpringApplication   : Started RemoteSpringApplication in 0.74 seconds (JVM running for 1.105)
```
>원격 클라이언트는 실제 어플리케이션과 같은 클래스 패스를 사용하기 때문에 어플리케이션 프라퍼티들을 직접 읽을 수 있다. 이것이  `spring.devtools.remote.secret` 프라퍼티가 읽혀지고 서버 인증으로 전달되는 이유이다.

>전송데이터를 암호화하고 패스워드 가로채기를 당하지 않게 하기 위해서 항상 연결 프로토콜로 `https://`를 사용하는 것이 바람직하다.

>원격 어플리케이션에 접속하기 위해 프락시<sub>proxy</sub>가 필요하다면 `spring.devtools.remote.proxy.host`와 `spring.devtools.remote.proxy.port` 프라퍼티를 환경설정으로 설정한다.

### 20.5.2 원격 업데이트
원격 클라이언트는 어플리케이션 클래스패스의 변경을 [로컬 재시작](#heading-202-자동-재시작) 과 같은 방법으로 관찰<sub>monitoring</sub>한다. 수정된 리소스가 원격 어플리케이션으로 push 되고나면 (요구된다면)재시작을 촉발한다. 이것은 로컬에 없는 클라우드 서비스가 제공하는 기능을 반복하는 경우 유용하다. 일반적으로 원격자원을 변경하고 재시작 하는 것이 리빌드및 배치의 전체 단계를 거치는 것보다 훨씬 빠르다.

>파일들은 원격 클라이언트가 실행중인 동안만 관찰된다. 만약 원격 클라이언트를 시작하기 전에 파일을 변경시키면 원격서버로 push되지 않는다.

## 21. 제품으로 어플리케이션 패키징하기
실행가능 jar<sub>Executable jar</sub>는 제품 배치를 위해 사용될 수 있다. 실행가능 jar는 독립적이기 때문에 클라우드 기반 배치에 아주 적당하다.

거기에 더해서 건강, 감사, 메티릭 REST나 JMX 엔트포인트들과 같은 "제품준비 완료" 기능들은 `spring-boot-actuator`의 추가를 고려해 본다. 자세한 것은 [Part 5 Spring boot Actuator: 제품 준비완료 기능](/blog/2017/12/11/spring-boot-chapter05/#heading-v-spring-boot-actuator-제품준비완료-기능들)에서 설명한다.

## 다음에 읽을 것
이제 당신은 스프링 부트를 어떻게 사용할지 그리고 당신이 따라야할 몇몇 모범 사례들을 이해했다. 당신은 특정한 [*스프링 부트의 기능들*](/blog/2017/12/10/spring-boot-chapter04/#heading-part4-스프링부트-기능들)을 좀더 깊이 공부하거나 건너 뛰고 "[생산 준비완료](/blog/2017/12/11/spring-boot-chapter05/#heading-v-spring-boot-actuator-제품준비완료-기능들)" 부분을 읽을 수 있다.




[^1]: Bills Of Material(BOM)은 자재 명세서로 어떤 재품을 구성하는 부속 명세서를 말한다. 여기에서는 starter jar내에 포함된 라이브러리들 목록을 말한다.
[^2]: Shade 플러그인은 uber-jar를 이용해 아티팩트를 패키징한다.
[^3]: 의존관계전이 (transitive dependencies)는 설정된 의존라이브러리가 의존하는 라이브러리를 같이 받아오는 것을 말한다.
[^4]: 메이븐 group 으로 프로젝트를 만드는 그룹(회사, 단체)를 말한다. 여기서는 스프링 부트를 만드는 곳을 말한다.
[^5]: 인메모리 데이터베이스(In-memory Database)는 데이터 스토리지의 메인 메모리에 설치되어 운영되는 방식의 데이터베이스 관리 시스템이다. [위키백과참조](https://ko.wikipedia.org/wiki/%EC%9D%B8%EB%A9%94%EB%AA%A8%EB%A6%AC_%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4)
[^6]: Auto-configuration is non-invasive.
[^7]: 분해된 형태. 원문은 Exploded form 인데 jar나 war로 압축되있는 어플리케이션을 실행시에는 압축을 풀어서 실행시키는 것을 말한다.
[^8]: 실행 중인 상태에서 변경사항 적용
[^9]: 커스터마이징
[^10]: Wrapping (Adapter) 패턴 - '이미 제공되어 있는 것' 과 '필요한 것' 사이의 '차이'를 없애주는 디자인 패턴
[^11]: 처음 상태에서 시작하는 것. 최초 실행이므로 캐시된 데이터가 없는 상태에서 시작된다. 여기서는 변하지 않는 jar들은 기본 클래스 로더에 의해 이미 시작되었고 리스타트 클래스로더는 변경된 부분만 새로 로딩하므로 속도가 빠르다.
[^12]: opt-in 사용자가 동의하면 실행할 수 있는 것을 말한다. 기능은 있으되 기본은 아니고 사용자가 동의해야 사용할 수 있는 것.