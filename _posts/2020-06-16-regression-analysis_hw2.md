---
layout: post
title: "회귀분석 알아보기 - 과제분석"
subtitle: 2차 과제
date: 2020-06-15 13:32:28 -0400
cover-img: /assets/img/regression_img2.png
tags: [regression-analysis, regression]
---

## Q1

- X = number of copiers
- Y = serviced time

<center>
<img src="https://user-images.githubusercontent.com/37768791/84624318-1f761980-af1c-11ea-9cd7-b69085d582dc.png">
</center>

#### (a) Set up the ANOVA table.

[해설]

아노바 테이블은 full_model과 reduce_model을 비교할 때 사용. 두 개의 회귀식을 구한 후, R언어로 anova 테이블을 돌려서 비교한다.

답 :

<center>
<img src="https://user-images.githubusercontent.com/37768791/84721888-b2ff2700-afbc-11ea-9257-e98f468993a9.png">
</center>

#### (b) Conduct a F-test to determine whether or not there is a linear association between time spent and number of copiers serviced; use $\alpha$ = 0.1. State alternatives, decision rule, and conclusion.

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

#### (c) By how much, relatively, is total variation in number of minutes spent on a call reduced when the number of copiers serviced is introduced into analysis? Is this a relatively small or large reduction? What is the name of this measure?

[해설]
reduced_model($\beta$1이 0인 회귀식)과 비교하는 문제인데 그렇다면 바로 anova 테이블을 돌리고 위에 (a)번에 있는 anova 테이블을 참고해서 SSR / SSTO의 값을 구하면 됨.

<center>
<img src="https://user-images.githubusercontent.com/37768791/84721888-b2ff2700-afbc-11ea-9257-e98f468993a9.png">
</center>

- SSR = 76960
- SSTO = 76960(Regression, SS) + 3416(ERR, SS)
- SSR / SSTO = R^2 = 0.9575 (0과 1사이의 값) (1에 가까우니 large reduction)

답 : large reduction, coefficient of determination(R^2)

#### (d) Calculate r and attach the appropriate sign.

[해설]
r 값을 구하는 문제인데 (c)번에서 푸는 방식으로 풀어서 루트만 씌워주면 됨.

r = root 0.9575 = 0.9875

답 : 0.9875

## Q2

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

<center>
<img src="https://user-images.githubusercontent.com/37768791/84730639-b650dd80-afd1-11ea-945e-67598551c91d.png">
</center>
