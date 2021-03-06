---
layout: post
title: "머신러닝 개념에 발담구기1"
subtitle: 퍼셉트론, 아달린
date: 2020-06-17 1:32:28 -0400
cover-img: /assets/img/ml.jpeg
tags: [machine learning, perceptron, adaline]
use_math: true
categories: ML
---

# 퍼셉트론이란?

![image](https://user-images.githubusercontent.com/37768791/84805794-c0112a00-b03f-11ea-9198-3a13d9e8592e.png)

위 사진을 보면 y로 2개의 노드가 연결되어 있는 걸 볼 수 있습니다. 여기서 y노드를 퍼셉트론이라고 합니다. 결국 그림과 같이 다수의 신호를 받아서 하나의 신호로 출력하는 것입니다.

### 논리 게이트들

- AND 게이트
- NAND 게이트
- OR 게이트
- XOR 게이트

<br>

# 아달린이란?

ADALINE = `ADA`ptive `LI`near `NE`uron의 줄임말로 적응형 선형 뉴런을 의미합니다.

간단히 얘기하면 퍼셉트론의 향상된 버전입니다.

그럼 퍼셉트론과 어떤 차이점이 있을까요?

- 비용함수 = J(w)
- 선형 활성화 함수

<center>
<img src="https://user-images.githubusercontent.com/37768791/84806771-2cd8f400-b041-11ea-8443-7f90f1d247a9.png">
</center>

그림에서 볼 수 있듯이 시그마 아이콘이 있는 최종 입력 함수 다음에 선형 활성화 함수(activation function)가 추가된 것을 볼 수 있습니다.

그래서 아달린 알고리즘은 `진짜 클래스 레이블(관측값)`과 `선형 활성화 함수의 실수 출력 값`을 비교합니다.

<br>

### 경사하강법은 무엇인가?

<br>

왜 아달린에 `경사하강법`이 나왔을까요?

경사하강법은 비용함수(에러)를 `최소화`하기 위한 방법 중 하나입니다. 아까 설명했듯이 아달린에서 비용함수를 사용하기 때문이죠.

`[원리]`

SSE(잔차제곱합)으로 가중치 학습에 사용될 비용함수를 정의합니다.

비용함수 = sum(관측값 - 선형 활성화 함수 출력값) / 2

일단 비용함수는 구했는데 그럼 최소화는 어떻게 시킬까요?

<center>
<img src="https://user-images.githubusercontent.com/37768791/84807485-47f83380-b042-11ea-8d62-a904d60b36d8.png">
</center>

새로 가중치를 업데이트해서 점점 가중치를 줄이는 과정인데 그럼 가중치 업데이트는?

새로운 가중치 = -학습률 \* 비용함수

- 학습률은 어떻게 정해지는가?

  에포크 수를 조정해서 그래프를 그려가면서 최소화하는 구간으로 가는지 시각적으로 확인해가면서 정합니다.

<br>

### 특성 스케일 조정을 통한 경사하강법 성능 최적화

특성 스케일을 조정하면 경사하강법의 성능 최적화를 이룰 수 있습니다.

특성 스케일 조정 방법 중 `표준화`를 사용해보겠습니다.

`표준화 방법`

- 데이터에 표준 정규 분포 성질 부여(더 적은 단계를 거쳐 학습의 속도 높이고 최적의 솔루션을 찾을 수 있음)

<br>

### 확률적 경사하강법(stochastic gradient descent / SGD)

: 매우 큰 데이터셋인 경우 기존의 경사하강법은 계산 비용이 너무 많이 소모됩니다. 매번 `전체 훈련 데이터셋`에 누적된 오차의 합을 기반으로 가중치를 업데이트해야 하기 때문이죠. 그러나 확률적 경사하강법은 `각 훈련 샘플`에 대해서 조금씩 가중치를 업데이트합니다.

<center>
<img src="https://user-images.githubusercontent.com/37768791/84872836-5afc1980-b0bd-11ea-8d12-cd6280060f43.png">
</center>

`[특징]`

1. 샘플 순서 무작위
2. 새로운 훈련 데이터가 도차하는대로 훈련(무작위이기 때문)
3. 가중치를 다시 초기화하지 않음.
4. 스트리밍 데이터에 주로 사용

<br>

# 분류 모델

<br>

- 분류의 과정

```
샘플 모으기 -> 성능 지표 설정(컬럼 설정 등등) -> 분류 알고리즘 선택 -> 모델 성능 평가(잘 분류하는가) -> 알고리즘 튜닝(결과를 잘 산출하게)
```

<br>

### 나이브 베이즈(NB classification)

---

<br>

**조건부 확률**

- Pr(Y\|X) = Pr(X, Y) / Pr(X)
- Pr(X, Y) = Pr(Y\|X) \* Pr(X)

**조건부 독립**

- Pr(X1, X2 \| Y) = Pr(X1 \| Y) \* Pr(X2 \| Y)
- 확률간 곱셈이 성립하면 독립적인 관계

`[용도]`

Pr(Y\|X)만 아는 상태에서 Pr(X\|Y)를 알고자 하는 경우.

조건부 확률을 참고했을 때,  
Pr(Y\|X) = Pr(X, Y) / Pr(X) = Pr(X\|Y) \* Pr(Y) / Pr(X)

`[문제]`  
Y(HIV)감염 여부와 세 검사방법(X1, X2, X3)에 따른 양성 판정 여부(+, -) 자료이다. 어떤 사람에 대한 세 검사방법의 결과가 (+, +, +)와 같을 때, 이 사람이 HIV에 감염되었을 확률을 나이브 베이즈 분류 방법을 이용해서 구해보아라.

<center>
<img src="https://user-images.githubusercontent.com/37768791/84860021-3e55e680-b0a9-11ea-8fa7-4ad51b3276db.png">
</center>

`[풀이]`

- HIV : Y = 1
- Normal : Y = 0
- 양성 : x = 1
- 음성 : x = 0

**구하고자 하는 것**  
 => Pr(Y = 1 \| X1 = 1, X2 = 1, X3 = 1) / Pr(Y = 0 \| X1 = 1, X2 = 1, X3 = 1)

`나이브 베이즈 적용`

=> Pr(X1 = 1, X2 = 1, X3 = 1 \| Y=1) / Pr(X1 = 1, X2 = 1, X3 = 1 \| Y=1) \* (Pr(Y = 1) / Pr(Y = 0)) / (Pr(X1 = 1, X2 = 1, X3 = 1) /Pr(X1 = 1, X2 = 1, X3 = 1))

=> 로그 씌우기

=> log((3/3) / (2/4)) + log((1/3) / (1/4)) + log((2/3) / (1/4)) \* log((3/7) / (4/7))

=> 1.674

<br>

### 소프트맥스(다중분류)

---

이전까지 분류작업들은 모두 이진분류, 즉 0이나 1 혹은 1이나 -1로 두가지의 레이블만 분류하는 문제였다면 소프트맥스 3개 이상의 다양한 클래스를 분류할 수 있는 모델입니다.

<center>
<img src="https://user-images.githubusercontent.com/37768791/84868910-54b76e80-b0b8-11ea-9958-595d1b5a0be1.png">
</center>

위 그림을 보시면 y로 출력된 값이 있는데 여기서 시그모이드 함수를 돌려서 특정 클래스로 분류될 `확률`을 뽑아냅니다. 그렇게 뽑아낸 확률 중 `가장 큰 확률의 클래스`로 결국 분류되는 것이죠.

<br>

### k-nn 분류

---

x라는 점이 주어지면 x점과 거리가 가장 가까운 `k개의 훈련자료점`의 y값을 비교하여 가장 많이 예측된 클래스를 출력

<center>
<img src="https://user-images.githubusercontent.com/37768791/84870262-0dca7880-b0ba-11ea-996e-a0e2c27a2090.png">
</center>

k는 결국 붉은 동그라미와 같이 특정 거리 범위 내에 있는 점의 개수를 의미합니다.

<center>
<img src="https://user-images.githubusercontent.com/37768791/84870689-947f5580-b0ba-11ea-9e59-519914736248.png">
</center>

위 이미지는 k의 개수에 따른 분류 경계를 그리고 있습니다. k의 개수를 높이면 높일 수록 `복잡도`는 낮아집니다. 범위 안에 많은 점이 들어가게 된다면 분류 경계가 한정적으로 그어질 수 밖에 없기 떄문입니다.

`[문제]`

<center>
<img src="https://user-images.githubusercontent.com/37768791/84871053-0c4d8000-b0bb-11ea-9373-49dc5fda6ab1.png">
</center>

위에서 언급했듯이 복잡하게 만들기 위해서는 k를 감소시켜야 합니다.

답 : (나)

<br>

### 로지스틱 뉴런(Logistic Neuron)

---

- 스코어함수 : f(x) = beta1 \* X1 + beta2 \* X2 + beta3 \* X3  
  Pr(Y = 1 \| X) = 1 / (1 + exp(-f(x))) = sigmoid(f(x))

`[문제]`

<center>
<img src="https://user-images.githubusercontent.com/37768791/84874691-f3939900-b0bf-11ea-87d0-8a21fa6a4fe1.png">
</center>

1. f(x)는 스코어함수이기에 확률값을 나타내므로 `True`
2. 분수를 좀 바꾸면 1 - 1 / (1 + exp(f(x)))이고 1 / 1 + (exp(-f(x)))와 동일하므로 `True`
3. 시그모이드 함수이므로 `True`
4. x=0일 때 y = 1일 확률과 x =1일 때 y = 0일 확률을 합하면 1인가? `False`
5. `True`
6. <span style="color: red">**가장 중요**</span> 스코어함수 : Y = beta0 + X, Pr(beta0 =1) / Pr(beta0 = 0) > Pr(beta0 =1) / Pr(beta = 0)이지 않고 둘 다 같습니다. 결국 답은 `False`

<br>

### 분류 모델 성능 평가

---

#### ROC 그래프

<center>
<img src="https://user-images.githubusercontent.com/37768791/84975040-715ab180-b15f-11ea-99e2-0f2a3fe7190a.png">
</center>

> 그래프가 왼쪽 모서리에 가까워질수록 모델의 성능이 좋습니다.

#### 성능지표

<center>
<img src="https://user-images.githubusercontent.com/37768791/84972435-d14e5980-b159-11ea-9dfd-76403ed67674.png">
</center>

- `민감도(재현율)(Sensitivity)`
  a / a+b : 실제 Yes인 것 중에 예측과 실제 모두 Yes인 것
  => Pr(y_hat = 1 \| y = 1)
- `특이도(Specificity)`
  d / c+d : 실제 No인 것 중에 예측과 실제 모두 No인 것
  => Pr(y_hat = 0 \| y = 0)
- `정밀도(Precision)`
  a / a+c : 예측이 Yes인 것 중에 실제와 예측 모두 Yes인 것
  => Pr(y = 1 | y_hat = 1)

`[문제]`

<center>
<img src="https://user-images.githubusercontent.com/37768791/84972828-a87a9400-b15a-11ea-80da-da718f119e44.png">
</center>

- 구하고자 하는 것 : Pr(Y = 1 \| Y_hat = 1) = a / a+c (정밀도)
- Pr(Y = 1) = 0.001 = a + b
- Sensitivity = 0.997 = a / a+b = Pr(Y_hat = 1 \| Y = 1)
- Specificity = 0.985 = d / c+d = Pr(Y_hat = 0 \| Y = 0)

  <나이브베이즈 적용>

  Pr(Y_hat = 1 \| Y = 1) = Pr(Y = 1 \| Y_hat = 1) \* Pr(Y_hat = 1) / Pr(Y = 1)

  Pr(Y = 1 \| Y_hat = 1) = 0.997 \* 0.001 / Pr(Y_hat = 1)

<br>

### 로지스틱 회귀(Logistic Regression) <span style="color:red">**중요**</span>

---

<center>
<img src="https://user-images.githubusercontent.com/37768791/84855453-1ceffd00-b09f-11ea-8cda-e80cc4be503a.png">
</center>

퍼셉트론의 한계

- 클래스를 그렸을 때 `선형적으로 구분되지 않을 때` 수렴 불가능

위 문제점을 해결할 수 있는 것이 바로 `로지스틱 회귀`입니다.

로지스틱 회귀도 2개의 클래스를 분류하기 위한 선형모델이지만 다중 분류도 확장할 수 있기 때문에 퍼셉트론의 한계를 극복할 수 있습니다.

`[특징]`

- **오즈비**(특정 이벤트가 발생할 확률) -> P / (1-P) (P는 예측하려는 대상일 확률)
- **로짓 함수**(오즈비에 자연로그 씌움) -> logit(P) = log(P / (1-P))
<center>
<img src="https://user-images.githubusercontent.com/37768791/84915275-8c91d680-b0f7-11ea-96da-c00a2f8704f6.png">
</center>
- **로지스틱 시그모이드 함수**(로지스틱 회귀에서의 활성화 함수)
- 시그모이드 함수를 돌리기 때문이기도 하고 확률로 표현해야하므로 값이 0과 1사이의 값이 나옴.

`[가중치 학습 방법]`

비용함수 = sum(시그모이드 활성화 함수 출력 레이블 - 관측 레이블)^2 / 2

<center>
<img src="https://user-images.githubusercontent.com/37768791/84857180-69d5d280-b0a3-11ea-873c-1ac14b5b58e7.png">
</center>

h(x) : 시그모이드 활성화 함수에 돌렸을 때 산출된 값입니다.  
cost : 비용함수

`[비용함수 그래프 설명]`

- y = 0  
  빨간 그래프를 보면 y를 0으로 예측했을 때의 비용함수 그래프입니다. h(x)값이 0으로 예측했을 때 비용함수가 0에 수렴하는 것을 볼 수 있습니다. 잘 예측했다는 것이죠. 그러나 h(x)가 1로 향해 갈수록 비용함수가 가파르게 상승하는 것을 볼 수 있습니다. 이렇게 비용함수의 상황을 통해 분류하는 것이 로지스틱 회귀의 방법입니다.

<br>

#### 규제를 통해 과대적합 피하기

---

`규제`는 뭐고 `과대적합`은 무엇일까요?

- 과대적합(overfitting) : 너무 핏하게 짜여진 것을 의미하고, 분산(예측의 일관성, 민감성)이 크다고 합니다. 핏하면 좋은 것이 아닌가 하겠지만 새로운 데이터로 예측을 했을 때 잘 안맞을 확률이 있습니다.

- 과소적합(underfitting) : 모델이 충분히 복잡하지 않는 것입니다. 너무 느슨하게 짜여진 것을 의미합니다. 편향(예측에서 벗어난 정도)이 크다고도 합니다.

 <center>
 <img src="https://user-images.githubusercontent.com/37768791/84857899-08aefe80-b0a5-11ea-9cae-98bbc2931130.png">
 </center>
<center>결국 모델도 그 중간점을 잘 찾아야합니다.</center>

그러기 위해선 `규제` 가 필요합니다

추가적인 정보(편향)를 주입해서 과대적합을 방지하는 것이 `규제`입니다.

`[기존 모델에 규제 추가]`

- 규제 항인 람다 변수를 추가해줍니다.
- 람다가 증가하면 규제 강도가 높아져서 과대적합을 줄여줍니다.
- 매개변수 C : 역규제 파라미터(규제와 역수 관계)

<center>
<img src="https://user-images.githubusercontent.com/37768791/84858709-a48d3a00-b0a6-11ea-8307-782dc873f1b7.png">
</center>

위 그래프를 보면 x축에 역규제 파라미터가 있고 y축에는 가중치의 분산(민감성)이 있습니다.

좌측으로 갈수록 규제가 증가하고 우측으로 갈수록 규제가 감소합니다. 규제가 강할수록 민감도는 0에 수렴하는 것을 볼 수 있습니다.
