---
title: "강화학습 기초: 가치함수와 벨만방정식"
date: 2021-01-22 16:12:02
categories:
  - Tech
  - 강화학습
tags: [ReinforcementLearning, BellmanEquation, ValueFunction]
---

> 본 포스트는 "파이썬과 케라스로 배우는 강화학습" 도서의 세번째 리뷰 포스트입니다.
> http://www.yes24.com/Product/Goods/44136413

## 2장 강화학습 기초 1: 가치함수와 벨만 방정식

---

이제 에이전트가 학습할 수 있도록 문제를 `MDP`로 정의 했다. 에이전트는 `MDP`를 통해 **최적 정책** 을 찾으면 된다. 이제 에이전트가 어떻게 최적 정책을 찾을 수 있을지 보다 구체적으로 알아보도록 하자!
**(❗️수식 주의)**

### 가치함수

에이전트의 입장에서 어떤 행동을 하는 것이 좋은지는 어떻게 알 수 있을까? 앞으로 받을 보상들을 고려해서 선택해야 좋은 선택을 할텐데, 아직 받지 않는 보상들을 어떻게 고려한단 말일까?🧐 **앞으로 받을 보상에 관련된 개념** 이 바로 `가치 함수`이다.

현재 시간 t로부터 에이전트가 행동을 하면서 받을 보상들을 단순히 합한다는 생각을 해볼 수 있다. 그렇게 되면 $R_{t+1}+R_{t+2}+R_{t+3}+R_{t+4}+R_{t+5}+...$와 같은 꼴일 것이다. 그런데 시간에 따른 보상을 이렇게 단순하게 더한다면 세가지 문제가 생긴다.

1. **지금 받은 보상이나 미래에 받는 보상이나 똑같이 취급한다.**
2. **100이라는 보상을 1번 받는 것과 20이라는 보상을 5번 받는 것을 구분할 방법이 없다.**
3. **시간이 무한대라고 하면 보상을 시간마다 0.1씩 받아도 합이 무한대이고 1씩 받아도 합이 무한대이다.**

이러한 문제 때문에 에이전트는 단순한 보상의 합으로는 **시간 t에 있었던 상태가 어떤 가치를 가지는지** 판단하기가 어렵다. 따라서 좀 더 정확하게 상태의 가치를 판단하기 위해 이전 포스트에서 설명한 `할인율`이라는 개념을 사용한다. 할인율을 적용하여 시간 t 이후의 **시간 t시점에서의 보상** 을 모두 더한 것을 `반환값`이라고 하며 $G_t$로 표현한다. 반환값을 수식으로 표현하면 아래와 같다.

$$G_t = R_{t+1}+\gamma R_{t+2}+\gamma^2 R_{t+3}+\gamma^3 R_{t+4}+\gamma^4 R_{t+5}...$$

반환값이라는 것은 에이전트가 실제로 환경을 탐험하며 받은 보상의 합으로, 이 책에서는 에이전트와 환경이 **유한한 시간동안 상호작용** 하는 경우만 다룬다. 이렇게 **유한한 에이전트와 환경의 상호작용** 을 `에피소드`라고 부른다. 에피소드 에서는 에피소드를 끝낼 수 있는 마지막 상태가 있는데, 체스의 경우를 생각한다면 킹을 잃는 순간이 마지막 상태가 된다.

에이전트가 에피소드가 끝난 후에 **'그때로부터 얼마의 보상을 받았지?'** 라며 보상을 정산하는 것이 반환값이다. 만일 에피소드를 t=1 부터 5까지 진행했다면 에피소드가 끝난 후에 방문했던 상태들에 대한 5개의 반환값이 생길 것이다.

$$
G_1 = R_{2}+\gamma R_{3}+\gamma^2 R_{4}+\gamma^3 R_{5}+\gamma^4 R_{6}\\\\
G_2 = R_{3}+\gamma R_{4}+\gamma^2 R_{5}+\gamma^3 R_{6}\\\\
G_3 = R_{4}+\gamma R_{5}+\gamma^2 R_{6}\\\\
G_4 = R_{5}+\gamma R_{6}\\\\
G_5 = R_{6}
$$

