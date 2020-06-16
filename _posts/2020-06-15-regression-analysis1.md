---
layout: post
title: "회귀분석 알아보기 - 과제분석"
subtitle: 1차 과제
date: 2020-06-15 13:32:28 -0400
cover-img: /assets/img/regression_img.jpg
tags: [regression-analysis, regression]
---

## Q1.

x = number of copiers
y = serviced time

<center>
<img src="https://user-images.githubusercontent.com/37768791/84624318-1f761980-af1c-11ea-9cd7-b69085d582dc.png">
</center>

#### (a) Obtain the estimated regression function.

[해설]

beta0과 beta1을 R을 통해 찾아서 회귀식을 만들어주는 문제

b0 = -0.5802

b1 = 15.0352

=> 답 : E{Y} = -0.5802 + 15.0352 \* X

#### (b) Plot the estimated regression function and the data. How well does the estimated regression function fit the data.

[해설]

산점도를 그리고 (a)번에서 나온 회귀식을 1차 함수로 그려줌. 점들이 회귀식 부근에 잘 분포되어 있는 것 같으면 fit한 것임(시각적으로 확인하는 거라 주관적임)

<center>
 <img src="https://user-images.githubusercontent.com/37768791/84624704-da9eb280-af1c-11ea-9fa2-1c53b003616b.png">
 </center>

=> 답 : The regression line seems to fit the data well in visual.

## Q2.

#### (a) Estimate the change in the mean service time when the number of copiers serviced increases by one. Use a 90 percent confidence interval. Interpret your confidence interval.

[해설]

신뢰구간 90퍼센트를 사용해서 프린터 개수(X값)가 1씩 증가할 때마다 서비스 시간(Y값)의 변화를 추정하라는 문제. 결국 X의 계수인 beta1을 추정하면 됨.(이 값에 따라 변화정도가 달라지기 때문)

- b1 = 15.0352
- t검정을 하면 t(1-(1-0.9 / 2), 45 - 2)의 값이 t_value는 1.6811이 나옴.
- s{b1} (표준오차) = 0.4831

-> 신뢰구간 : (b1 - (t_value \* s{b1}), b1 + (t_value \* s{b1}))

답 : (14.2231, 15.8474)

#### (b) Conduct a t-test to determine whether or not there is a linear association between X and Y here; control the alpha risk at 0.1. State the alternatives, decision rule, and conclusion. What is the P-value of your test?

[해설]

X와 Y사이에 일차적인 관계가 있는지 파악하기 위해 t검정을 하는 문제. 조건은 알파값이 0.1. 결국 p값을 구하면 된다.

Decision rule

- H0 : beta1 = 0 (no linear association between X and Y)
  -> beta1이 0이면 X가 없어지기 때문에 관계가 사라짐.
- Ha : beta1 != 0 (have linear association between X and Y)

alpha (유의수준) = 0.1

b1 = 15.0352

s{b1} = 0.4831

t_value (검정값) = t(1 - (1 - 0.9 / 2), 45 - 3) = 1.6811

t-statistic (통계량 / t\*)= b1 - 0 / s{b1} = 31.122

t-statistic not include in -1.6811 <= t\* <= 1.6811
reject H0, conclude Ha.

답 : have linear association between X and Y, P-value = 2.2\*10^-16

#### (c) Are your results in parts (a) and (b) consistent? Explain.

[해설]

(a)번은 베타1의 신뢰구간이고 (b)번에서는 베타1이 0이 아님을 보여준다. 그렇다면 신뢰구간에서 0이 포함되는지를 판단해야한다.

답 : consistent

#### (d) The manufacturer has suggested that the mean required time should not increase by more than 14 minutes for each additional copier that is serviced on a service call. Conduct a test to decide whether this standard is being satisfied by Tri-City. Control the risk of a Type I error at 0.05. State the alternatives, decision rule, and conclusion. What is P-value of the test.

[해설]
서비스 시간(Y값)이 14분을 넘기면 안된다고 하는데, 이에 대해서 테스트를 해서....

