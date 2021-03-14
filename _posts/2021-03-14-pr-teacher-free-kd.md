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



## Backgrounds

#### 1. Improving Transformers

#### 2. Model Scaling

#### 3. Improving Sequence Models

## Methods 

#### 1. Delight Transformation

<img src="/assets/images/delight_2.PNG" title="delight_2">


## Experiments & Results

## Conclusion

## Insights & Opinion
