---
title: "강화학습 기초: 정책 이터레이션, 가치 이터레이션"
date: 2021-01-24 13:00:23
categories:
  - Tech
  - 강화학습
tags: [ReinforcementLearning, DeepLearning, PolicyIteration, ValueIteration]
mathjax: true
---

> 본 포스트는 "파이썬과 케라스로 배우는 강화학습" 도서의 다섯번째 리뷰 포스트입니다.
> http://www.yes24.com/Product/Goods/44136413

## 3장 강화학습 기초 2: 그리드월드와 다이내믹 프로그래밍

### 정책 이터레이션 (Policy Iteration)

결국 MDP로 정의되는 문제에서 알고 싶은 것은 **가장 높은 보상을 얻게 하는 정책** 을 찾는 것이다. 하지만 처음에는 이 정책을 알 수가 없다. 보통 처음에는 무작위로 행동을 정하는 정책으로부터 시작하여 계속 발전시켜 나간다.

하지만 우리가 얻고자 하는 최적 정책은 무작위정책(random policy)이 아니다. 그렇다면 현재의 정책을 **평가** 하고 더 나은 정책으로 **발전** 해야한다. 어떤 정책이 있을 때 `정책평가(Policy Evaluation)`를 통해 얼마나 좋은지 평가하고, 그 평가를 기준으로 `정책발전(Policy Improvement)`을 통해 좀 더 나은 정책으로 발전 시킨다. 이러한 과정을 무한히 반복하면 **정책은 최적 정책으로 수렴** 한다.

**안선생의 안소리🤬 예상** : 무작위 정책에서 출발해서 도대체 어떻게 최적 정책에 수렴하지요? 자세히 설명해보세요!
--> https://www.youtube.com/watch?v=rrTxOkbHj-M&t=19s 팡요랩 영상의 18분 즈음부터 참고
(정리해보자면, 주위의 상태의 값들은 쓰레기 값이지만 **보상** 과 같은 작은 정보들을 토대로 점진적으로 업데이트 되어 최적에 다가가게 되는 것이다. 정수기에서 물이 정수되는 것처럼 말이다! 사실 아직도 업데이트가 되는것이 신기하다)

{% asset_img policy.png 400 250 '"정책평가와 정책발전" "정책평가와 정책발전"' %}

### 정책 평가 (Policy Evaluation)

가치함수는 정책이 얼마나 좋은지 판단하는 근거가 된다. 가치함수는 **현재 정책 $\pi$를 따라갔을 때 받을 보상에 대한 기댓값**⭐️ 이다. 에이전트의 목표는 어떻게 하면 보상을 많이 받을 수 있을지를 알아내는 것이므로 현재 정책에 따라 받을 보상에 대한 정보가 정책의 가치가 되는 것이다.

$$ v*\pi (s) = E[R*{t+1}+\gamma G\_{t+1}|S_t=s]$$

정책 이터레이션에서는 위의 벨만 기대 방정식을 사용하여 문제를 풀 것이다. 핵심은 **주변 상태의 가치함수와 한 타임스텝의 보상만 고려해서 현재 상태의 다음 가치함수를 계산** 하겠다는 것이다. 이 과정은 한 타임스텝의 보상만 고려하고 주변 상태의 가치함수들이 참 가치함수들이 아니기 때문에 이렇게 계산해도 이 값은 실제값이 아니다. 하지만 이러한 계산을 **여러번 반복** 한다면 **참 값으로 수렴** 한다는 것이 메인 아이디어이다.

이전 포스트에서 위의 식을 그리드월드 예제에서 계산 가능한 형태로 변환해 보았었다. 아래는 k번째 가치함수를 통해 k+1번째 가치함수를 계산하는 식이다. 우리는 아래의 식을 반복적으로 계산해 나갈 것이다.