- H0 : beta1 <= 14
- Ha : beta1 > 14

alpha = 0.05

b1 = 15.0352

s{b1} = 0.4831

t-value = (1 - (1 - 0.9 / 2), 45 -2) = 1.6811

t-statistic(t\*) = b1 - 14 / s{b1} = 2.1429

t\* not include in t-value range => conclude Ha => beta1 > 14

답 : beta >14, p_value = 0.01891

## Q3

#### (a) Obtain a 90 percent confidence interval for the mean service time on calls in which six copiers are serviced. Interpret your confidence interval.

[해설]

6개의 프린터라는 조건에서 서비스 시간(y값)에 대해 90퍼센트의 신뢰구간을 구하라는 얘기인데 그럼 X값에 6을 대입하고 신뢰구간을 구하면 된다.

E{Y_6} = -0.5802 + 15.0352 \* 6 = 89.631

t_value = (1 - (1 - 0.9 / 2), 45 - 2) = 1.6811

root_MSE(잔차의 표준오차) = 8.914

s{y_hat} (y_hat에 대한 표준오차)= 8.914 + sqrt(1 / 45 + (6 - X) / sum(X_i - X)^2) = 1.3965

신뢰구간 = (E{Y} - (t_value \* s{y_hat}), E{Y} + (t_value \* s{y_hat}))

답 : (74.4643, 91.9788)

#### (b) Obtain a 90 percent prediction interval for the service time on the next call in which six copiers are serviced. Is your prediction interval wider than the corresponding confidence interval in part (a)? Should it be?

[해설]

6개의 프린트가 서비스되는 다음 전화(미래의 일어날 new observation을 예측)에 서비스 시간을 신뢰구간 90퍼센트로 추정하기. 그렇게 추정한 값이 (a)번보다 넓은가? 그래야하는가?

E{Y_6} = -0.5802 + 15.0352 \* 6 = 89.631

t_value = 1.6811

s{pred} = sqrt(MSE \* (1 + 1 / 45 + (X_h - X) / sum(X_i - mean(X)^2))) = 1.0122

신뢰구간 = (E{Y_6} - (t_value \* s{pred}), E{Y_6} + (t_value \* s{pred}))

답 : (74.4643, 104.7983), more wider

#### (c) Management wishes to estimate the expected service time per copier on calls in which six copiers are serviced. Obtain an appropriate 90 percent confidence interval by covering the interval obtained in part (a). Interpret the converted confidence interval.

[해설]

6개의 프린트가 서비스될 때 각각의 프린트 서비스 시간을 추정하고 싶음. 추정은 (a)번 문제를 활용해서 90퍼센트의 신뢰구간을 구하라.

E{Y_6}\_per = (-0.5802 + 15.0352 \* 6) / 6 = 89.631 / 6 = 14.939

t_value = 1.6811

## Q4

airfreight breakage data

- X = transferred ampules
- Y = number of ampules

<center>
<img src="https://user-images.githubusercontent.com/37768791/84688222-f1272700-af79-11ea-8f03-9b920eb81a7e.png">
</center>

#### (a) Obtain the estimated regression function. Plot the estimated regression function and the data. Does a linear regression function appear to give a good fit here.

[해설]

회귀식 그리고 산점도와 회귀식이 일치하는지 확인

b0 (intercept)= 10.2

b1 = 4

E{Y} = 10.2 + 4 \* X

<center>
<img src="https://user-images.githubusercontent.com/37768791/84688521-71e62300-af7a-11ea-92d0-5bf37b2cc672.png">
</center>

답 : hard to tell the linear regression function fit the data as there are only four levels of X.

#### (b) Obtain a point estimated of the expected number of broken ampules when X = 1 transfer is made.

[해설]

X가 1일 때 Y의 추정값을 구해라.

X = 1

E{Y} = 10.2 + 4 \* 1 = 14.2

답 : 14.2

#### (c) Estimate the increase in the expected number of ampules broken when there are 2 transfers as compared to 1 transfer.

[해설]

X가 1일 때와 비교해서 X가 2일 때 Y의 증가를 추정

