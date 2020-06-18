---
layout: post
title: "머신러닝 개념에 발담구기2"
subtitle: 의사결정나무, 앙상블
date: 2020-06-18 12:32:28 -0400
cover-img: /assets/img/ml.jpeg
tags: [machine learning, decision tree, ensemble]
use_math: true
---

# 의사결정나무(Decision Tree)

<center>
<img src="https://user-images.githubusercontent.com/37768791/84975413-43c23800-b160-11ea-92e0-d2e9ff03d5be.png">
</center>

`[원리]`

적절한 분리기준과 정지규칙을 통해 가지를 만들어 나가는 분류 모델

- if-then 형식
- 비모수적 방법(선형성, 등분산성 확인 불필요)
- 연속형 변수, 범주형 변수 모두 취급 가능

아래 예시는 초록점만 분리하기 위한 의사결정트리를 만들어나가는 과정입니다.

<center>
<img src="https://user-images.githubusercontent.com/37768791/84975965-b253c580-b161-11ea-8032-370a5d0ecd7a.png">
</center>

1.  X1 > 0.47 이라는 분리 규칙을 기반으로 선을 긋고 2가지 영역으로 분리해줍니다. 그럼 좌측 노드에는 노란색만 있고, 우측 노드에 초록색이 모이게 만듭니다.

 <center>
 <img src="https://user-images.githubusercontent.com/37768791/84976105-02328c80-b162-11ea-93db-d34ea85467b7.png">
 </center>

2. 위에서 분리를 하고도 우측 노드에선 더 나눌 필요가 있어서 X2 < 0.39 라는 기준을 만들어서 더 나눠줍니다. 좌측 노드에 약간 섞이긴 했지만 우측 노드에 거의 초록점이 모여있네요.

<center>
<img src="https://user-images.githubusercontent.com/37768791/84976287-766d3000-b162-11ea-931e-d615ba7bffd1.png">
</center>

3. 두 번의 분류 기준을 더 접목해주니 이제 한 구역에 60% 이상이 초록점이 들어가게 되게 나눴습니다.

`[분류 기준 선택 지표(불순도)]`

> 불순도이기 때문에 값이 적을수록 선택하기 좋은 기준입니다.

<center>
<img src="https://user-images.githubusercontent.com/37768791/84977113-8554e200-b164-11ea-9572-564b88488d9d.png">
</center>

<br>

- 카이제곱 통계량
<center>
<img src="https://user-images.githubusercontent.com/37768791/84977177-b0d7cc80-b164-11ea-8b75-a0dbea0a0721.png">
</center>

<br>

- 지니지수
<center>
<img src="https://user-images.githubusercontent.com/37768791/84977247-d82e9980-b164-11ea-80fd-b67c15487d1e.png">
</center>

<br>

- 엔트로피 지수
<center>
<img src="https://user-images.githubusercontent.com/37768791/84977300-f4cad180-b164-11ea-9cf4-532d4438c28f.png">
</center>

<br>

# 앙상블(Ensemble)

말그대로 `모여서 협업` 한다는 의미를 내포하고 있습니다. 어떻게 협업을 한다는 것일까요?

간단한 모형을 결합하므로써 분류 정확도를 높은 복잡한 모형을 만들어 나가는 것을 의미합니다.

<center>
<img src="https://user-images.githubusercontent.com/37768791/84977931-9dc5fc00-b166-11ea-9313-8db4212ccbc1.png">
</center>

> `파란선`이 간단한 모형이고 다양하게 만들어진 간단한 모형들(샘플링을 통해 특정 개수만큼 생성)을 나중에 결합하여 `붉은선`의 복잡한 모형을 만들어냅니다. 이 모형으로 인해 분류 정확도가 높게 예측할 수 있습니다.

<br>

### 배깅 알고리즘(Bagging)

---

Bagging : `B`ootstrap `Agg`reat`ing` 의 줄임말

=> 여러 개의 부트스트랩 자료(같은 사이즈의 표본을 랜덤 중복 추출한 데이터)를 만들고, 각 부트스트랩 자료에 맞는 예측 모형을 만들고, 나중에 다 합해서 최종 예측 모형 만들기

위의 말을 도식화하면 아래와 같습니다.

<center>
<img src="https://user-images.githubusercontent.com/37768791/84978299-7f143500-b167-11ea-8ee7-80facf2dcc37.png">
</center>

