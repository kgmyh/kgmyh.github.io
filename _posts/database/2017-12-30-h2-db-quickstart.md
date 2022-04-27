---
layout: post
author: "김성환"
title: H2 Database Quickstart
description: H2 Database 빠른 시작 번역문서
categories: [database]
tags: [h2, quickstart, database]
comments: true
published: false
---
[원문](http://www.h2database.com/html/quickstart.html)

## H2 디비를 어플리케이션에 내장해서 사용하기

이 데이터베이스는 내장형 모드와 서버 모드로 사용할 수 있다. 내장형 모드로 사용하기 위해서는 다음이 필요하다.
1. `h2*.jar` 파일이 클래스패스에 추가되어야 한다. (H2는 다른 라이브러리를 의존하지 않는다.)
2. JDBC Driver 클래스로 `org.h2.Driver`를 사용한다.
3. Database URL `jdbc:h2:~/test` 는 사용자 홈 디렉토리의 `test` 데이터베이스와 연결한다.
4. 새 데이터베이스는 자동으로 생성된다.

## H2 콘솔 어플리케이션
콘솔은 브라우저 사용해 데이터베이스에 연결해 SQL문을 실행하게 해준다.
![h2-console](/assets/img/h2/h2-console.png)

### 순서대로 해보기
#### 설치
windows installer를 사용해 소프트웨어를 설치한다. (아직 설치 하지 않았다면) [^1]

#### 콘솔 시작하기
윈도우즈의 [시작]-[모든 프로그램]-[H2]-[H2 Console(command line)]을 클릭한다.[^2]
![H2 Console실행](/assets/img/h2/quickstart-1.png)

콘솔창이 나타난다.

![H2 Console window](/assets/img/h2/quickstart-2.png)

웹 브라우저에서 `http://localhost:8082` URL을 연다. 방화벽으로부터 보안 경고가 보일 수도있다. 만약에 네트워크를 이용해 다른 컴퓨터가 데이터베이스에 접근하지 못하게 하려면 방화벽에서 이 연결을 막으면 된다. 이번 설명에는 로컬 연결만 설명할 것이다.

### 로그인
[Generic H2]를 선택하고 [Connect]를 클릭한다.

![H2 Console 로그인](/assets/img/h2/quickstart-3.png)

각 항목들을 입력하고 [연결]을 클릭하면 로그인 된다. 그럼 다음과 같은 화면이 나온다.

![H2 Console 로그인](/assets/img/h2/quickstart-4.png)

SQL 창에 sql문을 작성하고 실행 버튼을 클릭하면 SQL문이 실행된다.

### 연결 끊기

![H2 Console 로그인](/assets/img/h2/quickstart-5.png)

위 그림에 표시된 버튼(상단 왼쪽)을 클릭한다.


[^1]: 인스톨러 버전과 플랫폼 독립적인 압축버전이 있다. [다운로드페이지.](http://www.h2database.com/html/download.html) 압축버전은 압축을 풀기만 하면된다.
[^2]:  플랫폼 독립적인 압축버전인 경우 설치경로/bin 디렉토리의 h2.bat (리눅스는 h2.sh)를 실행한다.








