---
layout: post
comments: false
title: "Beautiful Soup 4.4.0 문서 번역"
author: "김성환"
categories: [Python]
tags: [Python, Beautiful Soup]
published: false
---

원문 : https://www.crummy.com/software/BeautifulSoup/bs4/doc/#output-encoding

# Beautiful Soup 문서
[Beautiful Soup](https://www.crummy.com/software/BeautifulSoup/)는 HTML과 XML로 부터 데이터를 가져오기 위한 파이썬 라이브러리이다. 이 라이브러리는 자연스런 방법으로 검색하고 찾고 파스트리를 수정 기능을 제공하는 어떠한 파서(문장구조 분석프로그램)와도 호환된다. 이 라이브러리가 프로그래머들의 일하는 시간을 줄여줄 것이다.

이 설명서는 Beautiful Soup 4의 주요 기능을 예제와 함께 보여줄 것이다. 이 라이브러리의 장점과 어떻게 처리하는지, 어떻게 당신이 원하는 을 처리하도록 만드는지 그리고 당신의 예상과 다르게 되었을때 무엇을 해야 하는지를 설명할 것이다.

이 문서의 예제들은 Python 2.7과 Python 3.2 모두 사용할 수 있다. 

당신이 [Beautiful Soup 3](https://www.crummy.com/software/BeautifulSoup/bs3/documentation.html) 의 문서를 찾고 있다면 Beautiful Soup3 은 더이상 개발되지 않으며 모든 새로운 프로젝트에는 Beautiful Soup 4를 사용하는 것을 추천한다. 만약 Beautiful Soup 3과 4 버전의 차이점을 알고 습으면 [코드를 BS4로 옮기기](#heading-코드를-BS4-로-옮기기) 를 보시오.
이 문서는 Beautiful Soup 사용자의 도움으로 다른 언어로 번역되 있다.
+ [这篇文档当然还有中文版.](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/)
+ このページは日本語で利用できます. [外部リンク](http://kondou.com/BS4/)
+ 이 문서는 한국어 번역도 가능합니다.[외부 링크](#)

## 도움 얻기
만약 Beautiful Soup에 대해 질문이나 실행시 문제가 있다면 [토의 그룹으로 메일을 보내시오.](https://groups.google.com/forum/?fromgroups#!forum/beautifulsoup) 만약 발생한 문제가 HTML 문서 파싱이라면 그 문서에 대해 [diagnose() 함수가 말한 것](#heading-diagnose)을 꼭 알려주십시오.


## 빠른 시작
다음 HTML 문서는 이 문서 전체의 예제에서 사용할 것이다. 이것은 *이상한 나라의 앨리스* 이야기의 일부이다.
```html
<html><head><title>The Dormouse's story</title></head>
<body>
  <p class="title"><b>The Dormouse's story</b></p>
  <p class="story">Once upon a time there were three little sisters; and their names were
    <a href="http://example.com/elsie" class="sister"  id="link1">Elsie</a>,
    <a href="http://example.com/lacie" class="sister"  id="link2">Lacie</a> and
    <a href="http://example.com/tillie" class="sister"  id="link3">Tillie</a>;
    and they lived at the bottom of a well.
  </p>
  <p class="story">...</p>
</body>
</html>
```
"세 자매" 문서를 Beautiful Soup을 통해 실행하면 중첩 데이터 구조의 문서를 표현하는 `BeautifulSoup` 객체를 받을 수있다.
```python
from bs4 import BeautifulSoup

soup = BeautifulSoup(html_doc, 'html.parser')

print(soup.prettify())
# <html>
#  <head>
#   <title>
#    The Dormouse's story
#   </title>
#  </head>
#  <body>
#   <p class="title">
#    <b>
#     The Dormouse's story
#    </b>
#   </p>
#   <p class="story">
#    Once upon a time there were three little sisters; and their names were
#    <a class="sister" href="http://example.com/elsie" id="link1">
#     Elsie
#    </a>
#    ,
#    <a class="sister" href="http://example.com/lacie" id="link2">
#     Lacie
#    </a>
#    and
#    <a class="sister" href="http://example.com/tillie" id="link2">
#     Tillie
#    </a>
#    ; and they lived at the bottom of a well.
#   </p>
#   <p class="story">
#    ...
#   </p>
#  </body>
# </html>
```
위의 데이터 구조를 검색하는 간단한 방법이 여기에 있다.
```python
soup.title
# <title>The Dormouse's story</title>

soup.title.name
# u'title'

soup.title.string
# u'The Dormouse's story'

soup.title.parent.name
# u'head'

soup.p
# <p class="title"><b>The Dormouse's story</b></p>

soup.p['class']
# u'title'

soup.a
# <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>

soup.find_all('a')
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
#  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

soup.find(id="link3")
# <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>
```
일반적으로 많이 하는 작업으로 <a> 태그에서 URL들만 추출하는 것이다.
```python
for link in soup.find_all('a'):
    print(link.get('href'))
# http://example.com/elsie
# http://example.com/lacie
# http://example.com/tillie
```
또한가지의 자주 하는 작업으로는 페이지 내의 모든 text들을 추출하는 것이다.
```python
print(soup.get_text())
# The Dormouse's story
#
# The Dormouse's story
#
# Once upon a time there were three little sisters; and their names were
# Elsie,
# Lacie and
# Tillie;
# and they lived at the bottom of a well.
#
# ...
```
이런 것들이 당신이 하고자 하는 일과 비슷하다면 계속 읽으시오.

# Beautiful Soup 설치

## 설치 후 문제

## 파서 인스톨

# Soup 만들기

# 객체 종류들
## 태그

### 이름
### 속성

#### 다중 값 속성
## NavigableString
## BeautifulSoup
## 주석과 다른 특수한 문자열



# 문제 해결 (Troubleshooting)
## diagnose()


# Beautiful Soup3
## 코드를 BS4 로 옮기기