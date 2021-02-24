---
layout: post
title:  "[CV-KD] Relational Knowledge Distillation"
date:   2021-02-23T14:25:52-05:00
author: Heejin Do
categories: "paper_review"
tags:	vision CV knowledge distillation paper review relational knowledge-distillation RKD
---

> 논문 출처 (reference)
: Park, W., Kim, D., Lu, Y., & Cho, M. (2019). Relational knowledge distillation. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition (pp. 3967-3976).

이 논문은 데이터들 간의 상호 **관계(mutual relations)**를 transfer 하는 **Relational** Knowledge Distillation을 제안하는 논문이다.
vision 분야에 적용한 논문으로 

## Abstract
**Relational Knowledge Distillation(RKD)**은 teacher 모델의 데이터들 간 관계를 transfer 하는 distillation 기법으로, 기존의 KD 방법론들에서 student 모델이 teacher 모델의 output activation 자체를 모방했던 것과 구분된다. RKD의 적용을 위해 본 논문에서는 **<em>distance-wise</em>** 와 **<em>angle-wise losses</em>** 를 제안하며 이는 관계에서 구조적으로 다른점들을 penalize 한다. 여러 태스크에 적용한 실험 결과, 제안하는 방식은 student 모델의 성능을 향상시켰고, 특히 metric learning에서는 SOTA 성능을 달성하면서 동시에 teacher 모델의 성능을 뛰어넘었다. 

## Introduction

딥러닝에 기반한 컴퓨터 비전과 AI 분야가 발달하면서 SOTA 모델들이 높은 cost의 computation과 memory를 요구하기 시작했다. 그에 따라 크고 복잡한 teacher 모델에서 상대적으로 작은 student 모델로 knowledge를 transfer 하는 방법론들이 등장했다. 이에 대해
- (1) 학습된 모델에서 knowledge를 구성하는 요소가 무엇인가? **(What)**
- (2) knowledge 를 어떻게 한 모델에서 다른 모델로 전달하나? **(How)**

의 2가지 주요 질문이 있는데, KD(Knowledge Distillation)는 주로 (1) knowledge를 input에서 output으로의 학습된 mapping으로 보고, (2) teacher model의 output을 target으로 두어 student 모델을 학습시킴으로써 knowledge를 transfer 한다. 최근, KD는 student 모델 뿐만 아니라 teacher 모델도 그 자체로 self-distillation을 이용해 성능 향상을 이루는 등 그 효과가 증명되었다.

이 연구에서는 KD를 semiological system에서 구조적 관계에 초점을 맞추는 **Linguistic Structuralism**의 관점으로 재해석했다. 이는 Knowledge를 구성하는 요소는 사실 개별 데이터들이 아니라 학습된 representation들의 **'관계'**로 더 잘 표현될 것이라는 생각에서 기인한다. 중요한 정보는 data embedding space 상에서 데이터들의 구조에 있을 것이라는 주장을 바탕으로, 저자는 <em>individual output 그 자체가 아닌 output들의 **structural relation**들을 transfer 하는 Relational Knowledge Distillation을 제안한다.</em>


## Backgrounds

### Conventioanl knowledge Distillation
knowledge distillation은 student model의 예측을 teacher network의 예측에 맞추어 훈련하는 방법이다.
이 논문에서는 전체 훈련 데이터를 teacher 네트워크로 번역한 결과(predictions)를 모았고, 
이를 통해 student 네트워크가 teacher 모델을 흉내내는데 사용하기 위한 새로운 reference를 생산했다.

본문에서 사용되는 **Forward translation**을 위한 두 가지 방법이 있는데,
첫째는 student 모델을 original source로만 훈련하는 것이고,
둘째는 original source에 더해, 번역 결과를 추가적인 훈련 데이터로 사용하는 것이다. 

## Methods

### Relational Knowledge Distillation

#### 2.1. Distance-wise distillation loss

#### 2.2. Angle-wise distillation loss

## Experiments

## Results


<img src="/assets/images/ensemble_1.PNG" title="result1">


## Conclusion

## Insights & Opinion


