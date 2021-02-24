---
layout: post
title:  "[NMT-KD] Ensemble Distillation for Neural Machine Translation"
date:   2021-02-16T14:25:52-05:00
author: Heejin Do
categories: "paper_review"
tags:	nmt knowledge distillation paper review ensemble knowledge-distillation KD
---
> 논문 출처 (reference)
: Freitag, M., Al-Onaizan, Y., & Sankaran, B. (2017). Ensemble distillation for neural machine translation. arXiv preprint arXiv:1702.01802.


이 논문의 핵심 아이디어는, NMT에 knowledge distillation을 적용할 때
1. **ensemble**을 적용하는 것
2. **data filtering** 을 적용하는 것
이렇게 크게 두 가지이다.

> 내용을 보면 알겠지만 사실 총 4가지 방식의 연구가 진행되었지만, 결과적으로 가장 좋은 모델이 ensemble + data filtering 을 적용한 모델이었기에 이 두 가지를 핵심 요소로 강조하는 듯 하다.



## Abstract
기존 NMT 에서는 훈련 시간이 많이 든다는 단점이 있었다 (time expensive).

1. 이 논문에서는 우선, **ensemble 모델**과 **oracle BLEU teacher network**의 번역 퀄리티를 어떻게 single NMT system (student model)로 transfer 할 수 있는지 보여준다.
- 여기서, student 모델과 동일한 architecture를 가진 teacher 모델을 사용했는데 성능 향상을 보임

2. 위 과정에서 student 모델을 학습하는 것이 여전히 expensive 했기에, 저자는 두번째로 **data filtering method**를 제안한다. 
- 이는 teacher 의 knowledge 에 기반한 method 이며, 속도 향상 뿐만 아니라 번역 퀄리티 향상에도 기여함


## Introduction
이 논문의 main contribution을 정리하면 다음과 같다. 
- ensemble & oracle BLEU teacher model에 knowledge distillation을 적용함
- student model이 teacher model과 동일한 architecture를 가질때, knowledge distillation을 효율적으로 적용 할 수 있는 방법을 보임
- 간단하고 쉬운, reproducible 한 접근을 소개함
- training data를 teacher model 의 지식(knowledge)으로 filtering 함
- student model에서 다른 파라미터 초기화 설정들을 비교함


## Backgrounds

#### 1. knowledge Distillation
knowledge distillation은 student model의 예측을 teacher network의 예측에 맞추어 훈련하는 방법이다.
이 논문에서는 전체 훈련 데이터를 teacher 네트워크로 번역한 결과(predictions)를 모았고, 
이를 통해 student 네트워크가 teacher 모델을 흉내내는데 사용하기 위한 새로운 reference를 생산했다.

본문에서 사용되는 **Forward translation**을 위한 두 가지 방법이 있는데,
첫째는 student 모델을 original source로만 훈련하는 것이고,
둘째는 original source에 더해, 번역 결과를 추가적인 훈련 데이터로 사용하는 것이다. 

#### 2. Teacher Networks
두 가지 버전의 teacher model을 사용했는데 첫째는 **Ensemble Teacher Model**이고 둘째는 **oracle BLEU Teacher Model**이다.

1) **Ensemble Teacher Model**
- 여러개의 모델들을 동시에(parallel)하게 학습시키고 그 예측값들을 합쳤다. 이 때, 개별 모델들의 probability 평균을 구하는 방법을 사용했다. 
이 연구에서는 6개 모델의 ensemble을 사용했으며 모든 모델은 동일한 데이터와 최적화 방법을 따르지만, 파라미터들을 랜덤으로 초기화한다는 점에서만 차이가 있다.

2) **oracle BLEU Teacher Model**
- 조건부 확률을 최대화하는 것을 목표로 번역을 생성하는 left-to-right beam-search decoder를 사용했다. 또한, distillation 접근법에서 parallel data의 forward translation을 생산했다. 이 때, 마지막 후보 목록에서 로그 확률이 가장 높은 것이 아닌, **BLEU level이 가장 높은 문장을 선택**했다.

#### 3. Data Filtering
기계번역에서 언어쌍 데이터들은 주로 웹에서 크롤링되어 non-parallel 한 정보를 포함할 때가 많다. 또한 하나의 소스 문장은 배치나 단어의 형태가 다른 여러개의 문장으로 번역될 수 있다. 훈련 코퍼스에 이러한 노이즈가 많이 포함될수록 훈련 복잡도가 증가하기에, 이 논문에서는 full parallel 데이터를 teacher model로 학습하고 높은 TER 점수를 가진 문장은 훈련 데이터에서 제외시키는 data filtering 방법을 적용했다.

## Experiments
WMT 2016 German→English 데이터로 학습했고, newstest2014를 validation 데이터로, newstest2015를 test 데이터로 사용했다.
모든 단계는 두 번의 과정을 거쳤는데, 우선 student 네트워크를 랜덤한 파라미터 초기화로 학습 시킨 이후, 베이스라인 모델의 마지막 파라미터로 학습을 이어갔다.

