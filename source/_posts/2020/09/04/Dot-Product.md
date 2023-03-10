---
title: Dot Product 정리
date: 2020-09-04 14:07:47
categories:
  - Tech
  - 공학수학
tags: [Vector, Dot Product, Inner Product]
mathjax: true
---

공학수학 수업 중 등장한 내적(Dot Product)의 개념에 대해 정리를 해보려 한다.

## 내적(Dot Product)의 정체

먼저 살펴볼것은 벡터를 내적한다는 것이 무슨 의미인지에 관한 것이다.

`내적 | 內積 | inner product | scalar product`

적은 **쌓는다**는 뜻의 한자이고, 여기서는 **곱한다**는 뜻이다. 벡터의 곱하기는 두 가지 정의가 있는데, 내적은 벡터를 마치 수처럼 곱하는 개념이다. 그렇다 우리는 현재 벡터간의 곱하기를 하고 싶은 것이다.

### 물리적인 일(Work)의 개념에서 내적 이해하기

우리가 **일**을 한다라는 것은 **노동**을 한다고 할 수도 있지만, 물리학적으로 **일**을 한다는 것은 물체에 힘을 주어서 적당한 거리 만큼 이동하였을 때, 그게 얼마나 되는지를 측정하는 것이라 볼 수 있다.

{% asset_img work1.jpg '"노랭이가 일(Work)하고 있다" "노랭이가 일(Work)하고 있다"'%}

예를 들어 내가 나무토막에 F만큼의 힘을 주어서, s의 변위만큼 이동시켰다라고 이야기한다면, 내가 물체에 대해 한 일의 개념인 W는 아래와 같다

$$W_나=Fs$$

그런데 문제는 힘의 방향과 이동방향이 일치하지 않을 때의 경우인데, 아래의 그림처럼 이동한다고 생각해보자.

{% asset_img work2.png '"아저씨가 상자를 끌고가고 있다" "아저씨가 상자를 끌고가고 있다"'%}

우리가 물리학적으로 일을 한다는 개념의 전제 중 하나는 '힘의 방향'과 '이동 방향'이 서로 동일할 경우에만 일을 한다고 말할 수 있다는 것이다. 전제에 따라 분석해보면 우리는 이 상황에서의 힘 F를 다음과 같이 분해해볼 수 있다.

{% asset_img work3.jpeg '"힘 F 분해 해보기" "힘 F 분해 해보기"'%}

이때 이동방향과 같은 힘만이 일에 작용하므로 수직으로 가해진 힘은 물체에 일을 해준 것이 아니게 된다.
따라서 실제 아저씨가 물체에 대해 한 일은 아래와 같다.

$$W_{아저씨} = (F\cos\theta)s = Fs\cos\theta$$

핵심은 여기서 말한 F와 s가 모두, 방향성이 있는 벡터 값이라는 것이다. F는 힘의 크기와 방향이 존재하고, s 또한 얼마만큼 이동할 것인가(크기) 어디로 이동할 것인가(방향) 등의 요소가 존재한다.

이 두 벡터량에서, 우리는 위의 **일** 개념에 타당할 수 있는 내적을 정의할 수 있는데, 바로 F,s의 내적을 다음과 같이 정의하고,

$$W= \vec{F} \cdot \vec{S}$$

이동 방향, 작용하는 힘 사이의 각을 $\theta$라고 할 때,

$$\vec{F} \cdot \vec{S} = \Vert \vec F \Vert \cos \theta  * \Vert \vec S \Vert = \Vert \vec F \Vert \Vert \vec S \Vert \cos \theta $$

로 정의할 수 있는 것이다. 만약 첫번째 그림처럼 힘의 방향과 이동 방향이 같은 경우엔, 그 방향으로 100% 쓰인 것이므로 아래의 식처럼 되고, 나머지 경우는 각도에 해당하는 $\cos \theta$ 만큼이 곱해져서, 일의 정의에 맞는 물리량을 계산해 낼 수 있다.

$$W = \Vert \vec F \Vert \Vert \vec S \Vert \cos 0 = \Vert \vec F \Vert \Vert \vec S \Vert $$

### 내적의 기하학적 정의

내적을 이제 물리 개념이 아닌 일반적인 벡터에 적용해보면, 두 벡터 a,b에 대해 a,b가 이루는 각을 $\theta$라 할 때, 두 벡터의 내적을 다음과 같이 정의한다.
{% asset_img dot1.png '"기하학적 정의" "기하학적 정의"'%}

$$\vec a \cdot \vec b = \Vert \vec a \Vert \Vert \vec b \Vert \cos \theta $$

벡터 내적의 결과물은 스칼라 값이다. 벡터는 방향과 크기가 둘다 있고, 스칼라는 크기만 가지고 있음에 주의해야 한다. 벡터의 내적이라 함은 두 벡터끼리 곱하는 건데, 그 결과는 방향이 없는 스칼라 값이다...라고 해석해야 한다는 것이다. 우항의 인자들은 모두 스칼라 값이고, 이를 이용하면 내적의 교환법칙을 증명해 낼 수도 있다.

### 내적의 대수학적 정의

앞서 물리학적으로 내적을 알아봄과 동시에 기하학적 정의를 도출해 보았다.
그런데, 우리는 벡터를 성분으로 표현하는 법 역시 안다. 당연히 내적들도 연산을 성분으로 표현하는 방법을 생각할 수 있다.

{% asset_img dot2.png '"대수학적 정의" "대수학적 정의"' %}

### 내적의 속성

{% asset_img properties.jpg '"내적의 특성" "내적의 특성"' %}

### 제2코사인 법칙을 이용한 증명