- X = 1
  E{Y} = 10.2 + 4 \* 1 = 14.2

- X
  E{Y} = 10.2 + 4 \* 2 = 18.2

답 : 18.2 - 14.2 = 4

#### (d) Verify that your fitted regression lines goes through the point(X_bar, Y_bar)

[해설]

위에서 만든 회귀식에 X, Y 평균값을 넣었을 때 성립하는지 확인

- X_bar = 1
- Y_bar = 14.2

  10.2 + 4 \* 1 = 14.2 => 성립함

답 : regression lines goes through the point(X_bar, Y_bar)

## Q5

#### In Airfreight breakage Problem1.21, the least squares estimates are b0 = 10.2 and b1 = 4, and SSR(sum of squared residuals) = 17.6, Evaluate the least squares criterion Q in (1.8) for the estimates

- #### (1) b0 = 9, b1 = 3;
- #### (2) b0 = 11, b1 = 5

#### Is the criterion Q larger for these estimates than for the least squres estimates?

[해설]

(1)번과 (2)번의 경우 잔차제곱합이 기존 잔차제곱합보다 큰지 확인

(1) b0 = 9, b1 = 3, Q = 76
(2) b0 = 11, b1 = 5, Q = 60

답 : two values are larger than 17.6

## Q6

#### For each of the following questions, explain whether a confience interval for a mean response or as prediction interval for a new observation is appropriate.

- (a) What will be the humidity level in this greenhouse tommorrow when we set the temperature level at 31C?
- (b) How much do families whose disposable income is \$23,500 spend, on the average, for meals away from home?
- (c) How many kilowatt-hours of celebrity will be comsumed next month by commercial and industrial users in the Twin Cities service area, given that the index if business activity for the area remains at its present level?

[해설]

평균반응에 대한 신뢰구간 추정 vs 새로운 관찰에 대한 예측 구간 추정

- (a) : will 이므로 prediction interval
- (b) : mean = average, confidence interval
- (c) : will 이므로 prediction interval

답 : prediction interval, confidence interval, prediction interval

## Q7

**Show that**

<center>
<img src="https://user-images.githubusercontent.com/37768791/84690981-54b35380-af7e-11ea-97fa-2e151bc6bebe.png">
</center>

#### (a)

<center>
<img src="https://user-images.githubusercontent.com/37768791/84690644-c4750e80-af7d-11ea-84a3-88d3dd6f79fb.png">
</center>

#### (b)

<center>
<img src="https://user-images.githubusercontent.com/37768791/84690749-f1292600-af7d-11ea-9fce-92c87b6eff1e.png">
</center>

#### (c)

<center>
<img src="https://user-images.githubusercontent.com/37768791/84690773-01410580-af7e-11ea-86eb-a3f26a20511c.png">
</center>

#### (d)

<center>
<img src="https://user-images.githubusercontent.com/37768791/84690814-1322a880-af7e-11ea-8575-8445e17ba202.png">
</center>

#### (e)

b0 = Y_bar - b1 \* X_bar

b0 + b1 \* X_bar = Y_hat

답 : regression line always passes the point (X_bar, Y_bar)

## Q8

**Show that**

<center>
<img src="https://user-images.githubusercontent.com/37768791/84691081-80363e00-af7e-11ea-8c28-13bfb7ce206e.png">
</center>

#### (a)

<center>
<img src="https://user-images.githubusercontent.com/37768791/84691203-b673bd80-af7e-11ea-8afa-f96aaead142b.png">
</center>

#### (b)

<center>
<img src="https://user-images.githubusercontent.com/37768791/84691241-ca1f2400-af7e-11ea-8f62-56b0b5217137.png">
</center>

#### (c)

<center>
<img src="https://user-images.githubusercontent.com/37768791/84691273-d60ae600-af7e-11ea-8844-4924e3a648a0.png">
</center>

#### (d)

<center>
<img src="https://user-images.githubusercontent.com/37768791/84691307-e28f3e80-af7e-11ea-8f9e-6bff817fd46b.png">
</center>
