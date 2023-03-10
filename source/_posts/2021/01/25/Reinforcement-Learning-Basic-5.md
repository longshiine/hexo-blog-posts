---
title: "강화학습 기초: 강화학습과 정책평가"
date: 2021-01-25 13:10:37
categories:
  - Tech
  - 강화학습
tags: [ReinforcementLearning, MonteCarloApproximation, TemporalDifference]
mathjax: true
---

> 본 포스트는 "파이썬과 케라스로 배우는 강화학습" 도서의 여섯번째 리뷰 포스트입니다.
> http://www.yes24.com/Product/Goods/44136413

## 4장 강화학습 기초 3: 강화학습과 정책평가

강화학습과 다이내믹 프로그래밍의 차이는 강화학습은 환경의 모델을 몰라도 환경과의 상호작용을 통해 최적 정책을 학습한다는 것이다. 이때 에이전트는 환경과의 상호작용을 통해 **주어진 정책에 대한 가치함수를 학습** 할 수 있는데 이를 `예측`이라고 한다. 또한 가치함수를 토대로 **정책을 끊임없이 발전시켜 나가서 최적 정책을 학습** 하려는 것이 `제어`이다.

**예측** 에는 `몬테카를로 예측`과 `시간차 예측`이 있으며, **제어** 에는 시간차 제어인 `살사`가 있고, 살사의 한계를 극복하기 위한 오프폴리시 제어인 `큐러닝`이 있다.

### 사람의 학습 방법과 강화학습의 학습 방법

강화학습의 에이전트는 어떻게 모델 없이 **순차적 행동 결정 문제** 를 풀 수 있을까? 강화학습의 **학습** 은 어떻게 작동하는 것일까?🧐

다이내믹 프로그래밍을 한번 살펴보자. 다이내믹 프로그래밍은 상태나 차원이 증가할수록 계산 복잡도가 기하급수적으로 증가하는데, 이는 환경에 대한 정확한 정보를 가지고 **모든 상태에 대해 동시에 계산을 진행하기 때문이다.** 마치 바둑을 둘때 모든 경우의 수를 고려하여 두는 형상이다. 사람도 그럴까? 사람도 바둑을 둘 때, 모든 경우의 수를 고려하여 두는 것일까? 아니다! 사람은 학습의 많은 부분을 그냥 바둑을 두면서 진행한다. 후에 복기를 하면서 어디서 잘못을 했고, 어떻게 고쳐야 할까를 고민한다. 여기에 강화학습이 학습을 어떻게 하는가에 대한 답이 있다.

> **강화학습** 은 일단 해보고, 자신을 평가한뒤, 평가한 대로 자신을 업데이트 하며,이 과정을 무수히 반복한다.

### 강화학습의 예측과 제어

MDP로 정의되는 문제를 풀 때 중요한 것은 **벨만 기대 방정식의 기댓값인 $E_\pi$를 어떻게 계산하는가** 이다. 정책 이터레이션 대로 계산을 반복한다면 한 치의 오차도 없는 정확한 기댓값을 얻을 수 있으나, 이러한 다이내믹 프로그래밍을 적용할 수 있는 문제는 많지 않다.

사람은 어떤것을 판단할 때, **항상 정확한 정보를 근거로 판단하지 않는다.** 이터레이션보다 정확하진 않지만 **적당한 추론** 을 통해 학습을 해나가는 것이 실제 세상에서는 더 **효율적** 이다. 강화학습에서는 적당한 추론을 통해 원래 참 가치함수의 값을 `예측(prediction)`한다. 강화학습에서는 예측과 함께 정책을 발전시키는 것을 `제어(Control)`이라 부른다.

### 몬테카를로 근사의 예시

보통 원의 넓이를 계산하기 위해서는 원의 방정식($S = \pi r^2$)을 찾아 그 방정식을 이용해 계산한다. 근데, 원의 방정식을 모른다면? 넓이를 어떻게 계산해야 할까.

이때 사용할 수 있는 방법이 `몬테카를로 근사(Monte-Carlo Approximation)`이다. 몬테카를로라는 말은 **무작위로 무엇인가를 해본다**는 의미이며, 근사라는 것은 원래의 값은 모르지만 샘플을 통해 원래의 값이 이럴것이다라고 추정하는 것이다. 즉 **무작위로 무엇인가를 해서 원래의 넓이를 추정하는 것** 이 몬테카를로 근사이다.

몬테카를로 근사로 원의 넓이를 계산하는 예제를 한번 보자. 원(A)이 그려진 네모난 종이(B) 위에 점을 뿌린다고 생각해보자. 전체 뿌린 점들 중에서 A에 들어가 있는 점의 비율을 구하면 이미 알고 있는 $S(B)$를 통해서 $S(A)$의 값을 추정할 수 있다. **더 많은 샘플을 사용할수록 오차는 적어지며 무한히 반복하면 원래의 값과 동일해진다.**

$$
if \\\; n \to \infty, \\\, then \\\\
{1 \over n} \sum_{i=1}^n I(RedDot_i \in A) = {S(A) \over S(B)}
$$

