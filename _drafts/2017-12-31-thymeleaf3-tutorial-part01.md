---
layout: post
author: "김성환"
title: Thymeleaf 튜토리얼 파트1
description: 
categories: [Thymeleaf]
tags: [thymeleaf, 타임리프, 템플릿엔진]
comments: true
published: true
---
[원문](http://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html)
***

<img src="/assets/img/thymeleaf/thymeleaf.png" width="100" alt="Thymeleaf 로고">

* Kramdown table of contents
{:toc .toc}

# 1. 타임리프 소개
## _1.1 타임리프는 무엇인가_
타임리프는 웹과 독립실행 환경 모두에 적용할 수 있는 최신의 서버사이트 자바 템플릿 엔진으로 HTML, XML, Javascript, Css와 일반 Text 까지도 처리할 수 있다.

## 1.2 _타임리프가 처리하는 템플릿의 종류는 무엇인가?_
타임리프는 6종류의 템플릿들을 처리한다. 각각을 *Template Mode* 라 부른다.
* HTML
* XML
* TEXT
* JAVASCRIPT
* CSS
* RAW

_마크업_ 템플릿 모드 (`HTML`과 `XML`)와 3개의 _텍스트_ 템플릿 모드 (`TEXT`, `JAVASCRIPT` 그리고 `CSS`) 와 _조작할 수없는_<sub>no-op</sub> 템플릿 모드 (`RAW`)가 있다.

`HTML` 템플릿 모드는 HTML5, HTML4, XHTML을 포함한 모든 HTML을 처리한다. 어떠한 검사나 마크업형식에 만게 작성했는지에 대한 문서체크<sub>well-formedness</sub>가 실행되지 않으며 출력될 템플릿 코드 구조는 최대로 가능한 규모가 존중될 것이다.

## 4.6 리터럴(상수) 값들
### _Text Literals_

Text 리터럴은 단순한 문자 string으로 작은따옴표로 감싼다. 어떠한 문자들도 사용가능한데 만약 작은 따옴표를 사용하려면 `\'`를 사용해서 이스케이프처리 해야 한다.
~~~html
<p>
  Now you are looking at a <span th:text="'working web application'">template file</span>.
</p>
~~~
### _숫자 리터럴들_
숫자는 우리가 알고 있는 숫자다.
~~~html
<p>The year is <span th:text="2013">1492</span>.</p>
<p>In two years, it will be <span th:text="2013 + 2">1494</span>.</p>
~~~
### _Boolean 리터럴들_
boolean 리터럴은 `true`와 `false`이다. 예를 들어
```html
<div th:if="${user.isAdmin()} == false"> ...
```
이 예제에서, `== false` 는 괄호 밖에 쓰여져 있다. 이것이 Thymeleaf가 처리하는 방식이다. 만약 중괄호 안에 작성하면 OGNL/SpringEL 엔진에 의해 처리된다.
```html
<div th:if="${user.isAdmin() == false}"> ...
```
### _null 리터럴_
`null` 리터럴은 다음과 같이 사용될 수있다.
~~~html
<div th:if="${variable.something} == null"> ...
~~~
-----------<http://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#literals>-------


# 5. 속성 값 넣기
이번 챕터에서는 마크업 태그의 속성 값을 대입 (또는 수정) 하는 방법에 대해 설명할 것이다.

## _5.1 모든 속성의 값 넣기_
우리 웹사이트는 뉴스레터를 발간할 것이고 모든 사용자가 구독하기를 원한다 고하자. 그래서 `/WEB-INF/templates/subscribe.html` 템플릿을 `form`을 사용해 다음과 같이 생성했다.
~~~html
<form action="subscribe.html">
  <fieldset>
    <input type="text" name="email" />
    <input type="submit" value="Subscribe!" />
  </fieldset>
</form>
~~~

# 6. 반복
지금까지 우리는 홈페이지, 사용자 프로필 페이지 그리고 사용자가 뉴스레터 구독을 신청하는 페이지를 만들어왔다.  그런데 우리 제품들은 어떻게 하나? 제품 페이지를 만들기 위해 컬렉션의 아이템들을 반복 조회하는 방법이 필요할 것이다.

## _반복 기본_
`/WEB-INF/templates/product/list.html` 페이지에서 제품들을 보여주기 위해 테이블을 사용할 것이다. 한행(<tr>요소)에 한제품씩 보여질 것이다. 그래서 우리는 _행 템플릿<sub>template row</sub>_을(각 제품을  보여주는 방법의 전형적인 예가 될 것이다) 생성할 필요가 있다. 그리고 나서 타임리프가 그것을 한번에 한 제품씩 반복하도록 명령한다.

표준 구문은 이런 반복을 위해 다음과 같은 속성을 제공한다. : `th:each`

### _th:each 사용_
제품 목록 페이지를 위해 서비스단에서 제품 목록을 조회해서 템플릿 컨텍스트에 추가하는 컨트롤러 메소드가 필요할 것이다.
~~~java
public void process(
        final HttpServletRequest request, final HttpServletResponse response,
        final ServletContext servletContext, final ITemplateEngine templateEngine) throws Exception {

	ProductService productService = new ProductService();
    List<Product> allProducts = productService.findAll();
    
	WebContext ctx = new WebContext(request, response, servletContext, request.getLocale());
    ctx.setVariable("prods", allProducts);
    
	templateEngine.process("product/list", ctx, response.getWriter());
}
~~~
그리고 나서 템플릿 페이지에서 `th:each`를 사용해서 제품들의목록을 반복 조회한다.
~~~html
<!DOCTYPE html>

<html xmlns:th="http://www.thymeleaf.org">
  <head>
    <title>Good Thymes Virtual Grocery</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <link rel="stylesheet" type="text/css" media="all" 
          href="../../../css/gtvg.css" th:href="@{/css/gtvg.css}" />
  </head>
  <body>
    <h1>Product list</h1>
    <table>
      <tr>
        <th>NAME</th>
        <th>PRICE</th>
        <th>IN STOCK</th>
      </tr>
      <tr th:each="prod : ${prods}">
        <td th:text="${prod.name}">Onions</td>
        <td th:text="${prod.price}">2.41</td>
        <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
      </tr>
    </table>

    <p>
      <a href="../home.html" th:href="@{/}">Return to home</a>
    </p>
  </body>
</html>
~~~
위의 예 나오는 `prod : ${prods}` 속성값은 다음을 의미한다. ${prods} 평가의 결과로 나온 각 요소들에 대해서  prod 변수안의 현재 요소를 사용해서 템플릿 조각들을 반복한다. 우리가 본 각각의 요소에 다음과 같은 명칭을 준다.
* `${prods}`는 _반복될 표현식<sub>iterated expression</sub>_ 또는 _반복될 변수 <sub>iterated variable</sub>_ 라 부른다.
* `prod` 는 _반복변수<sub>iteration</sub>_ 또는 간단히 _iter 변수_ 라고 부른다.

주의할 것은 `prod` 반복 변수는 &lt;tr&gt; 요소 범위 안에서만 사용할 수있다. 그러므로 &lt;td&gt; 처럼 &lt;tr&gt;의 안에서  사용가능 하다. 