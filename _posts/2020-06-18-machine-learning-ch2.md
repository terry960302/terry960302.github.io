---
layout: post
title: "머신러닝 개념에 발담구기"
subtitle: 2차 정리
date: 2020-06-18 12:32:28 -0400
cover-img: /assets/img/ml.jpeg
tags: [machine_learning, decision tree, ensemble]
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

1. 카이제곱 통계량
<center>
<img src="https://user-images.githubusercontent.com/37768791/84977177-b0d7cc80-b164-11ea-8b75-a0dbea0a0721.png">
</center>

2. 지니지수
<center>
<img src="https://user-images.githubusercontent.com/37768791/84977247-d82e9980-b164-11ea-80fd-b67c15487d1e.png">
</center>

3. 엔트로피 지수
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

=> 기저모형들을 순차적으로 만든 후, 최종적으로 합쳐서 의사결정하는 구조입니다.

### AdaBoost(에이다부스트)

<center>
<img src="https://user-images.githubusercontent.com/37768791/84978707-999ade00-b168-11ea-9101-65ee7abf8780.png">
</center>

>