- **Training Set** : 총 데이터
- **S1, S2, S3** : 부트스트랩 자료(랜덤 중복 허용 추출 데이터)
- **Learning algorithm** : 일반 회귀식, 로지스틱 회귀, 의사결정 나무 등등(학습 모델이면 아무거나 상관없음)
- **Model M1, M2, M3** : 학습 모델로 나온 산출값들
- **Consensus Model** : 분류문제일 경우엔 `투표(Voting)`을 사용해서 예측 모형을 구함. 회귀문제일 경우엔 `평균(Average)구하기`를 통해 예측 모형을 구함.

<br>

### 부스팅 알고리즘(Boosting)

---

=> 기저모형들을 순차적으로 만든 후, 최종적으로 합쳐서 의사결정하는 구조입니다.  
=> 순차적으로 기저모형들을 만들 때 각 기저모형들은 이전 단계의 기저모형에 의존합니다.  
=> 에러값이 있는 오분류된 개체에 대해 더 가중치를 두고 기저모형이 만들어집니다.

<center>
<img src="https://user-images.githubusercontent.com/37768791/84980051-ff3c9980-b16b-11ea-9ee4-02c86b2a9969.png">
</center>

> 열만 봤을 때 3가지의 이미지가 있습니다. 좌측부터 1, 2, 3이미지라고 칭하겠습니다. 1번 기저모형은 그냥 전체 훈련 데이터입니다. 예측 모형을 거치면서 오분류된 데이터를 감지합니다. 2번 이미지도 이동하면서 오분류된 데이터에 대해 가중치를 크게 부여합니다.(기호들이 커진 것을 볼 수 있을 겁니다.) 3번 이미지에서는 다시 오분류된 것을 파악해서 가중치를 재조정한 결과물입니다.

<center>
<img src="https://user-images.githubusercontent.com/37768791/84980092-1b403b00-b16c-11ea-995b-3c25a8748c02.png">
</center>

> 이전 이미지에서 봤듯이 그렇게 만들어진 모형들에서 오분류된 데이터의 확률을 1에서 빼준 후 모두 더해줍니다.

### AdaBoost(에이다부스트)

: 훈련 오차를 효과적으로 줄일 수 있는 방법입니다.

<center>
<img src="https://user-images.githubusercontent.com/37768791/84979121-a1a74d80-b169-11ea-8eb9-ef10623549bc.png">
</center>

> 대략적인 구조는 배깅 알고리즘과 비슷해보일 수 있지만 S1으로 표본을 추출하는 과정과 Consensus Model 부분을 주목해야합니다.

- **Training Set** -> S1 : 전체 훈련 데이터를 그대로 가져옵니다.
- **S1 -> S2** : S1에선 가중치가 모두 같습니다. 에러값(e)를 반영해서 S2로 전달합니다.
- **S2 -> S3** : 에러값을 반영해서 가중치를 재조정해서 다시 에러를 산출해서 S3로 넘깁니다. 결국 받은 에러로 다시 가중치를 업데이트합니다.(이미지에서 보시면 S3에서 마지막 가중치(w)가 엄청 커진 것을 확인할 수 있습니다.)

<br>

<center>
<img src="https://user-images.githubusercontent.com/37768791/84981264-15982480-b16f-11ea-8a41-6fd847a3b25a.png">
</center>

`[문제]`

<center>
<img src="https://user-images.githubusercontent.com/37768791/84981312-352f4d00-b16f-11ea-8e76-de4d4689039d.png">
</center>

<br>

```python
## 패키지 설치
import numpy as np
import math
```

<br>

