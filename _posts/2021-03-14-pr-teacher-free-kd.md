---
layout: post
title:  "[NMT] Improving Low-Resource Neural Machine Translation with Teacher-Free Knowledge Distillation"
date:   2021-03-13T14:25:52-05:00
author: Heejin Do
categories: "paper_review"
tags:	nmt row-resource teacher-free knowledge dsitillation kd
---
> 논문 출처 (reference)
: Zhang, X., Li, X., Yang, Y., & Dong, R. (2020). Improving Low-Resource Neural Machine Translation With Teacher-Free Knowledge Distillation. IEEE Access, 8, 206638-206645.


이 논문에서는, low-resource 기계번역을 위한 Teacher-free Knowledge Distillation을 제안한다.

## Abstract
일반적으로 Knowledge Distillation(KD)는 크고 복잡한 teacher 모델에서 경량화된 student 모델로 지식을 distill 하는 것을 목표로 한다. Teacher 모델의 정보 퀄리티나 성능이 student 모델에 영향을 미쳤고, 이로 인해 주로 강력한 모델이 teacher 모델로 사용되었다. 그러나, low-resource 언어쌍 번역에서는 강한 teacher 모델을 만드는 것이 불가능하다. 따라서, **이 논문에서는 low-resource NMT를 위해, 수동적으로 디자인된 regularization distribution을 가상의 teacher 모델로 두고, 이로부터 학습하는 Teacher-Free Knowledge Distillation을 제안**한다. 이 distribution은 단어간 유사성 정보를 담고 있을 뿐 아니라, 모델 훈련을 위한 효과적인 regularity를 제공한다.

## Introduction
Knowledge Distillation에서 보통 teacher 모델은 높은 성능과 강력한 학습력을 지니며, soft target을 제공함으로써 student 모델을 학습시킨다. Student와 teacher의 output 간 cross-entropy loss를 최소화함으로써 student가 teacher 모델을 모방하도록 한다. 이 과정에서 teacher의 soft target이 다른 카테고리 간 유사성 정보를 담고 있는 "dark knowledge"를 전이할 수 있다고 믿어진다. 대게 teacher는 high-capacity 모델로, student는 compact한 모델로 정해지고 knowledge를 전이함으로써 성능 저하 없이 student의 compactness로 인해 benefit 받기를 기대한다. **이 연구에서는 모델을 compress 하는 대신, student가 teacher와 동일하게 parameterized 되도록 새로운 관점으로 접근한다.**

현재, 대부분의 NMT 시스템은 Encoder-Decoder 시스템 하에서 bilingual parallel corpus를 사용한다. 하지만, 여전히 data가 충분하지 않고 더 적게 발생하는 사건들(자주 사용하지 않는 언어들)에 대해서는 학습이 잘 되지 않는다.

데이터 양이 적은 low-resource NMT에서는, 더 나은 teacher 모델을 만드는게 사실상 불가능하다. 따라서 **이 연구에서는, 수동으로 target distribution(target-side vocab에 있는 단어들 간 유사성에 기반)을 디자인 해 가상의 teacher 모델로 두는 teacher-free KD를 제안**한다. 저자들은 훈련 과정 중에 loss function을 증대시켜 vocab 간 다양성을 더 정확하게 모델링했으며, 훈련 향상을 위해 MLE에 기반한 KL(Kullback-Leibler) divergence loss를 사용했다. 추가적인 loss는 두개의 확률분포를 비교함으로써 계산 되는데, 하나는 모델 훈련 과정에서 얻어지는 확률분포, 다른 하나는 단어 유사성에서 학습된 사전 분포(prior distribution)이다.

상대적으로 row-resource인 Uyghur-Chinese와 Mongol-chinese 언어쌍으로 실험한 결과, 제안된 방법이 soft target 속 유사성 정보를 효과적으로 사용해 번역의 질을 향상시킨다는 것을 보여준다.

<img src="/assets/images/teacher-free-1.PNG" title="teacher-free">

## Backgrounds
#### 1. Neural Machine Translation
NMT 모델은 대부분 Encoder-Decoder 프레임워크를 가지며, source와 target 단어들은 모두 high latitude vector 표현으로 투영된다. **Encoder**는 source sentence의 벡터를 표현하고 (hidden layer 벡터 표현 집합으로 번역됨) **Decoder**는 인코더에 의한 결과인 hidden layer 벡터 표현을 사용해 번역 후 최종적으로 target을 얻는다. Bilingual parallel corpus를 고려했을 때, NMT는 **MLE**(maximum likelihood estimation)를 training criterion으로 사용하고, 번역 모델의 **NLL**(negative log-likelihood)을 최소화함으로써 optimal parameter θ를 얻는다.