{% asset_img montecarlo.png 300 300 '"무수히 많은 점으로 원의 넓이를 추정" "무수히 많은 점으로 원의 넓이를 추정"' %}

원의 넓이의 방정식을 알면 한번에 구할 수 있는데, 왜 이런 고생을 할까?😰 몬테카를로 근사의 장점은 **방정식을 몰라도 원래 값을 추정할 수 있다** 는 것이다. 어떤 도형이든 상관없이 모든 도형의 넓이를 정확하진 않더라도 구할 수 있다. 아래와 같은 그림도 말이다!(아래의 그림에 대한 도형의 방정식은 존재하지 않는다). 이처럼 방정식을 몰라도 반복하기만 하면 답을 구할 수 있다는 장점은 강화학습에 그대로 이용된다.

{% asset_img drawing.png 300 300 '"도형의 모양에 상관없이 몬테카를로 근사로 구할 수 있다" "도형의 모양에 상관없이 몬테카를로 근사로 구할 수 있다"' %}

### 몬테카를로 예측(Monte-Carlo Prediction)

원의 넓이를 추정하는 대신 가치함수를 추정해보자. 원의 넓이를 추정할 때는 점이 하나의 **샘플** 이며 점을 찍는 것이 **샘플링(sampling)** 이었다. 가치함수를 추정할 때는 에이전트가 **한 번 환경에서 에피소드를 진행하는 것** 이 샘플링이다. 샘플의 평균으로 참 가치함수의 값을 추정할 것인데, 이때 몬테카를로 근사를 사용하는 것을 `몬테카를로 예측(Montecarlo Prediction)`이라고 한다.

정책 이터레이션은 벨만 기대 방정식의 기댓값이란 녀석을 실제로 계산한 것이다. 샘플링을 통해 기댓값을 계산하지 않고 **샘플들의 평균으로 가치함수를 예측** 하려면 어떻게 해야할까?

몬테 카를로 예측에서는 환경의 모델을 알아야 하는 $E_\pi$를 계산하지 않는다. 환경의 모델을 몰라도 여러 에피소드를 통해 구한 **반환값의 평균** 을 통해 $v_\pi (s)$를 추정한다.

$$v_\pi (s) \sim {1 \over N(s)}\sum_{i=1}^{N(s)}G_i(s)$$

한 번의 에피소드로는 추정이 불가능하다. 그것은 마치 원의 넓이를 구할 때 점을 한번 찍는 것과 같으며 몬테카를로 예측을 하기 위해서는 각 상태에 대한 반환값들이 많이 모여야 한다. 각 상태에 모인 반환값들의 평균을 통해 참 가치함수의 값을 추정한다.

현재 정책에 따라 무수히 많은 에피소드를 진행해 보면 현재 정책을 따랐을 때 지날 수 있는 모든 상태에 대해 **충분한 반환값** 들을 모을 수 있다. 따라서 상당히 정확한 가치함수의 값을 얻을 수 있다.

반환값들의 평균을 취하는 식을 조금 자세히 살펴보자. 편의상 상태에 대한 표현을 생략한다. n개의 반환값을 통해 평균을 취한 가치함수를 $V_{n+1}$이라고 하는데 여기서 대문자로 표현하는 이유는 오차가 내포되었다는 의미이다. 업데이트식을 정리해가는 과정은 다음과 같다.

$$
V_{n+1} = {1 \over n}\sum_{i=1}^n G_i = {1 \over n} \biggl( G_n + \sum_{i=1}^{n-1}G_i \biggl) \\\\
= {1 \over n} \biggl( G_n + (n-1)({1 \over n-1})\sum_{i=1}^{n-1}G_i \biggl) \\\\
= {1 \over n} ( G_n + (n-1)V_n) \\\\
= {1 \over n} ( G_n + nV_n - V_n) \\\\
= V_n + {1 \over n}(G_n - V_n) \\\\
$$

어떤 상태의 가치함수는 샘플링을 통해 에이전트가 그 상태를 방문할 때마다 업데이트하게 된다. 원래 가지고 있던 가치함수 값 $V(s)$에 ${1 \over n}(G(s) - V(s))$를 더함으로써 업데이트 하는 것이다. 이렇게 **시간에 따라 평균을 업데이트 해나가는 것** 을 `이동평균`이라고 하며, 아래는 가치함수의 업데이트 식이다.

$$V(s) \leftarrow V(s) + {1 \over n}(G(s) - V(s))$$

위의 수식에서 $G(s) - V(s)$를 오차라고 하며 ${1 \over n}$을 `스텝사이즈(Step size)`로서 업데이트할 때 오차의 얼마를 가지고 업데이트 할지를 결정한다. 일반적으로 **스텝사이즈** 는 $\alpha$라고 표현하며, 위의 식을 일반적인 형태로 나타내면 아래와 같다. 스텝사이즈가 클수록 과거에 얻은 반환값을 지수적으로 감소시킨다. (환경이 지속적으로 변화한다면 1/n로 평균을 구하는 것보다 일정한 숫자로 고정하는 것이 좋다(?))