```python
## 사용할 함수

# 1단계 값을 예시로 함수작성

y = [1, 1, 1, -1 , 1, -1, -1, 1, -1, -1] # +와 -를 구분하는 레이블
d1 = [0.1, 0.1, 0.1, 0.1, 0.1,0.1,0.1,0.1,0.1,0.1,] # 기존 가중치
h1 = [-1, -1, -1, -1, 1, -1,-1,  1, -1, -1] # 인디케이터 함수 돌린 값들


# 오분류난 인덱스 함수

def getMisClassified(y, h):
    result = []
    for i in range(0, len(y)):
        if y[i] != h[i]:
            result.append(i)
        else:
            continue
    return result


# 에러 함수

def getErr(y, d, h):
    result = 0
    for i in range(0, len(y)):
        if y[i] != h[i]:
            result += d[i] * 1
    return result


# 알파값 함수

def getAlpha(err):
    return 1/2 * np.log((1- err) / err)


# 익스포넨셜 반환값 함수

def getExp(y, d, h):
    result = []

    for i in range(0, len(y)):
        y_i = y[i]
        h_i = h[i]
        alpha = getAlpha(getErr(y, d, h))

        value = np.exp(-alpha * y_i * h_i)

        result.append(value)
    return result


# 가중치와 지수함수 반환값의 곱 함수

def get_d_x_exp(d, exp):
    return [i*j for i, j in zip(d, exp)]


# z값 함수

def getZ(d_x_exp):
    return sum(d_x_exp)


# 가중치 업데이트 함수

def getUpdatedWeight(d_x_exp, z):
    return [i / z for i in d_x_exp]
```

<br>

```python
## 1단계 수치계산
# 1단계
y = [1, 1, 1, -1 , 1, -1, -1, 1, -1, -1] # +와 -를 구분하는 레이블
d1 = [0.1, 0.1, 0.1, 0.1, 0.1,0.1,0.1,0.1,0.1,0.1]
h1 = [-1, -1, -1, -1, 1, -1,-1,  1, -1, -1] # 인디케이터 함수 돌린 값들

err1 = getErr(y, d1, h1)
alpha1 = getAlpha(err1)
exp1 = getExp(y, d1, h1)
d_x_exp1 = get_d_x_exp(d1, exp1)
z1 = getZ(d_x_exp1)

print("가중치 \n=> {0}\n".format([round(i, 2) for i in d1]))
print("오분류 인덱스 \n=> {0}\n".format(getMisClassified(y, h1)))
print("e^-a1*yi*h1(xi) \n=> {0}\n".format([round(i, 2) for i in exp1]))
print("d * e^-a1*yi*h1(xi) \n=> {0}\n".format([round(i, 2) for i in d_x_exp1]))
print("err \n=> {:.2}\n".format(err1))
print("alpha \n=> {:.2}\n".format(alpha1))
print("z \n=> {:.2}\n".format(z1))

'''
가중치
=> [0.1, 0.1, 0.1, 0.1, 0.1, 0.1, 0.1, 0.1, 0.1, 0.1]

오분류 인덱스
=> [0, 1, 2]

e^-a1*yi*h1(xi)
=> [1.53, 1.53, 1.53, 0.65, 0.65, 0.65, 0.65, 0.65, 0.65, 0.65]

d * e^-a1*yi*h1(xi)
=> [0.15, 0.15, 0.15, 0.07, 0.07, 0.07, 0.07, 0.07, 0.07, 0.07]

err
=> 0.3

alpha
=> 0.42

z
=> 0.92

'''
```

<br>

```python
## 2단계 수치계산
# 2단계
d2 = getUpdatedWeight(d_x_exp1, z1)
h2 = [1, 1, 1, -1, 1, 1, 1, 1, 1, -1] # 인디케이터 함수를 그림으로 통해 유추하여 반환한 값들

err2 = getErr(y, d2, h2)
alpha2 = getAlpha(err2)
exp2 = getExp(y, d2, h2)
d_x_exp2= get_d_x_exp(d2, exp2)
z2 = getZ(d_x_exp2)

print("가중치 \n=> {0}\n".format([round(i, 2) for i in d2]))
print("오분류 인덱스 \n=> {0}\n".format(getMisClassified(y, h2)))
print("e^-a2*yi*h2(xi) \n=> {0}\n".format([round(i, 2) for i in exp2]))
print("d * e^-a2*yi*h2(xi) \n=> {0}\n".format([round(i, 2) for i in d_x_exp2]))
print("err \n=> {:.2}\n".format(err2))
print("alpha \n=> {:.2}\n".format(alpha2))
print("z \n=> {:.2}\n".format(z2))

'''
가중치
=> [0.17, 0.17, 0.17, 0.07, 0.07, 0.07, 0.07, 0.07, 0.07, 0.07]

오분류 인덱스
=> [5, 6, 8]

e^-a2*yi*h2(xi)
=> [0.52, 0.52, 0.52, 0.52, 0.52, 1.91, 1.91, 0.52, 1.91, 0.52]

d * e^-a2*yi*h2(xi)
=> [0.09, 0.09, 0.09, 0.04, 0.04, 0.14, 0.14, 0.04, 0.14, 0.04]

err
=> 0.21

alpha
=> 0.65

z
=> 0.82
'''
```

