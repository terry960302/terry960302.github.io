---
layout: post
title: "회귀분석 알아보기 - 과제분석"
subtitle: 1차 과제
date: 2020-06-15 13:32:28 -0400
cover-img: /assets/img/office.jpg
tags: [regression-analysis, regression]
---

# 1차 과제 분석

## Q1.

x = number of copiers
y = serviced time

<center>
<img src="https://user-images.githubusercontent.com/37768791/84624318-1f761980-af1c-11ea-9cd7-b69085d582dc.png">
</center>

#### (a) Obtain the estimated regression function.

**해설**
beta0과 beta1을 R을 통해 찾아서 회귀식을 만들어주는 문제

beta0 = -0.5802
beta1 = 15.0352

=> 답 : E{Y} = -0.5802 + 15.0352 \* X

#### (b) Plot the estimated regression function and the data. How well does the estimated regression function fit the data.

**해설**
산점도를 그리고 (a)번에서 나온 회귀식을 1차 함수로 그려줌. 점들이 회귀식 부근에 잘 분포되어 있는 것 같으면 fit한 것임(시각적으로 확인하는 거라 주관적임)

<center>
 <img src="https://user-images.githubusercontent.com/37768791/84624704-da9eb280-af1c-11ea-9fa2-1c53b003616b.png">
 </center>

=> 답 : The regression line seems to fit the data well in visual.

</br>

## Q2.

#### (a) Estimate the change in the mean service time when the number of copiers serviced increases by one. Use a 90 percent confidence interval. Interpret your confidence interval.

**해설**
신뢰구간 90퍼센트를 사용해서 프린터 개수(X값)가 1씩 증가할 때마다 서비스 시간(Y값)의 변화를 추정하라는 문제. 결국 X의 계수인 beta1을 추정하면 됨.(이 값에 따라 변화정도가 달라지기 때문)

- beta1 = 15.0352
- t검정을 하면 t(1-(1-0.9 / 2), 45 - 2)의 값이 t_value는 1.6811이 나옴.
- s{b1} = 0.4831

-> 신뢰구간 : (beta1 - (t*value * s{b1}), beta1 + (t*value * s{b1}))

답 : (14.2231, 15.8474)

#### (b) Conduct a t-test to determine whether or not there is a linear association between X and Y here; control the alpha risk at 0.1. State the alternatives, decision rule, and conclusion. What is the P-value of your test?

**해설**
X와 Y사이에 일차적인 관계가 있는지 파악하기 위해 t검정을 하는 문제. 조건은 알파값이 0.1. 결국 p값을 구하면 된다.

Decision rule

- H0 : beta1 = 0 (no linear association between X and Y)
- Ha : beta1 != 0 (have linear association between X and Y)
