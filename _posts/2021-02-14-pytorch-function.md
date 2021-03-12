---
layout: post
title:  "[PyTorch] 알아두면 유용한 함수 모음 - 비교(comparison) 함수"
date:   2021-02-13T14:25:52-05:00
author: Heejin Do
categories: "pytorch"
tags:	pytorch function deep_learning 
---

## 비교(comparison)를 위한 함수
element-wise로 비교하는 함수로는 torch.eq(같은지) torch.ne(다른지) torch.ge(크거나 같은지) torch.le(작거나 같은지)등이 있고, 텐서 차원에서 비교하는 함수로는 torch.equal, torch.not_equl 등이 있다.

### [Element-Wise Comparison]
### 1) torch.ne
각 텐서의 요소(element)들을 element-wise로 각각 비교해 다르면 True를, 같으면 False를 반환한다.

- 형태 : torch.ne(`비교 대상 tensor`, `비교할 tensor나 value`, *, out=None) → Tensor
- `torch.not_equal`과 동일

```
EX) 
>>> torch.ne(torch.tensor([[2, 5], [4, 3]]), torch.tensor([[2, 8], [2, 3]]))
# 결과 : tensor([[False, True], [True, False]])
```
위처럼 shape가 동일한 텐서끼리는 같은 위치에 있는 값들을 비교하고, 비교할 tensor로 torch.Size([n, 1]) shape의 텐서가 주어지면 아래처럼 한 줄씩 값을 비교한다.
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

### 2) torch.eq
ne와 반대로 각 텐서의 요소(element)들을 비교해 같으면 True를, 다르면 False를 반환한다.
- 형태 : torch.eq(`비교 대상 tensor`, `비교할 tensor나 value`, *, out=None) → Tensor

```
EX) 
>>> torch.eq(torch.tensor([[2, 5], [4, 3]]), torch.tensor([[2, 8], [2, 3]]))
# 결과 : tensor([[True, False], [False, True]])
```
ne처럼 같은 위치에 있는 값들을 비교하지만 True, False가 반대로 나타난다.

### 3) torch.ge
마찬가지로 element-wise로 값을 비교해 크거나 같으면 True를, 작으면 False를 반환한다.
: (`torch.gt`는 크면 True, 작거나 같으면 False를 반환)
- `torch.greater_equal`과 동일
```
>>> torch.ge(torch.tensor([[2, 5], [4, 3]]), torch.tensor([[2, 8], [2, 3]]))
# 결과 : tensor([[True, False], [True, True]])
```

### 4) torch.le
마찬가지로 element-wise로 값을 비교해 작거나 같으면 True를, 크면 False를 반환한다.
: (`torch.lt`는 작으면 True, 크거나 같으면 False를 반환)
- `torch.less_equal`과 동일
```
>>> torch.ge(torch.tensor([[2, 5], [4, 3]]), torch.tensor([[2, 8], [2, 3]]))
# 결과 : tensor([[True, True], [False, True]])
```

### [Tensor-Wise Comparison]
element-wise의 비교가 아닌, 텐서 차원에서 비교가 이뤄진다.
### 1) torch.equal
두 텐서의 사이즈가 같으면 True를 반환, 다르면 False를 반환한다.
```
>>> torch.equal(torch.tensor([2, 4]), torch.tensor([2, 4]))
# 결과 : True
```
