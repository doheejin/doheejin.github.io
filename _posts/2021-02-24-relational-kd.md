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
vision 분야에 적용한 논문이라 자세한 실험 과정보다 방법론에 집중해서 정리해보았다.

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
보편화된 KD 방법들은 아래의 목적함수(objective function)를 최소화하는 것으로 설명될 수 있다.
<img src="/assets/images/kd_1.PNG" title="generalization of conventional kd">
여기서 l은 teacher와 student의 차이를 penalize 하는 손실함수이다.
예를 들어, [Hinton et al.](https://arxiv.org/abs/1503.02531) 이 제안한 knowledge distillation에서는 student와 teacher의 pre-softmax outputs를 사용하며 softmax를 취하고 Kullback-Leibler divergence를 loss function으로 사용한다.

<img src="/assets/images/kd_2.PNG" title="Hinton et al's KD">

또한, [Romero et al.]()에서는 f_T와 f_S를 각각 teacher와 student의 hidden layer output으로 두고 loss function을 Euclidean distance로 사용하였다. 이때, student의 hidden layer output 크기가 teacher 보다 더 작기 때문에 다른 차원을 이어주기 위한 linear mapping(β)이 도입되었다.
<img src="/assets/images/kd_3.PNG" title="Romero et al's KD">
이처럼, 대부분의 방법들을 (1)번 식으로 일반화시킬 수 있다. <em>본질적으로 보편적인 KD는 teacher모델의 individual output들을 student에게 전달하기에, 본 논문에서는 이러한 conventional KD를 **IKD**라고 부른다</em>.


## Methods

### Relational Knowledge Distillation(RKD)
RKD는 teacher의 아웃풋 데이터들 간의 상호 관계를 이용해 **structural knowledge**를 전달하는 것에 목표를 둔다. IKD와 다르게, 모든 n-tuple 데이터들마다 **relational potential (ψ)**을 계산하고, 이 potential을 이용해 teacher에서 student모델로 정보를 전달한다.

<img src="/assets/images/kd_4.PNG" title="RKD">
여기서 (x1, x2, ..., xn)는 X_N으로부터 구성된 n-tuple들이고 ψ는 주어진 n-tuple들 간의 relational energy를 측정하는 relational potential 함수이다. l은 teacher와 student간의 차이를 penalize하는 손실함수이다. RKD는 포텐셜 함수를 이용해 student가 teacher와 동일한 relation 구조를 가지도록 학습시키기 때문에, high-order 속성들을 포함한 knowledge를 transfer 할 수 있다.

어떤 potential 함수를 사용하느냐가 RKD에서 중요한 요소인데, 이 연구에서는 2가지의 간단하지만 효과적인 포텐셜 함수와 그에 상응하는 loss들을 제안한다. 첫째는 **pairwise** relation을 추출하는 **distance-wise** loss 이고, 둘째는 **ternary** relation을 추출하는 **angle-wise** loss이다.

#### 2.1. Distance-wise distillation loss
한 쌍(pair)의 훈련 examples가 주어졌을 때, distance-wise 포텐셜 함수(ψ_D)는 output representation 공간에서 두 example 간의 **유클리디언 거리**(Euclidean distance)를 측정한다. 
<img src="/assets/images/kd_5.PNG" title="distance-wise RKD : ψ_D">
이 식에서 µ는 미니배치 상에서 샘플링되는 모든 페어의 평균거리를 계산 한 값으로, 임베딩 스페이스 상의 포인트들 간 평균거리로 볼 수 있다. 즉 전체 식을 보면, **두 example 간의 상대적인 거리를 계산하기 위해** 임베딩 포인트들 간의 거리를 잰 후 그 값을 µ로 나눠주는 것이다.
<img src="/assets/images/kd_6.PNG" title="distance-wise RKD : µ">
teacher와 student 각각에서 측정된 distance-wise 포텐셜을 사용한 loss는 아래처럼 정의된다.
<img src="/assets/images/kd_7.PNG" title="distance-wise RKD">
궁극적으로 teacher와 student간의 structure 차이를 최소화 하는 것을 목표로 하기에, loss fucntion은 두 포텐셜 값 간의 차이를 줄이기 위해 **Hurber loss**를 사용한다.
<img src="/assets/images/kd_8.PNG" title="distance-wise RKD">

#### 2.2. Angle-wise distillation loss
triplet 의 example들이 주어졌을 때, angle-wise relational 포텐셜은 output representation 공간 상에서 세 example 사이의 각도를 측정한다.

<img src="/assets/images/kd_9.PNG" title="angle-wise RKD">

위의 angle-wise 포텐셜을 이용해 angle-wise distillation loss는 아래처럼 정의된다.

<img src="/assets/images/kd_10.PNG" title="angle-wise RKD">

angle-wise distillation loss는 angular 차이를 penalize 함으로써 훈련 example들 간의 관계를 transfer 한다. 각도가 거리보다 higher-order 속성이기 때문에, 훈련과적에서 student에게 더 유연함을 제공하며 relational information을 더 효과적으로 전달할 것으로 예상된다. 

## Experiments

## Results



## Conclusion

## Insights & Opinion


