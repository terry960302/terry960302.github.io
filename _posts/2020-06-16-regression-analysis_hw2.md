---
layout: post
title: "회귀분석 알아보기 - 과제분석"
subtitle: 2차 과제
date: 2020-06-16 9:32:28 -0400
cover-img: /assets/img/regression_img.png
tags: [regression-analysis, regression]
---

# Q1

<br>

- X = number of copiers
- Y = serviced time

<center>
<img src="https://user-images.githubusercontent.com/37768791/84624318-1f761980-af1c-11ea-9cd7-b69085d582dc.png">
</center>

### (a)

#### Set up the ANOVA table.

<span style="color:blue">
[해설]

아노바 테이블은 full_model과 reduce_model을 비교할 때 사용. 두 개의 회귀식을 구한 후, R언어로 anova 테이블을 돌려서 비교한다.

답 :

<center>
<img src="https://user-images.githubusercontent.com/37768791/84721888-b2ff2700-afbc-11ea-9257-e98f468993a9.png">
</center>
</span>

### (b)

#### Conduct a F-test to determine whether or not there is a linear association between time spent and number of copiers serviced; use $\alpha$ = 0.1. State alternatives, decision rule, and conclusion.

<span style="color:blue">
[해설]

linear association이 있는지 알기 위해서는 베타1의 값이 0인지 아닌지를 판단하면 됨. 귀무가설, 대립가설을 이용해서 F 통계량을 구한다음 기각영역에 들어가는지 안들어가는지 판단해서 결론을 내리면 됨.

- H0 : $\beta$1 = 0 (no association)
- Ha : $\beta$1 != 0 (have association)

위 ANOVA 테이블에 F값을 참고하면

- $\alpha$ = 0.1 (유의수준)
- F_value = f(1, 45 - 2) = 2.8260
- F_statistic = 986.88
- P_value (Pr(>F)) = 2.2 \* 10^-16 (F 이상일 확률)

F_value < F_statistic, statistic value is more larger than F_value(placed right side of graph = reject area), and p_value also placed on right side of graph as compared to $\alpha$.

so reject H0, conclude Ha.

<center>
<img src="https://user-images.githubusercontent.com/37768791/84722858-52251e00-afbf-11ea-8a86-9cc99807f9df.png">
</center>

답 : there is linear association between time spent and number of copiers.
</span>

###

#### (c) By how much, relatively, is total variation in number of minutes spent on a call reduced when the number of copiers serviced is introduced into analysis? Is this a relatively small or large reduction? What is the name of this measure?

<span style="color:blue">
[해설]
reduced_model($\beta$1이 0인 회귀식)과 비교하는 문제인데 그렇다면 바로 anova 테이블을 돌리고 위에 (a)번에 있는 anova 테이블을 참고해서 SSR / SSTO의 값을 구하면 됨.

<center>
<img src="https://user-images.githubusercontent.com/37768791/84721888-b2ff2700-afbc-11ea-9257-e98f468993a9.png">
</center>

- SSR = 76960
- SSTO = 76960(Regression, SS) + 3416(ERR, SS)
- SSR / SSTO = R^2 = 0.9575 (0과 1사이의 값) (1에 가까우니 large reduction)

답 : large reduction, coefficient of determination(R^2)
</span>

### (d)

#### Calculate r and attach the appropriate sign.

<span style="color:blue">

[해설]
r 값을 구하는 문제인데 (c)번에서 푸는 방식으로 풀어서 루트만 씌워주면 됨.

r = root 0.9575 = 0.9875

답 : 0.9875
</span>

# Q2

<br>

- X = number of copiers
- Y = serviced time

<center>
<img src="https://user-images.githubusercontent.com/37768791/84624318-1f761980-af1c-11ea-9cd7-b69085d582dc.png">
</center>

#### (a) Test whether or not there is a linear association between number of copiers and total number of minutes spent by a service person. Use a t-test with $\alpha$ = 0.01. State the null, alternatives, decistion rule, and conclusion. What is the P-value of the test?

[해설]
X와 Y간의 직선 함수의 관계가 있는지를 가설검증(그 중 t검정)을 통해 알아보라는 얘기

- b1 = 15.0352
- s{b1} = 0.4831
- t_value = t(1 - (1 - 0.9) / 2, 45 - 2) = 2.695
- t_statistic = b1 - 0 / s{b1} = 31.123

Decision rule

- H0 : $\beta1$ = 0(no association)
- Ha : $\beta1$ != 0(have association)

t_statistic >= t_value, so statistic value are in reject null-hypotheses area, conclude Ha.

