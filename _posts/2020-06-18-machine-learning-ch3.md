---
layout: post
title: "머신러닝 개념에 발담구기"
subtitle: 3차 정리
date: 2020-06-18 2:40:28 -0400
cover-img: /assets/img/ml.jpeg
tags: [machine learning, clustering, kmeans, association rule]
use_math: true
---

# 연관규칙(Association Rule)

: 데이터에 있는 항목들 간에 if-then 형식의 연관성을 찾는 것입니다. 장바구니 분석이라고도 불립니다. 예시로는 "Google의 추천 검색어", "쿠팡의 상품 추천" 등등이 있습니다.

- **지지도**
<center>
<img src="https://user-images.githubusercontent.com/37768791/84986341-61e86200-b179-11ea-8467-b39149be844e.png">
</center>

- **신뢰도**
<center>
<img src="https://user-images.githubusercontent.com/37768791/84986426-82182100-b179-11ea-9237-695b8a72f1f5.png">
</center>

- **향상도**(포함률)
<center>
<img src="https://user-images.githubusercontent.com/37768791/84986504-a1af4980-b179-11ea-8be6-96ec57f775d0.png">
</center>

위 공식을 기반으로 문제를 풀어봅시다.
<br>

`[문제]`

<center>
<img src="https://user-images.githubusercontent.com/37768791/84986746-07033a80-b17a-11ea-8f11-ec2ea35f77b6.png" >
<img src="https://user-images.githubusercontent.com/37768791/84989445-a296aa00-b17e-11ea-915d-37e7db8fc46a.png" >
<img src="https://user-images.githubusercontent.com/37768791/84986666-e4712180-b179-11ea-8bb4-ac2e8b0976ca.png" >
</center>

> 상단 이미지는 거래 데이터입니다. 상단 우측 이미지는 상대도수입니다. 하단이미지는 연관규칙입니다. 이를 기반으로 지지도, 신뢰도, 향상도를 계산하면 됩니다.

| 연관규칙 (X => Y) | 지지도 Pr(X, Y 교집합) | 향상도 Pr(X)           | 신뢰도 Pr(Y \| X)     |
| :---------------- | :--------------------- | :--------------------- | :-------------------- |
| A => B            | 500 / 2000 = **0.25**  | 900 / 2000 = **0.45**  | 500 / 900 = **0.556** |
| B => A            | 500 / 2000 = **0.25**  | 850 / 2000 = **0.425** | 500 / 850 = **0.588** |
| C => B            | 300 / 2000 = **0.15**  | 800 / 2000 = **0.4**   | 300 / 800 = **0.375** |
| B => C            | 300 / 2000 = **0.15**  | 850 / 2000 = **0.425** | 300 / 850 = **0.353** |
| A => C            | 400 / 2000 = **0.2**   | 800 / 2000 = **0.45**  | 400 / 900 = **0.444** |
| C => A            | 400 / 2000 = **0.2**   | 800 / 2000 = **0.4**   | 400 / 800 = **0.5**   |
| {A, B} => B       | 100 / 2000 = **0.05**  | 500 / 2000 = **0.25**  | 100 / 500 = **0.2**   |
| {B, C} => A       | 100 / 2000 = **0.05**  | 300 / 2000 = **0.15**  | 100 / 300 = **0.333** |
| {A, C} => B       | 100 / 2000 = **0.05**  | 400 / 2000 = **0.2**   | 100 / 400 = **0.25**  |

<br>

# 군집분석(Clustering Analysis)

: 데이터 중에 유사한 것들을 그룹화(cluster)하고, 그 그룹의 성격을 파악하는 것을 의미합니다.

`[군집 유형]`

- 상호배반적 군집 : 서로 교집합 없음
- 계보적 군집 : 교집합이 부분 교집합이라기보다 A안에 B가 속해있는 구조.
- 중복 군집 : 교집합이 있는 군집
- 퍼지 군집 : 벤다이어그램보다는 속할 확률을 표현한 군집

<br>

### K-means Clustering

---

: 서로 교집합이 없는 상호배반적인 `K`개의 군집을 형성하는 군집화하는 방법 중 하나

`[절차]`

<center>
<img src="https://user-images.githubusercontent.com/37768791/84983664-2eef9f80-b174-11ea-8499-2b92ccf30bca.png">
</center>

1. Elbow Method를 통해 K를 설정(몇 개의 군집으로 나눌지)
2. 데이터를 군집에 할당(군집의 중심점을 계산)
3. 데이터를 군집에 할당(군집의 중심점을 반복 계산)

<br>

`[문제]`

<center>
<img src="https://user-images.githubusercontent.com/37768791/84984011-f13f4680-b174-11ea-8212-8e74a5efeda8.png">
</center>

- 1번 풀이  
   위 설명에 따라 `데이터 할당` & `중심 계산`

  답 : (c), (d)

- 2번 풀이  
   가까운 군집인 (-3.0)이 있는 2번 군집에 할당되고 중심점이 다시 조정되지 않을까 한다.
  답 : (b)
