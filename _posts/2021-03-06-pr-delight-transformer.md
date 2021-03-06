---
layout: post
title:  "[NMT] DeLIGHT: Deep AND LIGHT-WEIGHT TRANSFORMER"
date:   2021-03-05T14:25:52-05:00
author: Heejin Do
categories: "paper_review"
tags:	nmt deep_learning deep learning paper review transformer
---
> 논문 출처 (reference)
: Mehta, Sachin, et al. "DeLighT: Very Deep and Light-weight Transformer." arXiv preprint arXiv:2008.00623 (2020).
: published as a conference paper at ICLR 2021 

> 소스 코드 (Open Source)
: https://github.com/sacmehta/delight


이 논문에서는, <em>**더 적은 파라미터를 사용**</em>해 기존의 Transformer 모델보다 좋거나 비슷한 성능을 내는 light-weight Transformer인 **DeLighT**를 제안한다.

## Abstract
DeLighT는 다음의 두 가지 영역에서 파라미터를 효율적으로 할당한다.

- 각 Transformer block들 **내부** (더 깊고 경량화된 DeLighT transformation을 사용함으로써)
- block들 **사이** (**block-wise scaling**을 통해 input 근처에선 얇고 좁은 DeLighT 블록을, output 근처에선 넓고 깊은 블록을 가능하게 함)

DeLighT 네트워크는 일반 Transformer에 비해 더 적은 파라미터와 수식을 가짐에도 불구하고, 2.5에서 4배 정도 더 깊다. 
Machine Translation과 language Modeling task에 적용한 결과, 2~3배 정도 적은 파라미터로 베이스라인의 성능을 달성하거나 뛰어넘을 수 있다는 것을 증명했다.

## Introduction
어텐션 기반 트랜스포머 네트워크가 널리 쓰이면서, 모델 성능 향상을 위해 hidden layer 수를 늘리며 wider 하게 만들거나, 더 많은 트랜스포머 블럭들을 쌓으며 deeper 한 모델을 만드는 방법이 자주 사용되었다(EX. T5, GPT-3). 하지만 *<U>이런 scaling은 파라미터 수를 현저히 증가시키고 학습을 복잡하게 만들며 더 정교한 regularization을 요구한다.</U>* 이 논문에서는 attention을 기반으로 하는 **parameter-efficient** 아키텍쳐를 제안하며, 이런 모델은 더 **쉽게** widely and deeply scaling 될 수 있다. (결국 제안하려는 모델 또한 deep & wide 한데, 더 적은 파라미터를 쓴다는 것에 초점!)

DeLighT는 [Vaswani et al.(2017)](https://arxiv.org/pdf/1706.03762.pdf)를 확장한 모델이며, 더 적은 파라미터와 계산량으로 비슷하거나 나은 성능을 낸다. 그 핵심은 DeLighT transformation인데, 이는 **group linear transformers(GLTs)**와 블럭의 너비와 깊이를 효율적으로 변형하기 위한 **expand-reduce 전략**을 사용한다. 또한 다른 그룹 간 정보 교환을 위해 feature shuffling(conv networks에서의 cahnnel shuffling과 비슷)을 사용하는데, 이는 GLT가 locality하기 때문이다.

이런 wide & deep representation은 multi-head attention을 single head로, fee-forward layer를 light-weight로 대체할 수 있고, 이는 전체 네트워크의 파라미터와 연산을 줄여준다. 가장 중요한 것은, 트랜스포머와는 다르게 depth와 width를 input size랑 구분되도록 하는 점인데, 이로써 더 효율적인 파라미터 할당이 가능해진다. 

## Backgrounds

#### 1. Improving Transformers
트랜스포머를 발전시키기 위한 많은 시도들이 있었는데, 그 첫 시도는 긴 input sequence에서 self attention을 계산하기 위한 것이다(이런 기법은 이 논문의 아키텍쳐와 결합될 수 있음). 두번째 시도는 multi-head attention을 설명하려는 것이고, 세번째 시도는 더 좋은 representation을 배움으로써 트랜스포머를 발전시키려는 것이다. 이런 기존 연구들과 다르게 <U>**본 연구는 block level과 across block 모두에서 효율적으로 파라미터를 할당하는 것이 가능하다는 것을 보여준다.**</U>

#### 2. Model Scaling
Model Scaling은 sequence model의 성능향상을 위한 보편적 방법인데, width-wise scaling에서는 모델의 차원이 증가하고, depth-wise에서는 블럭들이 깊게 stack 된다. 두 가지 모두 block 내부 파라미터 개수는 동일하게 유지되기에 sub-optimal solution이 필요해진다. <U>**이 논문에서는 variably-sized 블록과 파라미터의 효율적 할당을 가능케 하는 block-wise scaling을 제안한다.**</U> 결론적으로 (1) 인풋 근처의 얕고 좁은 DeLight block과 output 근처의 깊고 넓은 DeLighT block이 제일 좋은 성능을 보였고, (2) block-wise scaling과 모델 scaling을 결합한 모델이 모델 scaling만 한 것에 비해 더 좋은 성능을 보였다. CNN 또한 인풋 근처에서 얕고 좁은, 아웃풋 근처에서 깊고 넓은 representation을 가지지만, 고정된 횟수의 연산을 하는데 반해, <U>**여기서 제안하는 block-wise scaling은 각 레이어와 블록에서 다양한 수의 연산을 사용한다.**</U>

#### 3. Improving Sequence Models
sequence model의 발전을 위한 연구들도 많은데, 첫번째는 정확도를 증가시키기 위해 더 좋은 token-level representation을 사용하는 방법들(BPE, DeFINE 등)이고, 두번째는 효율성을 늘리기 위한 방법들(compression, distillation, pruning)이다. 그 중 DeLighT와 비슷한 선행연구는 expand-reduce 전략을 사용하는 DeFINE이다. 하지만 <U>**DeLighT는 더 많은 그룹을 사용**</U>하기에 DeFINE 보다 더 적은 파라미터로 더 좋은 성능을 낸다. 

## Methods 
> DeLIGHT : Deep and Light-Weight Transformers

이 논문은 트랜스포머 아키텍쳐를 확장하고, deep & light-weight 트랜스포머인 DeLighT를 제안한다. 이 모델은 (1) 더 넓은 representation을 배우기 위해 deep & light-weight expand-reduce 변형을 사용하고, (2) multi-head attention과 FFN 각각을 single-head와 light-weight FFN으로 대체할 수 있도록 한다. 

#### 1. 

## Results
#### 1. Single Teacher Model


#### 2. Ensemble Teacher Model

#### 3. Oracle BLEU Teacher Model

<img src="/assets/images/ensemble_3.PNG" title="result3">

#### 4. Reducing Model Size

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