$$v_{k+1}(s) = \sum_{a \in A} \pi (a|s)(r_{(s,a)}+ \gamma v_k(s'))$$

#### 한 번의 정책 평가 과정을 순서대로 나타내면 다음과 같다.

1. $k$번째 가치함수 matrix에서 현재 상태 $s$에서 갈 수 있는 다음상태 $s'$에 저장돼 있는 가치함수 $v_k(s')$을 불러온다.
   (보라색 부분중의 하나)
2. $v_k(s')$에 할인율 $\gamma$를 곱하고 그 상태로 가는 행동에 대한 보상 $R_s^a$을 더한다.
   $$r_{(s,a)} + \gamma v_k(s')$$
3. 2번에서 구한 값에 그 행동을 할 확률, 즉 정책 값을 곱한다.
   $$\pi (a|s)(r_{(s,a)} + \gamma v_k(s'))$$
4. 3번을 모든 선택 가능한 행동에 대해 반복하고 그 값들을 더한다.
   $$v_{k+1}(s) = \sum_{a \in A} \pi (a|s)(r_{(s,a)}+ \gamma v_k(s'))$$
5. 4번 과정을 통해 더한 값을 $k+1$번째 가치함수 matrix의 상태 $s$자리에 저장한다.
6. 1-5 과정을 모든 $s \in S$에 대해 반복한다.

{% asset_img policyIteration.png 400 200 '"policy evaluation" "policy evaluation"' %}

이것은 한번의 정책평가 과정이다. 하지만 한번의 정책평가로서는 제대로 평가를 할 수 없어, 이과정을 여러번 반복하는데, $v_1$으로 시작해서 무한히 반복하면 참 $v_\pi$가 될 수 있다.

### 정책 발전 (Policy Improvement)

애초에 정책을 발전 시키지 않는다면 정책에 대한 평가는 의미가 없다. 그렇다면 정책 평가를 바탕으로 어떻게 정책을 발전시킬 수 있을까? 사실 정책 발전의 방법이 정해져있는 것은 아니다. 하지만 이 책에서는 가장 널리 알려진 `탐욕 정책 발전(Greedy Policy Improvement)`을 사용한다.

에이전트가 해야할 일은 단순하다. 상태 s에서 선택 가능한 행동의 $q_\pi (s,a)$ 즉 큐함수 값을 비교하고 그중에서 가장 큰 값을 가지는 행동을 선택하면 된다. 이것을 **탐욕 정책 발전** 이라고 하는데, 눈앞에 보이는 것 중에서 **당장에 가장 큰 이익을 추구하는 것과 같은 모습** 이기 때문에 이러한 이름이 붙었다.

탐욕 정책 발전을 통해 업데이트된 정책은 아래와 같다. max 함수와는 다르게 반환되는 것이 **행동** 이다.
$$\pi'(s) = argmax_{a \in A} \\\; q_\pi (s,a)$$

{% asset_img greedy.png 400 200 '"Greedy Policy Improvement" "Greedy Policy Improvement"' %}

탐욕 정책 발전을 통해 정책을 업데이트하면 이전 가치함수에 비해 업데이트된 정책으로 움직였을 때 받을 가치함수가 **무조건 크거나 같다.** 다이타믹 프로그래밍에서는 이처럼 탐욕 정책 발전을 사용하여 가장 큰값의 가치함수를 가지는 최적 정책에 수렴할 수 있다.

### 정책 이터레이션 코드

우선 에이전트가 해야할 역할을 고려해서 전체 코드의 흐름을 보면 다음과 같다.

```python
class PolicyIteration:
  def __init__(self, env):
    # 환경에 대한 객체
    self.env = env

  # 정책 평가
  def policy_evaluation(self):
    pass

  # 정책 발전
  def policy_improvement(self):
    pass

  # 특정 상태에서 정책에 따른 행동
  def get_action(self, state):
    return action

if __name__ == "__main__":
  env = Env()
  policy_iteration = PolicyIteration(env)
  grid_world = GraphicDisplay(policy_iteration)
  grid_world.mainloop()

```

에이전트가 알고 있는 환경(env)의 정보는 다음과 같다.

- `env.width, env.height`: 그리드월드의 너비와 높이
- `env.state_after_aciton(state, action)`: 특정 상태에서 특정 행동을 했을 때 에이전트가 가는 다음 상태
- `env.get_all_states()`: 존재하는 모든 상태
- `env.get_reward(state, action)`: 특정 상태의 보상
- `env.possible_actions`: 상,하,좌,우

정책 이터레이션은 **정책 평가** 와 **정책 발전** 으로 이뤄져 있다. 따라서 각각의 함수를 정의 한다.

#### policy_evaluation

정책 평가를 통해 에이전트는 모든 상태의 가치함수를 업데이트 한다. 모든 상태에 대해 **벨반 기대 방정식** 의 계산이 끝나면 현재의 value_table에 next_value_table을 덮어쓰는 식으로 policy_evalutation을 진행한다.

$$v_{k+1}(s) = \sum_{a \in A} \pi (a|s)(r_{(s,a)}+ \gamma v_k(s'))$$

정책 평가에 사용되는 벨만 기대 방정식이다. 이때, **상태 변환 확률** 을 1이라고 설정 했기 때문에, 다음상태 $s'$은 만약 행동이 왼쪽일 경우 왼쪽에 있는 상태가 된다.

```python
# 벨만 기대 방정식을 통해 다음 가치함수를 계산하는 정책 평가
def policy_evaluation(self):
    # 다음 가치함수 초기화
    next_value_table = [[0.00] * self.env.width
                        for _ in range(self.env.height)]

    # 모든 상태에 대해서 벨만 기대방정식을 계산
    for state in self.env.get_all_states():
        value = 0.0
        # 마침 상태의 가치 함수 = 0
        if state == [2, 2]:
            next_value_table[state[0]][state[1]] = value
            continue

        # 벨만 기대 방정식
        for action in self.env.possible_actions:
            next_state = self.env.state_after_action(state, action)
            reward = self.env.get_reward(state, action)
            next_value = self.get_value(next_state)
            value += (self.get_policy(state)[action] *
                      (reward + self.discount_factor * next_value))

        next_value_table[state[0]][state[1]] = value

    self.value_table = next_value_table
```

- $\sum_{a \in A}$: `for action in self.env.possible_actions:`
- $\pi (a|s)$: `self.get_policy(state)[action]`
- $r(s,a)$: `reward = self.env.get_reward(state, action)`
- $\gamma$: `self.discount_factor` (0.9)
- $s'$: `next_state = self.env.state_after_action(state, action)`
- $v_k(s')$: `self.get_value(next_state)`

- $v_{k+1}(s)$: `next_value_table[state[0]][state[1]]`

get_policy 함수를 통해 각 상태에서 각 행동에 대한 확률값을 구한다. 그리고 다음 상태로 갔을 때 받을 보상과 다음상태의 가치함수를 할인율을 적용하여 더한다. 정책이 각 행동에 대한 확률을 나타내기 때문에 모든 행동에 대해 value를 계산하고 더하면 기댓값을 계산한 것이 된다.

#### policy_improvement

정책 평가를 통해 정책을 평가하면 그에 따른 새로운 가치함수를 얻는다. 에이전트는 이제 새로운 가치함수를 통해 정책을 업데이트 해야한다. 정책발전에는 탐욕 정책 발전을 사용한다.

```python
# 현재 가치 함수에 대해서 탐욕 정책 발전
def policy_improvement(self):
    next_policy = self.policy_table
    for state in self.env.get_all_states():
        if state == [2, 2]:
            continue

        value_list = []
        # 반환할 정책 초기화
        result = [0.0, 0.0, 0.0, 0.0]

        # 모든 행동에 대해서 [보상 + (할인율 * 다음 상태 가치함수)] 계산
        for index, action in enumerate(self.env.possible_actions):
            next_state = self.env.state_after_action(state, action)
            reward = self.env.get_reward(state, action)
            next_value = self.get_value(next_state)
            value = reward + self.discount_factor * next_value
            value_list.append(value)

        # 받을 보상이 최대인 행동들에 대해 탐욕 정책 발전
        max_idx_list = np.argwhere(value_list == np.amax(value_list))
        max_idx_list = max_idx_list.flatten().tolist()
        prob = 1 / len(max_idx_list)

        for idx in max_idx_list:
            result[idx] = prob

        next_policy[state[0]][state[1]] = result

    self.policy_table = next_policy
```

탐욕 정책 발전은 가치가 가장 높은 하나의 행동을 선택하는 것이다. 하지만 이 예제에서와 같이 현재 상태에서 가장 좋은 행동이 여러개일 수도 있다. 그럴때에는 가장 좋은 행동들을 동일한 확률로 선택하는 정책으로 업데이트한다.

- 현재 상태에서 가능한 행동에 대해 $r_{(s,a)}+\gamma v_k(s')$을 계산한다.
- 계산한 값을 value_list에 저장한다.
- max 함수를 통해 value_list에 담긴 값 중 가장 큰 값을 알아낸다.
- argwhere를 통해 가장 큰 값의 index를 알아내고(여러개면 여러개 반환) max_idx_list에 저장한다.
- max_idx_list의 길이를 바탕으로 확률을 계산하여 확률값을 계산하고 result에 저장한다.

이렇게 되면 policy_table에는 업데이트 된 정책(각 상태의 행동에 대한 확률)이 저장된다.

> 정책 이터레이션에서 에이전트는 정책 평가와 정책 발전을 반복하여 최적 정책을 찾아낸다.

{% asset_img optimalpolicy.png 300 300 '"정책 이터레이션으로 구한 최적 정책" "정책 이터레이션으로 구한 최적 정책"' %}

### 가치 이터레이션

정책 이터레이션과 가치 이터레이션의 중요한 차이점은 정책 이터레이션에서는 **정책 평가와 정책 발전** 으로 단계가 나누어져 있다면 가치 이터레이션에서는 그렇지 않다는 것이다.

가치 이터레이션은 현재의 가치함수가 최적 정책에 대한 가치함수라고 가정하기 때문에 정책을 발전하는 함수가 따로 필요하지 않다. 따라서 최적 행동을 반환하는 get_action 함수를 정책을 출력하는 데 대신 사용한다.

주요 코드의 전체적인 흐름은 다음과 같다.

```python
class ValueIteration:
  def __init__(self, env):
    # 환경 객체 생성
    self.env = env

  # 벨만 최적 방정식을 통해 다음 가치함수 계산
  def value_iteration(self):
    return

  # 현재 가치함수로부터 행동을 반환
  def get_action(self, state):
    return

  def get_value(self, state):
    return

if __name__ == "__main__":
  env = Env()
  value_iteration = ValueIteration(env)
  grid_world = GraphicDisplay(value_iteration)
  gird_world.mainloop()
```

정책 이터레이션에서는 policy_evaluation 함수에서 벨만 기대 방정식을 통해 다음 가치함수를 계산했다. 가치 이터레이션에서는 value_iteration 함수를 통해 다음 가치함수를 계산한다.

```python
# 벨만 최적 방정식을 통해 다음 가치 함수 계산
def value_iteration(self):
    # 다음 가치함수 초기화
    next_value_table = [[0.0] * self.env.width
                        for _ in range(self.env.height)]

    # 모든 상태에 대해서 벨만 최적방정식을 계산
    for state in self.env.get_all_states():
        # 마침 상태의 가치 함수 = 0
        if state == [2, 2]:
            next_value_table[state[0]][state[1]] = 0.0
            continue

        # 벨만 최적 방정식
        value_list = []
        for action in self.env.possible_actions:
            next_state = self.env.state_after_action(state, action)
            reward = self.env.get_reward(state, action)
            next_value = self.get_value(next_state)
            value_list.append((reward + self.discount_factor * next_value))

        # 최댓값을 다음 가치 함수로 대입
        next_value_table[state[0]][state[1]] = max(value_list)

    self.value_table = next_value_table
```

$$v_{k+1}(s) = max_{a \in A}(r_{s,a} + \gamma v_k(s'))$$

- 위의 벨만 최적 방정식을 계산하여 value_list에 저장한다.
- value_list에 저장된 값중 최대의 값을 새로운 가치함수로 저장한다.

```python
# 현재 가치 함수로부터 행동을 반환
def get_action(self, state):
    if state == [2, 2]:
        return []

    # 모든 행동에 대해 큐함수 (보상 + (감가율 * 다음 상태 가치함수))를 계산
    value_list = []
    for action in self.env.possible_actions:
        next_state = self.env.state_after_action(state, action)
        reward = self.env.get_reward(state, action)
        next_value = self.get_value(next_state)
        value = (reward + self.discount_factor * next_value)
        value_list.append(value)

    # 최대 큐 함수를 가진 행동(복수일 경우 여러 개)을 반환
    max_idx_list = np.argwhere(value_list == np.amax(value_list))
    action_list = max_idx_list.flatten().tolist()
    return action_list
```

- 모든 행동에 대해 큐함수를 구한다. $r_{s,a} + \gamma v_k(s')$
- 그 중 가장 큰 value 값을 가지는 행동의 인덱스를 가져온다(여러개라면 여러개 모두 가져온다)
- 행동들을 모두 action_list에 저장한다.

이 예제에 대해서는 Calculate 6번 정도에 가치함수가 거의 수렴하게 된다. 이때 수렴한 가치함수의 값을 토대로 정책을 출력해보면 아래의 그림과 같다.

{% asset_img valueiteration.png 300 300 '"가치 이터레이션으로 구한 최적 정책" "가치 이터레이션으로 구한 최적 정책"' %}

### 다이내믹 프로그래밍의 한계와 강화학습

벨만 방정식을 이용한 다이내믹 프로그래밍으로서 정책 이터레이션과 가치 이터레이션을 살펴봤다. 하지만 다이내믹 프로그래밍은 **계산을 빠르게 하는 것이지 학습을 하는 것** 은 아니다. 그렇다면 이러한 다이내믹 프로그래밍의 한계는 무엇일까?

**1. 계산복잡도** : 문제의 규모가 커지만 계산으로 푸는데에 한계. `계산복잡도` = 상태 크기의 3제곱
**2. 차원의 저주** : 그리드월드의 상태의 차원은 2차원. 상태의 차원이 늘어나면 상태의 수가 지수적으로 증가
**3. 환경에 대한 완벽한 정보가 필요** : 보상과 상태 변환 확률을 정확히 안다는 가정 필요. 보통은 이 정보를 확실히 알 수 없음.

현실세계의 환경에 놓인 문제를 풀어내는 데에는 위의 세 가지 한계가 치명적으로 작용한다. 때문에 이러한 한계를 극복하기 위해 환경을 모르지만 **환경과의 상호작용을 통해 경험을 바탕으로 학습하는 방법** 이 등장한다. 바로 `강화학습`이다.

### 모델없이 학습하는 강화학습

환경의 모델이란 무엇일까? MDP에서 환경의 모델은 상태 변환 확률과 보상이다.

$$Model = P_{ss'}^a, \\\; r(s,a)$$

현재 이 책에서 다루고 싶은 모델은 **수학적 모델** 로서 시스템에 입력이 들어왔을 때 시스템이 어떤 출력을 내는지에 대한 방정식이다. 이처럼 입력과 출력의 관계를 식으로 나타내는 과정을 `모델링(Modeling)`이라고 한다.

사실 입력과 출력 사이의 방정식은 정확할 수가 없다. 방정식에서는 A라는 입력이 들어와서 B라는 출력이 나오더라도 실제 세상에서는 B라는 출력이 절대로 나오지 않는다. 모델은 정확하면 정확할수록 복잡하며 공기나 바람같은 **자연현상을 정확하게 모델링하는 것은 불가능** 에 가깝다. 게임에서는 사실 사람이 환경을 만들었고, 사람이 정해준대로만 게임이 작동하므로 **모델링 오차는 없다** 고 볼 수 있다. 하지만 게임을 벗어난다면 글쎄...🤔

모델을 정확히 알기 어려운 경우, 시스템의 입력과 출력 사이의 관계를 알기 위해 두가지 접근 방법으로 접근해 볼 수 있다.

1. 할 수 있는 선에서 정확한 모델링을 한 다음, 모델링 오차에 대한 부분을 실험을 통해 조정한다.
2. 모델 없이 환경과의 상호작용을 통해 입력과 출력 사이의 관계를 학습한다.

1번은 학습의 개념없이 고전적으로 많이 적용하는 방법이며 시스템의 안정성을 보장한다. 하지만 문제가 복잡해지고 어려워질수록 한계가 있다.

2번은 학습의 개념이 들어간다. 학습의 특성상 모든 상황에서 동일하게 작동한다고 보장할 수 없지만 많은 복잡한 문제에서 모델이 필요없는 것은 장점이다. 2번 방법이 바로 이 책의 주제인 `강화학습` 이다.

### 정리

- `정책 이터레이션`: 벨만 기대 방정식을 이용해 정책을 평가하고, 탐욕 정책 발전을 이용해 정책 발전
- `가치 이터레이션`: 최적 정책을 가정하고 벨만 최적 방정식 이용. 정책이 직접적으로 주어지지 않아 큐함수를 통해 행동 선택
- `다이내믹 프로그래밍의 한계`: 계산 복잡도, 차원의 저주, 환경에 대한 완벽한 정보 필요

### 3장 한줄평

> 짧고 간결하게 쓰는게 오히려 힘들다..
