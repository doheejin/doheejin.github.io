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
sequence model의 발전을 위한 연구들도 많은데, 첫번째는 정확도를 증가시키기 위해 더 좋은 token-level representation을 사용하는 방법들(BPE, DeFINE 등)이고, 두번째는 효율성을 늘리기 위한 방법들(compression, distillation, pruning)이다. 그 중 DeLighT와 비슷한 선행연구는 expand-reduce 전략을 사용하는 DeFINE이다. 하지만 **<U>DeLighT는 더 많은 그룹을 사용</U>**하기에 DeFINE 보다 더 적은 파라미터로 더 좋은 성능을 낸다. 

## Methods 
> DeLIGHT : Deep and Light-Weight Transformers

이 논문은 트랜스포머 아키텍쳐를 확장한, deep & light-weight 트랜스포머 DeLighT를 제안하고
: 이는 다음의 특성을 지닌다.
: (1) 더 넓은 representation을 배우기 위해 **deep & light-weight expand-reduce** 변형 사용
: (2) multi-head attention, FFN 각각을 **single-head, light-weight FFN**으로 대체할 수 있도록 함
: (3) attention 차원이 depth/width와 구분돼 block-wise scaling으로 representation을 효율적으로 학습

<img src="/assets/images/delight.PNG" title="delight">

#### 1. Delight Transformation
DelighT transformation은 d_m 차원의 인풋 벡터를 고차원 공간(d_max = w_m･d_m)으로 확장하고, 이를 다시 d_0차원의 아웃풋 벡터로 축소한다.
이 확장-축소 과정은 N layer의 그룹 transformation을 통해 이뤄지고(Figure 1d), 이는 group linear transformation(GLT)이다.
인풋의 특정 파트에서 아웃풋을 도출해 local representation을 학습하고, feature-shuffling을 통해 서로 다른그룹들 간 정보를 공유함으로써 global representation을 학습한다.

일반적으로 트랜스포머의 표현력과 역량을 향상시키기 위해 인풋 차원인 d_m을 키우는 접근을 주로 하지만, 이는 operation 수를 늘린다. 따라서 이 연구에서는 표현력을 향상시키는 방법으로 확장-축소 방법을 사용해 intermediate transformations의 depth & width를 키웠다. 이로써 더 작은 차원의 어텐션 계산과, 더 적은 operation이 가능해졌다. 

더 자세히 보면 F function은 인풋 X를 받고, 그것을 분할해 l번째 레이어의 g_l 그룹으로 보낸다. F함수는 X_i를 W_l_i와 b_l_i를 이용해 선형변환해 아웃풋 Y_l을 만든다. 

<img src="/assets/images/delight_2.PNG" title="delight_2">

#### 2. Delight Block
<Figure 1b>를 보면 기존 트랜스포머와 다르게 single-head attention과 light-weight한 FFN을 사용했음을 알 수 있다.
: 자세한 설명은 논문 참고.

#### 3. Block-Wise Scaling
더 깊고 넓은 네트워크를 만들기 위해, 이 논문에서는 단순히 모든 블록에 동일한 수의 파라미터를 할당하는 depth나 width wise scaling 대신, block level로 scaling을 했다.

<img src="/assets/images/delight_3.PNG" title="delight_3">

## Experiments & Results
Machine Translation과 Language Modeling 태스크에 적용했다. 결론적으로 두 태스크 모두에서 기존 베이스라인 모델보다 더 적은 파라미터로 비슷하거나 더 뛰어난 성능을 낼 수 있음을 증명해준다. 구체적 setting과 조건은 논문을 통해 확인 가능하다. 

## Conclusion
이 논문에서는 block 내부와 block들 사이에서 파라미터들을 효율적으로 할당할 수 있게 하는, deep & light-weight 트랜스포머 DeLighT를 소개한다. 기존 모델과 비교했을 때 DeLighT는 (1) deep & light-weight 하고, (2) 비슷하거나 더 나은 성능을 보인다.

## Insights & Opinion
트랜스포머 모델을 변형하기 위한 기존의 여러 시도들이 **'어떤 조각을 어떻게 바꿔 끼워 구조를 바꿀지'**에 초점을 두었다면, 이 논문은 **기본 구조를 유지하되 각 구성요소들을 변형**시켰다는 점에서 차별점이 있다. 저자들이 풀고자 하는 문제는 <em>기존 트랜스포머 모델의 성능을 올리기 위해 단순히 depth나 width를 늘렸을 때 파라미터수 & 연산량이 증가하고 성능은 막상 크게 증가하지 않는 현상</em>이다. 이 문제를 해결하기 위해 **deep & light-weight transformer**인 DelighT를 제안한다. 어떤 방식으로 deep한지, 왜 light-weight인지를 살펴보면 그 구체적 방법론을 알 수 있다. 우선 **deep** 한 모델을 만들기 위해 확장-축소(expand-reduce) 변형을 사용했고(여기서 이용한 것이 GLT & block-wise scaling), 모델을 **light-weight** 하게 만들기 위해 multi-head를 single-head로, FFN을 light-weight FFN으로 대체했다. 결과를 보았을 때, 훨씬 적은 파라미터로 동일하거나 더 좋은 성능을 낼 수 있다는 것이 확연히 보였지만 훈련 시간이나 속도에 대한 언급은 없어 아쉬웠다. 따라서 DelighT 모델을 사용해 직접 실험을 해봐야겠다는 생각이 들었다. 이 DelighT 모델도 기존의 트랜스포머 모델처럼 다른 조각을 끼워맞추거나 빼거나 하는 식으로 추가 변형이 가능 할 것 같다는 점에서 확장성이 있을 듯 하다. 
