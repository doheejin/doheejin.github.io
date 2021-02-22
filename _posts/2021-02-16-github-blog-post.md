---
layout: post
title:  "[github] 깃허브 블로그 구성(3) - post 추가 및 업데이트 확인하기"
date:   2021-02-15T14:25:52-05:00
author: Heejin Do
categories: github
tags:	github blog vscode windows MacOS
---

지난 포스트에서는 Jekyll 테마를 이용해 자신의 블로그를 만드는 법에 대해 알아보았다.

이번 포스트에서는 로컬에서 블로그를 쉽게 관리하기 위해 환경 설정 하는 과정을 정리해보았다.

Windows와 MacOS 두가지 환경을 다루었다.
## 윈도우 (Windows)

### 1. 로컬에 작업용 폴더 생성
repository를 복제할 작업용 폴더를 하나 생성한다.
<img src="/assets/images/local_1.PNG" title="New Folder">

### 2. vscode에서 repository 복제
vscode를 열어 왼쪽의 `가지(branch)`모양 아이콘을 클릭한다.

<img src="/assets/images/local_2.PNG" title="New Folder">

`레포지토리 복제`를 클릭하고, 자신이 만든 블로그 레포지토리를 선택한다.

<img src="/assets/images/local_3.PNG" title="New Folder">

`1.`에서 만들어 둔 폴더를 클릭해 연결한다.

레포지토리 복제가 끝나면 다음과 같은 메시지가 뜨는데,

<img src="/assets/images/local_4.PNG" title="New Folder">

`열기`를 클릭해보면, 레포지토리가 잘 복제되었음을 확인 할 수 있다.
이제 해당 폴더의 파일을 수정/추가/삭제 해가며 자신만의 블로그를 꾸미면 된다!

<img src="/assets/images/local_5.PNG" title="New Folder">

------------------

### (추가) - git 초기 설정
기존에 vscode에서 git을 연동한 적이 없다면, 추가 설정이 필요 할 것이다.

터미널(vscode의 터미널이나 윈도우의 cmd)에서 아래의 내용을 입력한다.
{% highlight bash %}
git init 
git config --global user.name "닉네임" 
git config --global user.email "이메일"
{% endhighlight %}

위 초기화를 마치고 레포지토리 복제가 완료되었다면, remote add를 통해 원격을 연결한다.

{% highlight bash %}
git remote add <작업이름> <블로그url>
{% endhighlight %}

아래는 추가/삭제/수정사항을 반영할 때 add→commit→push를 위해 자주 쓰이는 명령어이다.

{% highlight bash %}
git status   # 현재 정보 확인
git add 파일/폴더   # commit 할 파일/폴더 추가
git commit -m '내용'   # commit 하기
git push -u <작업이름> master   # 원격에 변경사항 반영
{% endhighlight %}

## 맥 (MacOS)