NMT 훈련 과정에서는 vocabulary 중 타겟 단어에 해당되는 위치만 1로, 나머지는 0으로 표기된 원-핫 벡터를 target으로 사용한다. <U><em>-문제1-</em></U> 따라서, target 언어의 예측된 확률만 모델 훈련에 영향을 주며, 이는 **타겟 언어의 단어집합의 단어간 유사성을 무시한다**. (번역의 일반화와도 관련이 있는데, 기존 방법으로는 America와 USA가 동의어지만 완전 다르게 다뤄진다.)

<img src="/assets/images/teacher-free-2.PNG" title="teacher-free">

#### 2. Knowledge Distillation

전형적인 KD 접근법은 student를 학습시키기 위해 teacher 모델의 학습 결과인 label probabilities를 soft target으로 사용하는 것이다. Student prediction을 teacher의 prediction에 매칭시키며 학습이 이뤄지게 된다. Knowledge distillation은 pretrained 번역 모델로부터 **단어간 유사성을 얻기 위해**, 그리고 현재 모델의 훈련을 가이드하기 위해 자주 사용된다. 관찰된 데이터와의 cross entropy를 최소화하는 대신, **teacer의 probability distribution**과의 cross-entropy를 최소화한다(보통 teacher의 output distribution을 사용하지만, 이 논문에서는 virtual-teacher의 probability distribution을 사용한다는 점에서 차이가 있다.). 이렇게 teacher의 distribution으로 학습하는 것은 매우 매력적인데, 다른 클래스들 간 정보(예를 들면, 클래스들 간 유사성)를 더 많이 주고, 그라디언트의 분산이 더 적기 때문이다.

<U><em>-문제2-</em></U> 하지만, 이런 방식으로 얻어진 **정보는 해석불가능하며**(dark knowledge라고 불리는 것으로도 알 수 있음) 이런 정보를 얻기 위해서 **추가적인 번역 모델 훈련이 필요**하다. (teacher 모델을 따로 미리 훈련해야하기 때문인 듯)

<img src="/assets/images/teacher-free-3.PNG" title="teacher-free">

## Methods 
#### Teacher-Free Knowledge Distillation
<em>(1) Low-resource 언어쌍 번역에서는 높은 성능의 teacher모델을 얻는것이 쉽지 않고</em>, <em>(2) dark knowledge가 모델의 학습을 향상시켜준다는 점</em>에 착안해 이 연구에서는 teacher의 output distribution(기존 KD에서 타겟팅하던)을 간단한 것으로 대체했다. 이것이 Teacher-free Knowledge Distillation(Tf-KD)이며 여기서 사용되는 dark knowledge는 카테고리들 간 유사성을 포함할 뿐 아니라 student 훈련에서 regularization을 가능케 한다.

dark knowledge를 얻기 위해, 저자는 단어들 간 유사성에 따라 수동적으로 라벨을 디자인했다. 동시에, MLE에 기초해, Kullback-Leibler(KL) divergence를 추가해 모델의 손실함수를 확장했다. 추가 loss는 두 확률 분포 간 비교로 계산되는데, (1) 하나는 디테일한 모델 훈련 예측에서 비롯되고, (2) 다른 하나는 ground-truthb token-wise 분포에서 비롯되며 monolingual data dependent 사전 분포처럼 정의된다. 제안된 방법은 그 후, KL 수식으로 최종 loss에 반영된다.

대량의 monolingual data로부터 추출하기 전에, 단어 집합의 token(word/subword) 벡터 표현을 먼저 얻는다.
<img src="/assets/images/teacher-free-4.PNG" title="teacher-free">

emb(y)는 단어 y의 벡터 표현을 의미하며, 단어간 유사성은 단어 벡터 간 코사인 유사도로 표현된다. **단어집합의 target word와 유사한 단어는 evaluation function을 통해 별도의 weight를 할당**받는다. Prior probability가 training distribution에 따르도록 softmax를 사용했고, temperature control 기법을 써 전반적인 lable quality를 높였다. 아래q(y∗)는 각 단어의 prior distribution을 정의하는 수식이다.
<img src="/assets/images/teacher-free-5.PNG" title="teacher-free">

## Experiments
#### 1. Pre-Trained Embedding

#### 2. Low-Resource NMT

## Results
#### 1. Selection of Hyperparameter

#### 2. Selelction of similarity Evaluation Function

#### 3. Combined with the back-translation


## Conclusion
이 연구에서는 low-resource senario에서는 sequence level KD로 번역의 질을 향상 시킬 수 없다는 것을 발견했다. 이에 저자는 Teacher-free KD 프레임 워크를 제안하며, 이 방법은 단어 간 유사성으로 teacher model의 output을 모델링한다. 실험을 통해 이 방법을 증명했으며, powerful한 teacher 모델을 찾는 것이 힘들다면 수동-디자인된 regularization term으로도 성능을 강화할 수 있는 것을 제안한다. 
## Insights & Opinion
