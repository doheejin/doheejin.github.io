---
layout: post
title:  "[github] 깃허브 블로그 구성(1) - 블로그 만들기"
date:   2021-02-14T14:25:52-05:00
author: Heejin Do
categories: github
tags:	github blog
---

개발을 하다보면 기록의 중요성을 깨닫게 된다. 노트에 정리하는 것에 한계를 느껴, 검색과 재사용성에서 이점이 큰 Github Blog를 이용해 온라인 저장소를 만들어보았다. (o゜▽゜)o☆

간단해보였지만 시행착오를 겪었고, 최종적으로 적용한 방식을 아래에 정리해두었다. 그대로 따라한다면 아마 문제없이 성공 할 수 있을 것이다.

## Jekyll 테마 선택 및 Repository 생성

직접 코딩을 해 사이트를 구성할 수도 있지만, 나는 시간절약을 위해 [Jekyll 테마(클릭)](http://jekyllthemes.org/)를 사용했다.

### 1. Jekyll 테마 고르기
수많은 테마 중 마음에 드는 테마를 하나 선택한다.

<a href="http://jekyllthemes.org/">
<img src="/assets/images/jekyll.PNG" title="Jekyll theme example">
</a>

### 2. 선택한 테마 Fork 하기
선택한 테마를 자신의 github에 Fork 하는 과정이다.

각 테마를 보면 아래 사진처럼 `Homepage` 버튼이 나와있다. 이 버튼을 클릭한다.

<img src="/assets/images/jekyll_1.PNG" title="Jekyll Homepage">

버튼을 클릭하면 해당 github 페이지로 이동된다.

오른쪽 상단의 Fork를 클릭해 자신의 repository로 복제한다.

<img src="/assets/images/jekyll_2.PNG" title="Jekyll them Fork">

이제 자신의 repository에 해당 테마가 복제되어있음을 확인 할 수 있다.


### 3. 깃허브 Repository 생성

복제된 repository에서 오른쪽의 `settings`를 클릭해 `repository name` 부분을 변경한다.

이때, 이름은 **자신의_유저이름.github.io**로 설정해야 한다. (ex : doheejin.github.io)

<img src="/assets/images/jekyll_3.PNG" title="Jekyll settings">

아래로 스크롤을 내리면 publish 된 사이트가 나올 것이다. 블로그 생성 완료 \^o^/
<img src="/assets/images/jekyll_4.PNG" title="Jekyll settings">

다음 포스트에서는 로컬에서 블로그 작업 환경을 구성하는 방법을 정리하고자 한다.

----- 