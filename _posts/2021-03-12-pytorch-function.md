---
layout: post
title:  "[PyTorch] 알아두면 유용한 함수 모음(딥러닝/머신러닝을 위한)"
date:   2021-03-05T14:25:52-05:00
author: Heejin Do
categories: "pytorch"
tags:	pytorch function deep_learning 
---

## 1. 같은지(torch.eq) 다른지(torch.ne) element-wise로 비교
### 1) torch.ne
각 텐서의 요소(element)들을 비교해 다르면 True를, 같으면 False를 반환한다.

- 형태 : torch.ne(`비교할 tensor`, `비교할 tensor나 value`, *, out=None) → Tensor

```
EX) 
>>> torch.ne(torch.tensor([[2, 5], [4, 3]]), torch.tensor([[2, 8], [2, 3]]))
tensor([[False, True], [True, False]])
```

{% highlight python %}
# tensor 정의
A = np.array([
        [   14,    11,     7,     6],
        [   11,    14,     6,    22],
        [ 2435,    20,    32, 13894],
        [ 1352,     9,  2076,   111],
        [    5,    10,     4,    67],
        [    2,    18,    16,   221]
        ])
A = torch.LongTensor(A)

B = np.array([
        [   7],
        [  11],
        [2435],
        [ 165],
        [   4],
        [   2]
        ])
B = torch.LongTensor(B)

not_include = A.ne(B)

print(not_include)
> 결과 : tensor([[ True,  True, False,  True],
                 [False,  True,  True,  True],
                 [False,  True,  True,  True],
                 [ True,  True,  True,  True],
                 [ True,  True, False,  True],
                 [False,  True,  True,  True]])

{% endhighlight %}

#### torch.eq

#### torch.where

#### torch.nonzero

#### torch.ones & torch.ones_like