## Results
#### 1. Single Teacher Model
하나의 teacher 모델을 사용한 실험에서는 student 보다 더 강한 teacher 모델을 쓰지 않고 student와 동일한 모델을 teacher 모델로 사용했다. Forward Translation을 사용함으로써 student 모델이 더 강해지긴 했지만, 그것만으로는 모델이 크게 개선되지 않았다. Reference 와 forward translation을 같이 사용했을 때, BLEU 와 TER 에서 1.4 points 향상을 보였다. TER score 를 이용해서 data filtering 을 거친 실험(TER<0.8 이하인 문장만 이용)에서도 비슷한 결과가 나왔지만, 훈련 데이터를 12% 정도 줄임으로 인해 더 빠른 훈련이 가능해졌다.

<img src="/assets/images/ensemble_1.PNG" title="result1">

#### 2. Ensemble Teacher Model
6개의 모델을 ensemble 한 것을 teacher 모델로 사용한 실험 결과이다. Forward Translation만 사용했을 때는 BLEU와 TER 각각에서 1.4, 1.9 포인트 향상을 보였고, 원본 reference와 같이 사용했을 때는 추가로 0.3 BLEU 포인트 향상을 보였다. TER score 를 이용해서 data filtering 을 거친 실험에서는 BLEU와 TER 각각에서 2, 2.2 포인트가 증가했다. 

<img src="/assets/images/ensemble_2.PNG" title="result2">

#### 3. Oracle BLEU Teacher Model
2번 실험에서와 동일한 ensemble 모델을 사용했는데 highest log probability 를 선택하는 대신, highest sentence level BLEU 를 가진 문장을 선택했다는 점에서 차이가 있다. Forward Translation만 사용했을 때 BLEU와 TER 각각에서 1.1, 1.2 포인트 향상을 보였고, 원본 reference와 같이 사용했을 때는 BLEU와 TER 모두 1.5 포인트 향상을 보였다. **<em>하지만, 2번의 Ensemble 모델에 비해 결과가 조금 더 낮았다.</em>**

<img src="/assets/images/ensemble_3.PNG" title="result3">

#### 4. Reducing Model Size
**더 낮은 차원**의 student 네트워크를 가르치기 위해 ensemble teacher 네트워크를 사용한 실험이다. 이때 단일모델과 비교했을 때 번역 성능에 손실이 가지 않게 유지하면서, 워드 임베딩 사이즈를 620(이전 실험들)에서 150으로, 히든 레이어 사이즈를 1000에서 300으로 줄였다. 심지어 성능이 BLEU 0.4, TER 0.6 만큼 더 높았다.

<img src="/assets/images/ensemble_4.PNG" title="result4">

## Conclusion
이 논문은 knowledge distillation을 여러 teacher 네트워크를 이용해 적용한 연구를 담았다. 더 구체적으로 세분화하면 다음과 같다.
1. Student 모델과 동일한 teacher 모델을 사용할 때 어떻게 성능 향상을 가져올 수 있는지 증명
- By combining Forward Translation & Original Reference

2. 6개의 단일 모델을 ensemble한 것을 teacher 모델로 지정하여 student 네트워크의 성능 향상
- Forward Translation 에서 얻어진 TER 점수를 반영한 data filtering 기법을 적용했을 때 best 성능을 달성함

3. Oracle BLEU 에 기반한 teacher 모델을 사용한 실험에서도 student의 성능 향상이 있었지만, ensemble teacher 모델에 비해 성능이 낮았음

4. Ensembel teacher 모델을 사용해 student 네트워크에서 번역 성능을 유지하면서도 사이즈를 줄이는 방법을 보임

## Insights & Opinion
NMT에 Knowledge Distillation을 적용한 논문을 찾다가 발견한 7개 논문 중 하나이다. 후속 논문들을 먼저 읽고 이 논문을 접해서 그런지 knowledge distillation의 적용 방법론 측면에서는 이미 당연하게 사용되어지는 것들을 나열해놓은 느낌이 들었다. 결국 기존에 쓰이던 knowledge distillation을 적용했는데, teacher 모델을 어떤 형태로 만들 것인가에 연구의 초점이 맞춰져 있는 것으로 보인다. Data filtering의 경우 filtering 이후의 과정만 보면 훈련 속도를 높일 수 있겠지만, forward translation을 한 번 거친 후의 filtering 결과를 다음 훈련에 사용한다는 점에서 전체 훈련 시간은 오히려 증가하는게 아닌가 하는 의문이 들었다. 여러 방향으로 실험한 결과 중에서 Embedding + Data Filtering의 결과가 가장 좋았다는 점이 주목할만한데 (<em>제목이 정해진 이유 아닐까</em>) filtering 정도를 `TER<=0.8`로 잡은 이유에 대한 언급이나 추가 reasoning이 없어서 아쉽다. result 2와 3을 비교한 부분에서는 역시 대부분의 연구에서 highest log probabilities를 기준으로 잡는 것에는 그럴만한 이유가 있구나 하는 생각이 들었지만, 다른 기준을 목표로 훈련시켜 보는 것도 하나의 trial 이 될 수 있다는 것을 배웠다.