$$V(s) \leftarrow V(s) + \alpha (G(s) - V(s))$$

몬테카를로 예측에서 에이전트는 이 업데이트 식을 통해 에피소드 동안 경험했던 모든 상태에 대해 가치함수를 업데이트한다. 어떠한 상태의 가치함수가 업데이트 될수록 가치함수는 **현재 정책에 대한 참 가치함수에 수렴해간다.** 이후의 모든 강화학습 방법에서 가치함수를 업데이트하는 것은 위의 수식의 변형일 뿐이다. 따라서 이 식을 정확하게 이해하는 것이 중요하다.

{% asset_img gridworld.png 300 300 '"그리드월드에서 몬테카를로 예측" "그리드월드에서 몬테카를로 예측"' %}

한 에피소드는 빨간색 선과 같으며, 에이전트는 마침 상태에 갈 때까지 아무것도 하지 않는다. 마침 상태(파란색 동그라미)에 도착하면 에이전트는 지나온 **모든 상태의 가치함수를 업데이트** 한다. 에피소드 동안 방문했던 모든상태의 가치함수를 업데이트하면 에이전트는 다시 시작부터 새로운 에피소드를 진행하며, 이러한 과정을 계속 반복하는 것이 **몬테카를로 예측** 이다.

### 시간차 예측(Temporal-Difference Prediction)

몬테카를로 예측의 단점은 **실시간이 아니라는 점** 이다. 가치함수를 업데이트하기 위해 에피소드가 끝날 때까지 기다려야 한다. 또한 **에피소드의 끝이 없거나, 에피소드의 길이가 긴 경우** 에는 몬테카를로 예측이 적합하지 않다.

`시간차 예측(Temporal-Difference Prediction)`은 몬테카를로 예측과는 다르게 타임스텝마다 가치함수를 업데이트 한다. 사람이 다음 순간을 지속적으로 예측하고 바로 학습하는 것처럼, 에이전트를 실시간으로 예측과 ground-truth 간의 차이로 학습시키기 위한 기법이다.

몬테카를로 예측에서 $G_t$는 에피소드가 끝나야 그 값을 알 수 있었다. 시간차 예측에서는 비슷하지만 $G_t$를 $R_{t+1} + \gamma v_\pi(s')$으로 나타낸 가치함수의 정의인 아래의 식을 이용한다. 다이내믹 프로그래밍처럼 기댓값을 계산하지는 않고 $R_{t+1} + \gamma v_\pi(s')$값을 샘플링해서 그 값으로 현재의 가치함수를 업데이트 한다.

$$v_\pi (s) = E_\pi [R_{t+1}+ \gamma v_\pi (S_{t+1})|S_t = s]$$

가치함수의 업데이트는 실시간으로 이뤄지며, 몬테카를로 예측과는 달리 **한번에 하나의 가치함수만 업데이트** 한다. 에이전트는 현재 가지고 있는 가치함수 리스트에서 다음 상태에 해당하는 가치함수 $V(S_{t+1})$을 가져올 수 있을 것이다. 그러면 바로 $R_{t+1} + \gamma v_\pi(s')$를 계산할 수 있고, 계산된 값은 $S_t$의 가치함수 업데이트의 목표가 된다.

$$V(S_t) \leftarrow V(S_t) + \alpha (R_{t+1} + \gamma V(S_{t+1}) - V(S_t))$$

$R_{t+1} + \gamma V(S_{t+1}) - V(S_t)$는 `시간차 에러(Temporal-Difference Error)`라고 하며 시간차 예측에서 업데이트의 목표는 **반환값** 과는 달리 **실제의 값은 아니다.** $V(S_{t+1})$은 현재 에이전트가 가지고 있는 값이고, 에이전트는 이 값을 $S_{t+1}$의 가치함수일 것이라고 **예측** 할 뿐이다. 다른 상태의 가치함수 예측값을 통해 지금 상태의 가치함수를 예측하는 이러한 방식을 `부트스트랩(Bootstrap)`이라고 한다. 즉, **업데이트 목표도 정확하지 않은 상황에서 가치함수를 업데이트 하는 것이다.**

시간차 예측은 에피소드가 끝날 때까지 기다릴 필요없이 가치함수를 바로 업데이트 할 수 있다. 하지만 이렇게 업데이트를 해도 몬테카를로 예측과 같이 **원래 가치함수 값에 수렴할까?**

**충분히 많은 샘플링을 통해 업데이트** 하면 참 가치함수에 수렴하며 많은 경우 몬테카를로 예측보다 **더 효율적으로 빠른 시간안에 참 가치함수에 근접한다** 고 한다. (이 부분에 대한 디테일한 설명은 나와있지 않다..😭) 하지만 시간차 예측은 몬테 카를로 예측보다 초기 가치함수 값에 따라 **예측 정확도가 많이 달라진다는 단점** 이 존재한다.

다음 포스트에서는 강화학습 알고리즘인 `살사(SARSA)`와 `큐러닝(Q-learning)`을 살펴볼 것이다!