답 : have linear association between number of copiers and total number of minutes. P_value = 2.2 \* 10^-16

#### (b) Test whether or not there is a "positive" linear association between number of copiers and total number of minutes spent by a service person. Use a t-test with $\alpha$ = 0.01. State the null, alternatives, decistion rule, and conclusion. What is the P-value of the test?

[해설]

(a)번 문제와는 거의 비슷한데 관계가 양의 관계에 놓여있는지 검증해야함. 이때는 가설검증할 때, 기존에는 0이 "이다 / 아니다"로 뒀지만 양수, 음수를 고려해야하므로 "이상, 이하"로 놔야함.

- b0 = - 0.5802
- b1 = 15.0352
- s{b1} = 0.4831
- t_value = t(1 - (1 - 0.9) / 2, 45 - 2) = 2.695
- t_statistic = b1 - 0 / s{b1} = 31.123

Decision rule

- H0 : $\beta1$ <= 0 (cause of b0 < 0, E{Y} become negative)
- Ha : $\beta1$ > 0 (positive association)

t_statistic >= t_value, so statistic value are in reject null-hypotheses area, conclude Ha.

답 : there is positive linear association between X and Y, P_value = 2.2 \* 10^-16

#### (c) Find 99% confidence interval of $\beta1$. Can we know the test result of part (a) from this confidence interval?

[해설]

일단 이전에는 90% 신뢰구간으로 베타1 값을 추정했는데 이번에는 99%로 추정하고, 이 추정 범위만 보고 (a)번의 가설검증 결과값을 알 수 있는가?

- b1 = 15.0352
- s{b1} = 0.4831
- t_value = t(1 - (1- 0.99)/2, 45 - 2) = 2.6951
- root_MSE(잔차 표준오차) = 8.914

99% 신뢰구간 : b1 +- root_MSE = (13.7333, 16.3372)

beta1 does not include 0 according to 99% confidence interval, so reject H0(null hypotheses) : $\beta1$ = 0, conclude Ha.

답 : we know the result of test (a) according to 99% confidence interval.

## Q3

**Refer to Copier maintenance Problem 1.20. As in part(a) Q2, we would like to test whether or not there is a linear association between number of copiers and total number of minutes spent by a service person. Here, we use a general linear test with $\alpha$ = .01.**

#### (a) State full and reduced models.

[해설]

그냥 베타1 = 0일 때와 아닐때의 두개의 회귀식을 만들어주면 됨.

- b0 = - 0.5802
- b1 = 15.0352

답 :

- full_model
  => Y_hat = $\beta0$ + $\beta1$ \* X
  => Y_hat = -0.5802 + 15.0352 \* X
- reduced_model
  => Y_hat = $\beta0$
  => Y_hat = -0.5802

#### (b) Obtain (1) SSE(F), (2) SSE(R), (3) dfF. (4) dfR, (5) test statistic F\* for the general linear test, (6) decision rule (7) p-value.

[해설]

- (1) SSE(F) : Full Model의 잔차제곱합

  - Y_hat = -0.5802 + 15.0352 \* X
  - redisuals = Y - Y_hat
  - SSE(F) = sum(residuals^2) = 3416.377

- (2) SSE(R) : Reduced Model의 잔차제곱합

  - Y_hat = -0.5802
  - redisuals = Y - mean(Y)
  - SSE(F) = sum(residuals^2) = 80376.8

- (3) dfF : 데이터의 개수 / 자유도, full model은 전체 개수 - 2
  - 45 - 2 = 43
- (4) dfR : 데이터의 개수 / 자유도, full model은 전체 개수 - 1
  - 45 - 1 = 44
- (5) test statistic F\* : (SSE(R) - SSE(F) / 1) / (SSE(F) / df(F))
  - ((80376.8 - 3416.377) / 1) / (3416.377 / 43) = 968.66
- (6) decision rule : 가설검증
  - H0 : $\beta1$ = 0(no association)
  - Ha : $\beta1$ != 0(have association)
  - f-statistic >= f_value -> reject null hypotheses
- (7) p-value : 확률 변수
  - p_value = Pr(F> 968.66) = 0(거의 0에 수렴한다).

#### (c) Are the test statistic F\* and the decision rule for the general linear test numerically equivalent to those in Q2(a)?

[해설]

t(1 - (1 - 0.99) / 2, 45 - 2) = f(1 - 0.99, 45 - 2)이기 때문에 값이 같다고 할 수 있다.

답 : Yes, those two tests are numerically equivalent.