MDP로 정의 되는 세계에서 반환값은 에피소드마다 다를 수 있다. 때문에 에이전트는 특정 상태의 가치를 **반환값에 대한 기댓값** 으로 판단해야 한다. 이것이 바로 `가치함수`의 개념이다. 따라서 가치함수는 아래와 같이 표현된다.

$$v(s) = E[G_t|S_t=s]$$

각 타임스텝마다 받는 보상이 모두 확률적이고 반환값이 그 보상들의 합이므로 반환값은 **확률변수** 이다. 하지만 가치함수는 확률변수가 아니라 특정 양을 나타내는 값이므로 **소문자**로 표현한다. 가치함수의 식에 위에서 정의한 반환값의 수식을 대입하고 정리해보면 아래와 같다.

$$
v(s) = E[R_{t+1}+\gamma R_{t+2}+\gamma^2 R_{t+3}...|S_t=s]\\\\
 = E[R_{t+1}+\gamma(R_{t+2}+\gamma R_{t+3}...)|S_t=s]\\\\
 = E[R_{t+1}+\gamma G_{t+1}|S_t=s]
$$

사실 $R_{t+2}+\gamma R_{t+3} ...$ 부분을 반환값의 형태로 표현하긴 했지만 사실 에이전트가 실제로 받은 보상이 아니다. 이 보상은 **앞으로 받을 것이라 예상하는 보상** 으로써 이 부분 또한 가치함수로 표현할 수 있다.

$$v(s) = E[R_{t+1}+\gamma v(S_{t+1})|S_t=s]$$

여기까지는 가치함수를 정의할 때 정책을 고려하지 않았다. 하지만 에이전트가 앞으로 받을 보상에 대해 생각할 때 정책을 고려하지 않으면 안된다. 왜냐하면 상태에서 상태로 넘어갈 때 에이전트는 무조건 행동을 해야 하고 **각 상태에서의 행동을 결정하는 것** 이 에이전트의 정책이기 때문이다.

보상은 어떤 상태에서 어떤 행동을 하는지에 따라 환경에서 에이전트에게 주어진다. 이말은 곧 `MDP`로 정의 되는 문제에서의 가치함수가 행동을 결정하는 `정책`에 의존한다는 것이다. 따라서 아래의 수식처럼 **가치함수에 아래 첨자로 정책을 쓰면** 더 명확한 수식이 된다.

$$v_\pi (s) = E_\pi [R_{t+1}+ \gamma v_\pi (S_{t+1})|S_t = s]$$

이 수식이 바로 강화학습에서 상당히 중요한 `벨만 기대 방정식(Bellman Expectation Equation)`이다.
벨만 기대 방정식은 **현재 상태의 가치함수 $v_\pi (s)$와 다음상태의 가치함수 $v_\pi (S_{t+1})$ 사이의 관계** 를 말해주는 방정식이다.

> 강화학습은 벨만 방정식을 어떻게 풀어가느냐의 스토리이다 🤧

### 큐함수

가치함수는 말 그대로 **"함수"** 이다. 따라서 입력이 무엇이고 출력이 무엇인지 알 필요가 있다. 지금까지 설명한 가치함수는 `상태 가치함수(state value-function)`으로써 **상태** 가 입력으로 들어오면 그 상태에서 **앞으로 받을 보상의 합** 을 출력하는 함수이다. 따라서 에이전트는 가치함수를 통해 어떤 상태에 있는 것이 얼마나 좋은지를 알 수 있다.

상태 가치함수가 각 상태에 대해 가치를 알려주는 것처럼 각 행동에 대해 가치를 알려주는 함수가 있다면 어떨까? 아마 에이전트는 그 함수의 값만 보고 바로 행동을 선택할 수 있을 것이다. **어떤 상태에서 어떤 행동이 얼마나 좋은지** 알려주는 함수를 `큐함수(Q Function)`, 다른말로 행동 가치함수 라고 한다.

