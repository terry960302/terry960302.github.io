---
layout: post
title: "머신러닝 개념에 발담구기"
subtitle: 4차 정리
date: 2020-06-18 4:58:28 -0400
cover-img: /assets/img/ml.jpeg
tags: [machine learning, cnn, rnn]
use_math: true
---

# 합성망 모형(CNN, Convolutional Neural Network)

이미지에 필터를 씌울 때는 내부에서는 이미지를 벡터화 시킨 후에 행렬의 연산이 들어갑니다. 이런 연산의 과정 중에서 `합성곱 연산`이 있습니다.

<center>
<img src="https://user-images.githubusercontent.com/37768791/84994105-26ec2b80-b185-11ea-9f46-b4117dd5a25c.png">
합성곱 과정 도식화
<img src="https://user-images.githubusercontent.com/37768791/84997121-1ccc2c00-b189-11ea-9196-46dd14d4fa2f.png">
cnn 구조
</center>

> 위 그림과 같이 이미지가 있으면 합성곱으로 표현을 합니다. 계속 샘플링과 합성곱 연산을 반복합니다.

<br>

아래는 합성곱 연산이 이루어지는 과정을 담은 움짤입니다.

<center>
<img src="../assets/img/Convolution_schematic.gif">
</center>

> 노란색이 필터이고 필터가 1씩 이동(`stride`=1)해가면서 필터의 값을 곱해서 최종적으로 우측 분홍색 표에 값이 반영이 됩니다. (참고로 x1 이라고 붉게 적혀있는 것이 가중치입니다.)

<br>

<center>
<img src="https://user-images.githubusercontent.com/37768791/84996669-8c8de700-b188-11ea-95c2-ca28785184dc.png">
</center>
<center>
<img src="https://user-images.githubusercontent.com/37768791/84996401-2ef99a80-b188-11ea-9040-2b91d8c3c0d2.png">
</center>

> 위의 움짤을 정지화면으로 해서 가중치를 구하는 과정을 시각화했습니다. 가중치와 곱한 값의 총 합을 Feature Map에 반영합니다.

<br>

### 합성곱 연산 과정

---

<center>
<img src="https://user-images.githubusercontent.com/37768791/84996861-c9f27480-b188-11ea-96e1-c84188a7add4.png">
</center>

- **채널** : 컬러 이미지는 RGB의 3개의 채널로 이루어지는 것과 같이 하나의 이미지를 산출해내기 위한 이미지들을 일컫습니다.
- **필터** : 이미지의 특징을 찾아내기 위한 공용 파라미터입니다. 가중치를 포함하고 있습니다.
- **Stride** : 필터가 이동하는 정도, 1이면 1칸씩 이동, 2면 두 칸씩 이동
- **특징맵** : 합성곱 계층의 입출력 데이터입니다.

### 패딩

: html을 좀 다뤄보셨던 분이라면 무슨 의미인지 바로 감이 오실 것입니다. 아래 그림을 봅시다.

<center>
<img src="https://user-images.githubusercontent.com/37768791/84997648-d0cdb700-b189-11ea-869e-972997bbefb8.png">
</center>

> 기존 특징맵 주위로 빈 칸이 둘러싸여져 있는 것을 볼 수 있습니다. 이 빈칸에 0을 넣어서 필터로 연산이 이루어지게 합니다. 계산과정은 이전과 같이 기존 특징맵에 필터를 씌우고 1씩 이동해가면서 곱하고 모두 더해서 우측 특징맵에 반영하는 구조입니다.

<br>

### 풀링 연산(Pooling)

: 가로, 세로 방향의 공간을 줄이는 연산입니다. 유형으로는 평균 풀링과 최대 풀링이 있습니다.

<center>
<img src="https://user-images.githubusercontent.com/37768791/84998198-7d0f9d80-b18a-11ea-8bd1-e5ec7257ada9.png">
최대 풀링(max pooling)
</center>
