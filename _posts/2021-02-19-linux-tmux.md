---
layout: post
title:  "[Linux] tmux 다운로드 & log 기록 남기기"
date:   2021-02-18T14:25:52-05:00
author: Heejin Do
categories: linux
tags:	linux envs tmux log
---

원격서버를 사용하다가 갑자기 연결이 끊기거나, 창이 꺼지는 경우가 발생하면 작업이 비정상적으로 종료 될 수 있다.
그런 상황을 방지하기 위해 필자는 원격 연결이 꺼지더라도 서버에서 돌린 코드가 그대로 실행되도록 할 수 있는 **tmux**를 사용한다.
이 포스트에서는 리눅스 환경에서 **tmux** 를 사용 할 수 있는 방법과 **tmux**에서 **로그 기록** 남기는 방법을 정리해보았다 ( •̀ ω •́ )✧

### 다운로드
우선 [Anaconda사이트](https://anaconda.org/conda-forge/tmux)에 접속해 tmux 패키지를 다운받는다.
* <em>**주의)** 이때, conda로 다운로드 해야 tmux 에서 log 기록을 남기기 위한 작업을 이어갈 수 있다.</em>

<img src="/assets/images/tmux_1.PNG" title="tmux download">

### tmux 기본 명령어
아래는 tmux에서 자주 사용하는 기본 명령어들이다. 이 명령어들만 알고 있으면 기본적인 작업은 수행 할 수 있다.

- 새 세션 생성 : tmux new -s `session-name`
- 세션 중단하기 (빠져 나오기) : ctrl + B (prefix) → d
- 세션 다시 시작 (이어서 작업) : tmux attach -t `session-number` or `session-name`
- 세션 목록 확인 : tmux ls
- 세션 완전히 종료 : tmux kill-session -t `session-number` or `session-name`

### 로그 기록 남기기

#### 1. git clone
아래 명령어를 통해 자신의 작업환경에 git 레포지토리를 복제한다.

{% highlight bash %}
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
{% endhighlight %}

#### 2. .tmux.conf 파일 만들기
우선 자신의 작업 디렉토리 ~ 아래에 .tmux.conf 파일을 만들고,

{% highlight bash %}
mkdir ~/.tmux.conf
{% endhighlight %}

아래 내용을 추가한다.
{% highlight markdown %}
#--- Plugins ---#

#logging

#it requires at least version 1.9

#https://github.com/tmux-plugins/tmux-logging

#
set -g @plugin 'tmux-plugins/tmux-logging'
set -g history-limit 50000  # enhance the limitation of saving the complete history (prefix + alt + shift + p)

#Initialize TMUX plugin manager (keep this line at the very bottom of tmux.conf)

run '~/.tmux/plugins/tpm/tpm'
{% endhighlight %}

#### 3. tmux source .tmux.conf
아래의 내용을 순서대로 입력한다.
tmux → `ctrl`+B<em>(prefix)</em> → `shift`+i → tmux source .tmux.conf

#### 4. 사용하기
로그기록을 남길 때는 prefix(`ctrl`+B) 후에 `shift`+P를 누르면 기록 남기기가 시작되고,

output file 은 `로그파일명.tmux` 의 형태로 저장된다.

----- 