{% asset_img qfunction.jpeg 200 200 '"큐함수" "큐함수"' %}

큐함수는 상태, 행동이라는 두가지 변수를 가지며 $q_\pi (s,a)$라고 나타낸다. 또한 가치함수와 큐함수 사이의 관계식은 다음과 같이 표현할 수 있다.

$$v_\pi (s) = \sum_{a \in A} \pi (a|s) q_\pi (s,a)$$

1. 각 행동을 했을 때 앞으로 받을 보상인 큐함수 $q_\pi (s,a)$를 정책 $\pi (a|s)$에 곱한다
2. 모든 행동에 대해 큐함수와 $\pi (a|s)$를 곱한 값을 더하면 가치함수가 된다.

큐함수는 강화학습에서 중요한 역할을 한다. **강화학습에서 에이전트가 행동을 선택하는 기준** 으로 가치 함수 보다는 보통 **큐함수** 를 사용한다. 그 이유는 뒤에서 등장한다! 큐함수 또한 벨만 기대 방정식의 형태로 나타 낼수 있으며 조건문에 행동이 들어간다는 점에서 가치함수의 식과 다르다.

$$q_\pi (s,a) = E_\pi [R_{t+1}+ \gamma q_\pi (S_{t+1}, A_{t+1})|S_t = s, A_t=a]$$

### 벨만 기대 방정식

이제 2장의 메인 디쉬인 `벨만 기대 방정식`을 찬찬히 살펴보자(👂🏻집중). 살짝 정리를 해보면 벨만 **기대** 방정식이라고 하는 이유는 식에 **기댓값의 개념** 이 들어가기 때문이고, 이 벨만 방정식은 **현재 상태의 가치함수와 다음 상태의 가치함수 사이의 관계** 를 식으로 나타낸 것이었다.

벨만 방정식은 강화학습에서 상당히 중요한 부분을 차지한다. 벨만 방정식이 강화학습에서 왜 그렇게 중요한 위치를 차지하고 있는 걸까?🤔 앞에서 정의했던 가치함수의 정의를 다시 한번 살펴보자.

$$v_\pi (s) = E[R_{t+1}+\gamma R_{t+2}+\gamma^2 R_{t+3}...|S_t=s]$$

이 수식으로 부터 기댓값을 알아내려면 앞으로 받을 보상에 대해 고려해야하고, 이는 정의상으로는 가능하지만 **상태가 많아질수록 상당히 비효율적인 방법** 이다. 따라서 컴퓨터가 이 기댓값을 계산하기 위해 다른 조치가 필요하다.

예를 들어 1을 100번 더해야하는 문제가 있다고 해보자. 식 하나로 풀어내는 방법은 아래와 같다

$$1+1+1+...+1 = 100$$
하지만 다른 방법으로 접근해볼 수도 있다. x라는 변수를 지정해 그 값에 1을 계속 더해나가는 것이다.

```python
X = 0
for i in range(100):
  X = X + 1
```

벨만 방정식으로 가치함수를 계산하는 것은 두 번째 방식과 같은 것이다. 한 번에 모든 것을 계산하는 것이 아니라 값을 변수에 저장하고, 루프를 도는 계산을 통해 참 값을 알아나가는 것이다. 즉, **현재 가치함수 값을 업데이트** 해 나가는 것이다. 하지만 업데이트 하려면 기댓값을 계산해야 하는데 기댓값은 어떻게 계산할 수 있을까?

