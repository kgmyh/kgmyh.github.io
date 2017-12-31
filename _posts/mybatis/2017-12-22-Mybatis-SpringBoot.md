---
layout: post
comments: true
title: "Mybatis-Spring-Boot-Starter 소개 문서 번역"
author: "김성환"
categories: [Springboot]
tags: [springboot, mybatis]
description: 마이바티스-스프링 부트-스타터 문서 번역
---
[원문](http://www.mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/)

* Kramdown table of contents
{:toc .toc}

# 소개 
***
## 마이바티스-스프링-부트-스타터란 무엇인가

마이바티스-스프링-부트-스타터는 [스프링 부트](http://projects.spring.io/spring-boot/)에 마이바티스를 빠르게 빌드할 수 있게 도와준다.

이 모듈을 사용하면 다음을 얻을 수 있다
- 독립실행 어플리케이션 빌드
- 항상 만들어야하는 상용구문<sub>boilerplate</sub>을 거의 0에 가깝게 줄일 수 있다
- XML설정을 줄일 수 있다

## 요구사항
마이바티스-스프링부트-스타터는 Java 6 이상 버전을 요구한다. 그리고 다음과 같은 버전의 마이바티스-스프링과 스프링 부트를 요구한다.

|마이바티스-스프링-부트-스타터|[마이바티스-스프링](http://www.mybatis.org/spring/index.html#Requirements)|스프링부트|
|-|-|-|
|1.3.x (1.3.1)|1.3 이상|1.5 이상|
1.2.x (1.2.1)|1.3 이상|1.4 이상|
|1.1.x (1.1.1)|1.3 이상|1.3 이상|
|1.0.x (1.0.2)|1.2 이상|1.3 이상|

## 인스톨

마이바티스-스프링-부트-스타터 모듈을 사용하기 위해서는  `mybatis-spring-boot-autoconfigure.jar` 파일과 그것의 의존라이브러리들(`mybatis.jar`,`mybatis-spring.jar` 등)을 클래스 패스상에 포함 시키면 된다. 

만약 메이븐을 사용한다면 다음 의존관계 설정을 `pom.xml`에 포함시킨다.

```xml
<dependency>
  <groupId>org.mybatis.spring.boot</groupId>
  <artifactId>mybatis-spring-boot-starter</artifactId>
  <version>2.0.0-SNAPSHOT</version> 
</dependency>
```
만약 그레이들을 사용한다면 `build.gradle`에 다음을 추가 한다
```groovy
dependencies{ 
  compile("org.mybatis.spring.boot:mybatis-spring-boot-starter:2.0.0-SNAPSHOT") 
}
```

## 빠른 설정

이미 알고 있듯이, 마이바티스를 스프링과 함께 사용하기 위해서는 최소한 한개의 `SqlSessionFactory` 와 최소한 한개의 매퍼 인터페이스가 필요하다.

마이바티스-스프링-부트-스타터는 다음과 같은일을 처리한다.

- 존재하는 `DataSource` 자동감지
- `SqlSeessionFactoryBean`을 사용해서 해당 데이터소스를 전달하는 `SqlSessionFactory`의 객체를 생성하고 등록한다.
- `SqlSessionFactory`을 이용해 `SqlSessionTemplate`의 객체를 생성하고 등록한다.
- 매퍼들을 자동스캔한 뒤 스캔된 것들을 `SqlSessionTemplate`에 연결하고 Spring Context에 등록해서 당신의 빈들에 주입할 수 있게 해준다.

다음과 같은 매퍼가 있다고 가정한다.
```java
@Mapper
public interface CityMapper {

    @Select("SELECT * FROM CITY WHERE state = #{state}")
    City findByState(@Param("state") String state);

}
```
당신은 그냥 평범한 스프링 부트 어플리케이션을 생성하고 다음 코드와 같이 매퍼가 주입되게 한다.(스프링 4.3 이상에서만 가능)
```java
@SpringBootApplication
public class SampleMybatisApplication implements CommandLineRunner {
   private final CityMapper cityMapper;

   public SampleMybatisApplication(CityMapper cityMapper) {
       this.cityMapper = cityMapper;
   }

   public static void main(String[] args) {
       SpringApplication.run(SampleMybatisApplication.class, args);
   }

   @Override
   public void run(String... args) throws Exception {
       System.out.println(this.cityMapper.findByState("CA"));
   }

}
```
당신이 할일은 이게 전부다. 이제 당신의 어플리케이션은 스프링 부트 어플리케이션으로 실행될 수 있다.

## 고급 스캐닝
기본적으로 마이바티스-스프링-부트-스타터는 `@Mapper` 어노테이션이 표시된 매퍼들을 검색한다.

만약 매퍼 스캔을 위해 당신이 정의한 어노테이션이나 마커 인터페이스[^1]을 사용하기 원한다면 `@MapperScan` 어노테이션을 사용하면 된다. 그것에 대한 자세한 것은 [마이바티스-스프링 참조페이지](http://www.mybatis.org/spring/mappers.html#scan)에서 확인하시오. 

마이바티스-스프링-부트-스타터는 스프링 컨텍스트에서 `MapperFactoryBean`을 하나라도 찾으면 매퍼 스캔작업을 시작하지 않기 때문에 만약 당신이 스캔작업이 완전히 멈추길 바라면 당신의 매퍼들을 `@Bean` 메소드를 이용해 명시적으로 등록해야만 한다. 

## SqlSession 사용하기
`SqlSessionTemplate`의 객체는 생성된 뒤 스프링 컨택스트에 등록되기 때문에 당신의 빈에 주입된 마이바티스 API를 다음 코드처럼 사용할 수 있다. (스프링 4.3 이상버전에서만 사용가능)
```java
@Component
public class CityDao {

   private final SqlSession sqlSession;

   public CityDao(SqlSession sqlSession) {
      this.sqlSession = sqlSession;
   }

   public City selectCityById(long id) {
      return this.sqlSession.selectOne("selectCityById", id);
   }

}
```

## 환경설정
다른 스프링 부트 어플리케이션 처럼 마이바티스-스프링-부트-어플리케이션의 환경설정 인자들<sub>parameter</sub>들도 `application.properties`(또는 `application.yml`)에 작성한다.

마이바티스는 프라퍼티들에 `mybatis` 접두어를 사용한다.

프라퍼티들은 다음과 같다.

|프라퍼티|설명|
|-|-|
|`config-location`|마이바티스 xml 설정파일의 위치|
|`check-config-location`|마이바티스 xml 설정 파일이 존재 여부 체크를 실행할지 말지를 표시한다.|
|`mapper-location`|매퍼 xml 설정파일의 위치|
|`type-aliases-package`|Type Aliases를 찾기위한 패키지들. (여러 패키지 지정시 구분 기호로 `, ; \t \n` 사용)|
|`type-handlers-package`|Type Handler들을 찾기 위한 패키지들 (여러 패키지 지정시 구분 기호로 `, ; \t \n` 사용)|
|`executor-type`|실행자 타입 : `SIMPLE`, `REUSE`,`BATCH`.|
|`configuration-properties`|마이바티스 설정을 위한 외부에 작성한 프라퍼티들. <br>명시된 프라퍼티들은 마이바티스 설정파일이나 매퍼 파일에서 자리표시자<sub>placeholder</sub>를 이용해 사용된다.<br>더 자세한 것은 [마이바티스 참조 페이지](http://www.mybatis.org/mybatis-3/configuration.html#properties)를 보시오|
|`configuration`|마이바티스 `Configuration` 빈. 사용가능한 프라퍼티들은 [마이바티스 참조 페이지](http://www.mybatis.org/mybatis-3/configuration.html#settings)를 보시오. <br>주의: 이 설정은 `config-location` 과 동시에 사용할 수 없다.|

예:
```
#application.properties
mybatis.type-aliases-package=com.example.domain.model
mybatis.type-handlers-package=com.example.typehandler
mybatis.configuration.map-underscore-to-camel-case=true
mybatis.configuration.default-fetch-size=100
mybatis.configuration.default-statement-timeout=30
...
```
```
# application.yml
mybatis:
    type-aliases-package: com.example.domain.model
    type-handlers-package: com.example.typehandler
    configuration:
        map-underscore-to-camel-case: true
        default-fetch-size: 100
        default-statement-timeout: 30
...
```
## ConfigurationCustomizer  사용하기
마이바티스-스프링-부트-스타터는 자동설정으로 등록된 마이바티스 환경설정을 Java Config를 사용해 커스터 마이징 할 수 있다. 마이바티스-스프링-부트-스타터는 `ConfigurationCustomizer` 를 implements한 빈을 자동으로 찾아서 마이바티스 환경설정을 커스터마이징하는 메소드를 호출한다. (1.2.1 이상 버전부터 사용가능)

예:
```java
//@Configuration class
@Bean
ConfigurationCustomizer mybatisConfigurationCustomizer() {
    return new ConfigurationCustomizer() {
        @Override
        public void customize(Configuration configuration) {
            // customize ...
        }
    };
}
```

## 마이바티스 컴포넌트들 탐지하기.
마이바티스-스프링-부트-스타터는 마이바티스에서 제공하는 다음의 인터페이스를 구현한<sub>implements</sub> 빈들을 탐지한다.
- [Interceptor](http://www.mybatis.org/mybatis-3/configuration.html#plugins)
- [DatabaseIdProvider](http://www.mybatis.org/mybatis-3/configuration.html#databaseIdProvider)

## 샘플들 실행하기
이 프로젝트는 두개의 샘플을 제공한다. 

|샘플|설명|
|-|-|
|[첫번째 샘플](https://github.com/mybatis/spring-boot-starter/tree/master/mybatis-spring-boot-samples/mybatis-spring-boot-sample-annotation)|한개의 매퍼와 그 매퍼가 주입되는 하나의 빈으로 구성된 가장 간단한 시나리오를 보여주는 샘플.<br>이 샘플을 위의 빠른 설정 부분에서 보았었다.|
|[두번째 샘플](https://github.com/mybatis/spring-boot-starter/tree/master/mybatis-spring-boot-samples/mybatis-spring-boot-sample-xml)|xml 파일을 이용한 매퍼와 `SqlSessionTemplate`을 사용하는 Dao를 사용하는 방법을 보여준다.|



[^1]: Marker Interface는 java.io.Serializable 처럼 특정 클래스의 역할을 표시하기 위한 목적의 Interface이다. (역자)









