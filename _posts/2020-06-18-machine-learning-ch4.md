---
layout: post
title: "머신러닝 개념에 발담구기4"
subtitle: CNN, RNN
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

> 위의 움짤을 정지화면으로 해서 특징맵 산출 과정을 시각화했습니다. 기존 특징맵과 필터의 곱한 값의 총합을 Feature Map에 반영합니다.

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

---

: html을 좀 다뤄보셨던 분이라면 무슨 의미인지 바로 감이 오실 것입니다. 아래 그림을 봅시다.

<center>
<img src="https://user-images.githubusercontent.com/37768791/84997648-d0cdb700-b189-11ea-869e-972997bbefb8.png">
</center>

> 기존 특징맵 주위로 빈 칸이 둘러싸여져 있는 것을 볼 수 있습니다. 이 빈칸에 0을 넣어서 필터로 연산이 이루어지게 합니다. 계산과정은 이전과 같이 기존 특징맵에 필터를 씌우고 1씩 이동해가면서 곱하고 모두 더해서 우측 특징맵에 반영하는 구조입니다.

<br>

### 풀링 연산(Pooling)

---

: 가로, 세로 방향의 공간을 줄이는 연산입니다. 유형으로는 평균 풀링과 최대 풀링이 있습니다.

<center>
<img src="https://user-images.githubusercontent.com/37768791/84998198-7d0f9d80-b18a-11ea-8bd1-e5ec7257ada9.png">
최대 풀링(max pooling)
</center>

> 그림에서 보이는 것과 같이 기존 특징맵에서 특정 크기만큼 샘플링해서 최댓값을 결과 특징맵에 반영합니다. 평균 풀링도 평균을 구한다는 점만 다를 뿐 거의 비슷해서 생략합니다. 참고로 필터를 씌우는 과정이 아니라 기존 특징맵의 크기를 줄이는 과정입니다.

<br>

# 순환신경망 모형(RNN, Recurrent Neural Network)

: 신경망에 `시간`이라는 개념을 도입해서 `순차적`으로 들어오는 정보를 처리하는 신경망입니다. CNN은 주로 이미지에서 사용되었지만, RNN은 주로 자연어나 음성신호, 주식 같은 곳에서 사용됩니다.

<center>
<img src="https://user-images.githubusercontent.com/37768791/85019471-d935ea00-b1a9-11ea-8f2a-88b3f6b668be.png">
</center>

> 위 그림을 보면 x값이 입력되고 특정 함수를 통해 y가 산출되는 것을 볼 수 있는데 기존 뉴런과 다른 점은 산출된 y값이 출력되기는 하지만 동시에 옆에 은닉층의 x로 입력되는 점입니다. 이것이 시간이 지나면서 계속 y가 x로 이동하는 과정을 도식화한 것입니다.

<br>

`메모리셀`

: 은닉층에서 활설화함수를 통해 산출값을 내보내는 노드를 메모리셀이라고 합니다. 그림에서는 하나의 노드를 나타냅니다.

<br>

### LSTM 모형 (`L`ong `S`hort `T`erm `M`emory)

---

: 셀 하나는 서로 상호작용하는 4개의 입력계층(1개의 기억셀 + 3개의 게이트)로 구성된다는 말입니다.

`[등장배경]`  
기존 RNN의 문제점이 있었습니다. 위에서도 언급했듯이 시간이 지나면서 노드는 산출값을 옆에 노드에 계속 전달을 합니다. 그래서 이전 데이터들을 기반으로 앞서 나올 말이나 음성에 대해 예측을 할 수 있습니다. 그러나 이 과정이 너무 길어지면 너무 옛날에 말했던 데이터를 기반으로 접근을 해야하는데 이를 `장기 의존성 문제`라고 합니다. 이를 해결하기 LSTM이 나왔습니다.

아래 기존 rnn 모형과 lstm 모형을 비교해보겠습니다.

<center>
<img src="https://user-images.githubusercontent.com/37768791/85024050-827fde80-b1b0-11ea-81cc-1834437800c1.png">
</center>

vanila RNN은 기본적인 RNN입니다. 보시면 산출값 전달만 하고 있습니다. 그러나 LSTM모형에서는 4가지의 레이어가 있습니다.

- 망각게이트  
  : t-1 지점에서 어떤 정보를 버릴지 결정합니다.
- 입력게이트  
  : 어떤 정보를 셀 상태에 저장할지 결정합니다.
- 셀 상태 업데이트  
  : 입력 게이트에서 계산한 값과 이전 시점의 셀 상태의 값을 더하여 계산합니다.
- 출력게이트  
  : 업데이트한 셀 상태의 값에 tanh를 변환한 뒤, o_t를 곱합니다.
