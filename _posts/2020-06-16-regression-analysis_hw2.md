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

n = 45
b1 =