기댓값에는 어떠한 행동을 할 확률(**정책 $\pi (a|s)$**)과 그 행동을 했을 때 어떤 상태로 가게 되는 확률(**상태 변환 확률 $P_{ss'}^a$**)이 포함되어 있다. 따라서 정책과 상태 변환 확률을 포함해서 계산하면 된다.

$$v_\pi (s) = \sum_{a \in A} \pi (a|s) (r_{(s,a)} + \gamma \sum_{s' \in S} P_{ss'}^a v_\pi (s') )$$

예시를 한번 살펴보는 것이 매우 도움이 될 것이다.
{% asset_img bellman.png 500 200 '"벨만 기대 방정식의 예시" "벨만 기대 방정식의 예시"' %}
설명을 용이하게 하기 위해,

1. `상태 변환 확률`($P_{ss'}^a$)은 모두 1이라고 생각하고(왼쪽으로 가기로 결정했다면 1의 확률로 왼쪽으로 간다고 하자)
2. `정책`($\pi (a|s)$)은 무작위로 행동하는 것으로서 각 행동이 25%의 확률로 선택이 되며,
3. `할인율`($\gamma$)은 0.9라고 생각하고,
4. $s'$은 모든 상태일 수 있으나, 에이전트의 행동으로 도착하게 될 상태라고 해보자.
5. 회색 별은 $r(s,a)$에 해당하는 보상 값이다.

위의 가정을 적용하여 식을 바꾸어 본다면,

$$ v*\pi (s) = \sum*{a \in A} 0.25 (r*{(s,a)} + (0.9)v*\pi (s') ) $$

와 같이 바꿀 수 있고, 위의 표와 같이 계산을 할 수 있게 된다.

> 벨만 기대 방정식을 이용해 현재의 가치함수를 계속 업데이트 하다 보면,
> 에이전트가 얻을 실제 보상에 대한 참 기댓값을 얻을 수 있다.

### 벨만 최적 방정식

처음의 가치함수의 값들은 의미가 없는 값으로 초기화 된다. 초기값으로 부터 시작해서 `벨만 기대 방정식`으로 반복적으로 계산한다고 가정해보자. 이 계산을 반복하다보면 방정식의 우항과 좌항이 같아진다(무한히 반복한다는 가정하에). 즉, $v_\pi (s)$값이 수렴하는 것이다. 그렇다면 현재의 정책 $\pi$에 대한 **참 가치함수** 를 구한것이다.

$$v_{k+1}(s) \leftarrow \sum_{a \in A} \pi (a|s)(r_{(s,a)}+ \gamma v_k(s'))$$

벨만 기대 방정식을 기댓값을 계산하기 위해 살짝 변형하면, 현재 정책에 대한 **참 가치함수** 를 구할 수 있다. 위의 식은 뒤에서 배울 dynamic programming에서 자세히 다루도록 한다.

그러나 **참 가치함수** 와 **최적 가치함수(Optimal Value Function)** 은 다르다⭐️. `참 가치함수`는 **어떤 정책을 따라서 움직였을 경우에 받게되는 보상에 대한 참값** 이고, `최적의 가치함수`는 **수많은 정책 중에서 가장 높은 보상을 얻게 되는 정책을 따랐을 때의 가치함수** 이다.

그렇다. 우리는 단순히 현재 정책에 대한 가치함수를 구하고 싶은게 아닌, **최적 정책**을 찾고 싶은 것이다. 단순히 현 정책에 대한 가치함수를 찾는 것이 아닌 더 좋은 정책으로 현재의 정책을 업데이트 해 나가야 한다. 이쯤에서 이런 질문이 들어야 한다.

- 더 좋은 정책이라는 것의 정의는 무엇인가?
- 어떤 정책이 더 좋은 정책이라고 판단할 수 있을까?

더 좋은 정책이란 정책을 따라갔을 때 받을 보상들의 합이 더 큰 경우라고 말할 수 있을 것이다. 그리고 그것은 **가치함수** 를 통해 판단할 수 있었다. 결국 가치함수가 정책이 얼마나 좋은지를 말해주는 것이다. 따라서 **모든 정책 중, 가장 큰 가치함수를 갖는 정책이 최적 정책이다.** 최적 큐함수 또한 같은 방식으로 생각한다.

$$
v^*(s) = max_{\pi}[v_\pi (s)]\\\\
q^*(s,a) = max_{\pi}[q_\pi (s,a)]
$$

가장 높은 가치함수(큐함수)를 에이전트가 찾았다고 가정해보자. 이때 **최적 정책** 은 각 상태 s에서의 최적의 큐함수에 대해 가장 큰 값을 가진 행동을 하는 것이다. 즉, 선택 상황에서 판단 기준은 큐함수이며, 최적 정책은 최적 큐함수 $q^*$만 안다면 아래와 같이 구할 수 있다.

$$
\pi^*(s,a) = \begin{cases}
  \displaystyle 1, \\\;if \\\; a = argmax_{a \in A} \\\;q^*(s,a) \\\\
  \displaystyle 0, \\\;otherwise
\end{cases}
$$

그렇다면 최적의 큐함수는 어떻게 구할 수 있을까?
그것을 구하는 것이 **순차적 행동 결정 문제(MDP)** 를 푸는 것이다. 어떻게 최적의 가치함수를 구하는지에 대해서는 다음 장에서 다룬다. 여기서는 최적의 가치함수 끼리 관계가 어떻게 되는지를 살펴보자.

현재 상태의 가치함수가 최적이라고 가정해보자. 현재상태의 가치함수가 최적이라는 것은 에이전트가 가장 좋은 행동을 선택한다는 것이다. 이때 선택의 기준이 되는 큐함수는 최적의 큐함수 이어야 하고, 따라서 다음이 최적의 가치함수의 식이 된다.
$$v^*(s) = max_{a}[q^*(s,a) | S_t=s, A_t=a]$$

여기서 큐함수를 가치함수로 고쳐서 표현하면 아래와 같다.

$$v^*(s) = max_{a}E[R_{t+1} + \gamma v^*(S_{t+1}) | S_t=s, A_t=a]$$

바로 이 식을 `벨만 최적 방정식(Bellman Optimality Equation)` 이라 부르며, 이식은 최적의 가치함수에 대한 것이다. 큐함수에 대해서도 벨만 최적 방정식을 표현할 수 있는데, 아래와 같이 표현한다.

$$q^*(s,a) = E[R_{t+1} + \gamma max_{a'}q^*(S_{t+1}, a') | S_t=s, A_t=a]$$

기댓값인 이유는 다음 상태가 상태 변환 확률에 따라 달라지기 때문이다. 벨만 기대 방정식과 벨만 최적 방정식을 이용해 MDP로 정의되는 문제를 **계산** 으로 푸는 방법이 바로 다음장에서 다룰 `다이내믹 프로그래밍(Dynamic programming)`이다.

### 정리

1. `MDP`: 순차적 행동 결정 문제를 수학적으로 정의하는 것. 상태($S_t$), 행동($A_t$), 보상함수($r(s,a)$), 상태 변환 확률($P_{ss'}^a$), 할인율($\gamma$)로 구성.
2. `가치함수`: 현재 상태로부터 정책을 따라갔을 때 받을 것이라 예상되는 보상의 합.
   $$v_\pi (s) = E_\pi [R_{t+1}+ \gamma v_\pi (S_{t+1})|S_t = s]$$
3. `큐함수`: 각 행동에 대해 가치를 알려주는 함수, 정책 업데이트 시에 사용
   $$q_\pi (s,a) = E_\pi [R_{t+1}+ \gamma q_\pi (S_{t+1}, A_{t+1})|S_t = s, A_t=a]$$
4. `벨만 기대 방정식`: 현재 상태의 가치함수와 다음 상태 가치함수의 관계식
   $$v_\pi (s) = E_\pi [R_{t+1}+ \gamma v_\pi (S_{t+1})|S_t = s]$$
5. `벨만 최적 방정식`: 최적의 정책을 따르는 가치함수와 다음 상태 가치함수의 관계식
   $$v^*(s) = max_{a}E[R_{t+1} + \gamma v^*(S_{t+1}) | S_t=s, A_t=a]$$

### 2장 한줄평

> 최적 방정식 부분을 좀 더 명확하게 다시 정리해 봐야겠다. 🤥