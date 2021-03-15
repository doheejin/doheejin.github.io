---
layout: post
title:  "[Linux] Slurm 스케줄러 활용법"
date:   2021-02-18T14:25:52-05:00
author: Heejin Do
categories: linux
tags:	linux envs slurm cluster
---

이번 포스트에는 리눅스 환경에서 slurm 스케줄러를 활용하는 방법을 간단히 정리했다.

## 작업 스크립트 작성
클러스터에서 작업을 돌릴 때는 bash 스크립트(.sh 형태)를 만들어서 실행시키는게 편하다.

bash 스크립트에서 slurm 명령은 `sbatch`를 통해 이루어진다.

### 1. sbatch
#sbatch 뒤에 옵션을 달면, slurm 명령어가 실행된다.

{% highlight bash %}
#!/bin/bash

#SBATCH -J ensemble   # job name
#SBATCH -o ensemble.%j.out   # standard output and error log
#SBATCH -p A100-pci           # queue  name  or  partiton name
#SBATCH -t 70:00:00               # Run time (hh:mm:ss) 

~~~기타 명령어~~~
{% endhighlight %}

- `-J` : 작업의 이름 지정 // 이름을 명시하지 않으면 작업 이름은 작업 스크립트명이 됨(이름.sh)
- `-o` : 작업 스크립트의 출력 파일을 지정 // %j는 작업의 고유번호를 의미
- `-p` : 파티션 이름
- `-t` : 작업 시간 설정

### 2. GPU 사용 설정

{% highlight bash %}
## 1. gpu 개수 설정

## gpu 1장
#SBATCH   --gres=gpu:1

## gpu 2장
#SBATCH   --gres=gpu:2


## 2. 노드 지정

## 노드를 지정할 경우
#SBATCH  --nodelist=n10

## 노드를 지정하지 않을 경우
#SBATCH   --nodes=1

## 3. task, node 설정

## gpu 가 2장이면
#SBTACH   --ntasks=2
#SBATCH   --tasks-per-node=2
#SBATCH   --cpus-per-task=1

## gpu 가 4장이면 
#SBTACH   --ntasks=4
#SBATCH   --tasks-per-node=4
#SBATCH   --cpus-per-task=1

{% endhighlight %}

### 3. 관리자 옵션

{% highlight bash %}
#SBATCH --ntasks=1        # Run on a single CPU
#SBATCH --mem=2gb         # Limit of Memory
#SBATCH --time=00:30:00   # Limit of Time
{% endhighlight %}

- 관리자가 이런 옵션이 포함되도록 설정 한 경우, 작업 스크립트에도 명시해줘야 함

------

## 작업 제출, 확인, 삭제

### 1. 작업 제출

작업 제출 또한 sbatch 명령어를 통해 이루어진다. 앞서 만들어둔 `파일명.sh` 파일을 `sbatch 파일명.sh` 을 이용해 제출한다.

{% highlight bash %}
$ sbatch job_script.sh
Submitted batch job 1465
{% endhighlight %}

### 2. 작업 확인
현재 던져진 작업 목록을 확인하기 위해서는 squeue 명령어를 이용하면 된다.

{% highlight bash %}
$ squeue

JOBID  NAME          STATE     USER     GROUP    PARTITION       NODE NODELIST CPUS TRES_PER_NODE TIME_LIMIT  TIME_LEFT  
6539   ensemble      RUNNING   dhj1     usercl   TITANRTX        1    n1       4    gpu:4         3-00:00:00  1-22:57:11 
6532   bash          PENDING   gildong  usercl   2080ti          1    n2       1    gpu:8         3-00:00:00  2-03:25:06
{% endhighlight %}

- `JOBID` : 제출한 작업의 식별 번호
- `PARTITION` : 현재 작업이 제출된 파티션의 이름 (slurm에서의 partition은 SGE에서 queue와 같은 개념임)
- `NAME` : 작업의 이름
- `USER` : 작업을 제출한 리눅스 계정명
- `STATE` : 현재 작업의 상태 (running: 작업 실행 중, pending: 기다리는 상태)
- `NODELIST` : 이 작업을 수행될 수 있도록 할당된 컴퓨터의 노드를 의미
- `TRES_PER_NODE` : 이 작업에 할당된 gpu 수
- `TIME_LIMIT` : 이 작업에 걸린 제한시간 (작업 가능 한 최대 시간)
- `TIME_LEFT` : 작업 가능한 남은 시간


### 3. 작업 삭제

{% highlight bash %}
$ scancel 6539
{% endhighlight %}

### 4. 작업 내용 구체적으로 확인
{% highlight bash %}
scontrol show job
{% endhighlight %}

-----