<br>

```python
## 3단계 수치계산

# 3단계

d3 = getUpdatedWeight(d_x_exp2, z2)
h3 = [1, 1, 1, 1, -1, -1, -1, -1, -1, -1] # 인디케이터 함수를 그림으로 통해 유추하여 반환한 값들

err3 = getErr(y, d3, h3)
alpha3 = getAlpha(err3)
exp3 = getExp(y, d3, h3)
d_x_exp3= get_d_x_exp(d3, exp3)
z3 = getZ(d_x_exp3)

print("가중치 \n=> {0}\n".format([round(i, 2) for i in d3]))
print("오분류 인덱스 \n=> {0}\n".format(getMisClassified(y, h3)))
print("e^-a3*yi*h3(xi) \n=> {0}\n".format([round(i, 2) for i in exp3]))
print("d * e^-a3*yi*h3(xi) \n=> {0}\n".format([round(i, 2) for i in d_x_exp3]))
print("err \n=> {:.2}\n".format(err3))
print("alpha \n=> {:.2}\n".format(alpha3))
print("z \n=> {:.2}\n".format(z3))

'''
가중치
=> [0.11, 0.11, 0.11, 0.05, 0.05, 0.17, 0.17, 0.05, 0.17, 0.05]

오분류 인덱스
=> [3, 4, 7]

e^-a3*yi*h3(xi)
=> [0.4, 0.4, 0.4, 2.52, 2.52, 0.4, 0.4, 2.52, 0.4, 0.4]

d * e^-a3*yi*h3(xi)
=> [0.04, 0.04, 0.04, 0.11, 0.11, 0.07, 0.07, 0.11, 0.07, 0.02]

err
=> 0.14

alpha
=> 0.92

z
=> 0.69
'''
```

<br>

```python
## 우측 상단 영역 예측
sign = lambda x: math.copysign(1, x) # 람다 함수는 다른 언어의 map과 거의 비슷(math 모듈의 copysign 함수로 sign함수를 구현하기 위함.)

# 우측 상단 영역은 1단계 범위에 안들어가서 -1, 2단계 범위에도 안들어가서 -1, 3단계 범위엔 들어가서 1입니다.
value = (-1) * alpha1 + (-1) * alpha2 + 1 * alpha3 # 위에서 구한 알파값으로 계산을 합니다.
result = sign(value) # sign 함수에 넣어 양수 영역인지 음수 영역인지 반환합니다.

print("우측 상단 영역을 예측해봅시다.")

if result >= 0:
    print("=> 양수 영역에 해당합니다.")
else:
    print("=> 음수 영역에 해당합니다.")

'''
우측 상단 영역을 예측해봅시다.
=> 음수 영역에 해당합니다.
'''
```

<br>

```python
## 해석

'''
에이다 부스트(adaboost)는 학습을 할 때 전체를 대상으로 훈련을 진행하고 오분류된 데이터에는 높은 가중치를, 제대로 분류된
데이터들에 대해서는 낮은 가중치를 부여합니다. 그래서 높은 가중치에 대해서 집중적으로 학습을 하는 단계를 거칩니다.
그리고 이렇게 단계를 거치면서 각 단계별로 서로 다른 가중치를 부여하게 되었는데, 다수결 투표 방식으로 학습기를 합칩니다.
이렇게 합친 학습기를 바탕으로 위에서 언급했던 우측 상단 영역의 예측을 할 수 있습니다.
'''
```

<br>

### 랜덤 포레스트(Radom Forest, rf)

: 트리를 구성할 때, 각 노드에서 일부의 변수들을 랜덤하게 지정합니다.(데이터에서 컬럼을 임의로 지정해서 추출합니다.)

- 정확도 높음
- 빅데이터에 적합
- 변수의 중요도에 대한 추정치를 제공

<center>
<img src="https://user-images.githubusercontent.com/37768791/84980946-5e031280-b16e-11ea-9382-7eb21aa153f9.png">
</center>
