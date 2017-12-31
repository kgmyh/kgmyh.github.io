---
layout: post
comments: true
title: "SpringBoot Reference번역 - Part01:튜토리얼 개요"
author: "김성환"
categories: [Springboot]
tags: [springboot, springboot Reference]
description: 스프링 부트 part1 번역. 전체 문서에 대한 문서의 개요
---
버전 : 2.0.0.M7
저자: 
Phillip Webb, Dave Syer, Josh Long, Stéphane Nicoll, Rob Winch, Andy Wilkinson, Marcel Overdijk, Christian Dupuis, Sébastien Deleuze, Michael Simons, Vedran Pavić, Jay Bryant
번역: 김성환

__Copyright ⓒ 2012-2017__
Copies of this document may be made for your own use and for distribution to others, provided that you do not charge any fee for such copies and further provided that each copy contains this Copyright Notice, whether distributed in print or electronically.

[원본](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#boot-documentation)

* Kramdown table of contents
{:toc .toc}
# Part 1. Spring Boot 문서

이번 장은 스프링 부트 참조 문서에 대한 간단히 개요를 제공한다. 이 문서의 나머지부분에 대한 가이드가 될 것이다.

## 1. 이 문서에 대해서
스프링 부트 참조가이드는 다음 세 형태로 제공된다.
+ [HTML](https://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/html/)
+ [PDF](https://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/pdf/spring-boot-reference.pdf)
+ [EPUB](https://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/epub/spring-boot-reference.epub)
이 문서의 최종본은 [docs.spring.io/spring-boot/docs/current/reference](https://docs.spring.io/spring-boot/docs/current/reference/)에 위치한다.

이 문서의 저작권은 개인 소유로 사용하거나 다른 사람들에게 돈을 받지 않고 배포 할 수 있다. 그리고 프린트나 파일로 배포시 이 저작권 공고가 담겨져 있어야 한다.

## 2. 도움 받기
만약 스프링 부트로 개발시 문제가 생기는 경우 다음과 항목들을 참고 하시오.
+ [How-to documents](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#howto) 를 보시오. 일반적으로 많이 나오는 질문들에 대한 해결책을 제공한다.
+ 스프링 기본을 배우시오. 스프링부트는 스프링의 다른 프로젝트들을 빌드한다.[spring.io](https://spring.io/) 웹사이트의 풍부한 참조 문서들을 확인하시오. 만약 스프링을 처음 접하였다면 [가이드](https://spring.io/guides)의 문서를 공부해 보시오.
+ 질문하시오. 우리는 [스택오버플로우](https://stackoverflow.com/) 사이트의 [`spring-boot`](https://stackoverflow.com/tags/spring-boot) 태그가 붙은 질문들을 수시로 모니터링 하고 있다.
+ [스프링부트 github 이슈](https://github.com/spring-projects/spring-boot/issues)에 스프링부트 관련 버그를 올려주시오.

> 스프링부트는 이 문서를 포함해 모두 오픈소스이다. 만약 당신이 이 문서에서 오류를 발견하거나 개선에 참여하고 싶으면 [참여](https://github.com/spring-projects/spring-boot/tree/master) 해 주시오.

## 3.첫번째 단계
당신이 스프링부트나 '스프링'을 처음 시작한다면 다음 주제를 따라 시작하시오.
+ 간단한 것 부터 : [개요](/blog/2017/12/04/spring-boot-chapter02/#heading-8-스프링부트-소개) \| [요구사항](/blog/2017/12/04/spring-boot-chapter02/#heading-9-시스템-요구사항) \| [설치](/blog/2017/12/04/spring-boot-chapter02/#heading-10-스프링부트-설치install)
+ 튜토리얼: [Part1](/blog/2017/12/04/spring-boot-chapter02/#heading-11-첫번째-스프링부트-어플리케이션-개발하기) \| [Part2](/blog/2017/12/04/spring-boot-chapter02/#heading-113-코드-작성하기)
+ 당신의 예제를 실행하기: [Part1](/blog/2017/12/04/spring-boot-chapter02/#heading-114-예제-실행하기) \| [Part2](/blog/2017/12/04/spring-boot-chapter02/#heading-115-실행가능-jar-생성하기)

## 4. 스프링부트로 작업하기
진짜 스프링부트를 시작할 준비가 되었나? 이 문서는 다음과 같은 항목의 내용을 제공할 것이다.
+ 빌드 시스템: [메이븐](/blog/2017/12/04/spring-boot-chapter03/#heading-132-메이븐-maven) \|[그래이들](/blog/2017/12/04/spring-boot-chapter03/#heading-133-그래이들-gradle) \|[앤트](/blog/2017/12/04/spring-boot-chapter03/#heading-134-앤트-ant) \|[스타터들](/blog/2017/12/04/spring-boot-chapter03/#heading-135-스타터-들starters)
+ 모범 사례: [코드구조](/blog/2017/12/04/spring-boot-chapter03/#heading-14-코드-구조) \| [@Configuration](/blog/2017/12/04/spring-boot-chapter03/#heading-15-설정-클래스-configuration-classes) \| [@EnableAutoConfiguration](/blog/2017/12/04/spring-boot-chapter03/#heading-16-자동-설정-auto-configuration) \| [빈들과 의존관계 주입](/blog/2017/12/04/spring-boot-chapter03/#heading-17-스프링-빈들과-의존관계-주입)
+ 코드 실행하기: [IDE](/blog/2017/12/04/spring-boot-chapter03/#heading-191-ide에서-실행하기)\|[패키징](/blog/2017/12/04/spring-boot-chapter03/#heading-192-패키지화한-어플리케이션으로-실행하기)\|[메이븐](/blog/2017/12/04/spring-boot-chapter03/#heading-193-메이븐-플러그인-사용하기)\|[그래이들](/blog/2017/12/04/spring-boot-chapter03/#heading-194-그래이들-플러그인-사용하기)
+ 어플리케이션 패키징하기: [Jar로 제품화](/blog/2017/12/04/spring-boot-chapter03/#heading-21-제품으로-어플리케이션-패키징하기)
+ Spring Boot CLI: [CLI사용하기](#heading-part7-스프링부트-cli)

## 5. 스프링부트의 특징에 대해 배우기
스프링부트의 핵심기능에 대한 더 많은 상세사항을 필요한가? [이어지는 항목들에서 다룰 것이다.](#)
+ 핵심특징: [스프링어플리케이션](/blog/2017/12/10/spring-boot-chapter04/#heading-23-springapplication) \|[외부설정](/blog/2017/12/10/spring-boot-chapter04/#heading-24-외부설정) \|[프로파일스](#) \|[기록남기기-logging](#)\|
+ 웹 어플리케이션: [MVC](#) \|[내장 컨테이너들](#)
+ 데이터관련 작업 : [SQL](#) \|[NO-SQL](#)
+ 메세징: [개요](#) \|[JMS](#)
+ 테스팅: [개요](#) \|[부트 어플리케이션](#) \|[유틸들](#)
+ 확장: [자동 설정](#) \|[@Conditions](#)

## 6. 제품화하기
스프링부트 어플리케이션을 제품화 하기 위한 [좋은 기능](#)을 제공한다.
+ 끝점(endpoint)들 관리: [개요](#) \| [사용자정의](#)
+ 연결 옵션들: [HTTP](#) \| [JMS](#)
+ 모니터링: [메트릭스](#) \| [감사(Auditing)](#) \|[실행추적(Tracing)](#) \|[진행](#)

## 7. 고급 주제들
마지막으로 상급 사용자들을 위한 몇몇 토픽이 있다.
+ 스프링부트 어플리케이션 개발: [클라우드 개발](#) \| [OS 서비스](#)
+ 빌드툴 플러그인들: [메이븐](#) \| [그래이들](#)
+ 부록: [어플리케이션 설정들](#) \| [자동설정 클래스들](#) \| [실행할수 있는 Jar](#)



