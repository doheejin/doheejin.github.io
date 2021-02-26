---
layout: post
title:  "[vscode] vscode에서 원격서버 연동"
date:   2021-02-25T14:25:52-05:00
author: Heejin Do
categories: vscode
tags:	vscode envs server
---

putty를 이용해 원격서버에 접속할 수도 있지만, vscode를 이용해 원격서버에 접속하면 파일에 바로 접근할 수 있어 코딩이 더 편리하다.(이외에도 다양한 IDE 사용, extension 사용, 쉬운 UI 등 장점이 많음)
이 포스트에서는 vscode에서 <em>**원격서버에 접속하는 방법**</em>과, 매번 귀찮게 비밀번호를 치지 않아도 <em>**자동 접속이 되도록 설정**</em>하는 법에 대해 다루어보았다.

## 원격서버에 접속하는 법

우선 local에서 vscode 실행 후 확장(`ctrl`+`shift`+X)에서 `Remote Development`를 다운받는다.

<img src="/assets/images/server_1.PNG" title="Remote Development">

`F1`을 누른 후 **Remote-SSH: Connect to Host...** 를 클릭한다.

<img src="/assets/images/server_2.PNG" title="Remote-SSH">

Add New SSH Host 를 누르고 자신이 접속하려고 하는 서버의 정보를 입력한다.

이때 **ssh 계정명@ip주소(또는 도메인)** 형식으로 입력하고, 비밀번호를 치면 접속이 된다.

<img src="/assets/images/server_3.PNG" title="server info">

간단하게 원격 서버에 접속이 되었지만, 서버 내부에서 새로운 폴더에 접근하거나 특정 시간이 지나면 비밀번호를 다시 입력하라고 request를 받게 된다.
작업하는데 계속 비밀번호를 입력하기는 번거로우므로 로컬에서 접속 시 비밀번호를 입력하지 않아도 자동으로 접속되는 설정을 해두었다. (아래에 정리)

## 자동 접속 설정 (비밀번호 입력 생략)

순서는 아래의 3단계로 정리해볼 수 있다.

**(1) 로컬에서 ssh-key 발급 → (2) 발급된 ssh-key파일을 서버에 등록 → (3) config 파일에서 키파일을 인식하도록 설정**

### 1. 로컬에서 ssh-key 발급
Windows PowerShell 실행 후 `ssh-keygen -t rsa -b 4096` 을 입력해 ssh-key를 발급받는다. 
입력하라고 나오는 부분에서는 다 Enter를 쳐서 넘어가면 키가 발급된다. 이제 ` Get-Content .\.ssh\id_rsa.pub `를 입력해 키 파일의 내용을 확인하고 **복사**한다.

<img src="/assets/images/server_4.PNG" title="ssh-key input">




### 2. 발급된 ssh-key파일을 서버에 등록



### 3. config 파일에서 키파일을 인식하도록 설정





----- 