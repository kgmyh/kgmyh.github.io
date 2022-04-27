---
layout: post
title: Jekyll에서 Sass 사용 설정하기
description: Jekyll에서 Sass를 사용하기 위한 디렉토리 구조 및 기본 설정하기
author: 김성환
categories: [Jekyll]
tags: [Jekyll, Sass]
comment: true
published: False
---
지킬을 이용해 블로그를 만드는 재미에 빠져 있다. 그런데 계속 아쉬운 것이 css를 이용해 style을 적용하는 것이다. 내용이 중요한데 어느 순간 보면 css 수정하고 있다. 사실 지킬을 접한지 얼마 안되 초보자이고 css에도 초보자이다 보니 테마에서 제공한 css를 조금씩 바꾸는 정도로 만족하고 있었다. 그러나 만족이 안된다. ㅜ..ㅜ 그래서 이것 저것 찾다 보니 sass 란 것이 있다는 것을 알게 되어 이것을 지킬에 어떻게 적용하는지 찾아보게되었다. 

나의 가장 큰 의문은 sass로 작성하면 css로 변환을 해야 하는데 그것이 어떻게 이뤄 지는지 였다. 그리고 그것에 대한 설정. 그것을 정리하려고 한다.

이것이 끝나면 sass를 본격적으로 공부해봐야 겠다.


지금 것을 정리하는데 [Using Sass with Jekyll](http://markdotto.com/2014/09/25/sass-and-jekyll/) 을 참고했다.
* Kramdown table of contents
{:toc .toc}

Jekyll에 Sass로 style 적용하기
===

## 1. Sass Stylesheet 만들기

Sass Stylesheet를 만들때 가장 먼저 할 일을 Front matter<sub>YAML 머릿말</sub>를 작성하는 것이다. 안에 내용 유무와 관계 없이 빈 것이라도 만들어야 한다.
css 디렉토리 하위에 파일명.scss 으로 저장한다. (파일명.css 로 변환된다.)

**ex: styles.scss**
```
---
# Front matter - 내용이 없어도 --- 는 반드시 들어가야 한다. 
---
Sass 내용
```

**경로**
```
ROOT 경로/
  |---_include/
  |---_layouts/
  |---_post/
  |---_site/
  |---assets/
  |---css/
  |      |---styles.scss
  |---_config.yml
```

## 2. layout HTML에 stylesheet 링크 걸기
위 구조로 만들고 지킬 서버를 실행하면 `styles.css` 파일이 `_site/css/` 디렉토리에 생성된다.
css 파일명은 scss 파일명을 사용해서 만들어 진다.

HTML 파일의 `<head>`에 다음과 같이 스타일시트 링크를 걸어준다. 레이아웃의 `default.html(main html)`에 작성하면 된다.
```html
<link rel="stylesheet" href="/css/styles.css">
```

## 3. Sass imports 사용
sass 파일을 모듈화 해 여러개 만들고 `@import` 구문을 이용해 css로 변환될 때 하나로 합쳐지도록 할 수도 있다.

모듈화한 `.scss` 파일을 `_sass/` 디렉토리에 넣은뒤 메인 scss파일(위의 `styles.scss`)에서 `@import`로 합친다. 이 방식은 역할에 따라 (예를 들어 폰트 설정, 레이아웃설정, 기본설정등) 모듈화해서 만들어 관리하기 할 수 있기 때문에 좋을 것 같다.

`_sass/`에 `base.scss`,  `font.scss`, `layout.scss` 를 만들었다면 `styles.scss`에 서 다음과 같이 작성하면된다.

```
---
# Front matter
---
//import 하기
@import "base";
@import "font";
@import "layout";
```
>주의 1. _sass 에 작성한 scss 파일들은 import되는 것이 목적인 파일들이기 때문에 front matter를 작성하지 않는다.
>주의 2. scss에 한글이 들어가면 안된다. 

## 4. _config.yml 설정
 + 위의 모듈화한 scss파일들을 저장할 디렉토리를 변경하고 싶은 경우 다음과 같이 설정한다.
 ```
 sass:
    sass_dir: 저장할 경로
```

+ scss파일이 css 로 변환될 때 최소화형태<sub>Minification</sub>로 만들 경우 다음과 같이 설정한다.
```
sass:
   style: compressed
```



