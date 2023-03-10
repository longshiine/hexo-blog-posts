---
title: <파이썬 알고리즘 인터뷰> - 21장. 그리디 알고리즘
categories:
  - Tech
  - 알고리즘
tags: [GreedyAlgorithm]
date: 2023-01-25 20:50:38
thumbnail: https://user-images.githubusercontent.com/1250095/86745916-a62e9a00-c075-11ea-9aa5-8455e2527f87.png
---

> 도서 파이썬 알고리즘 인터뷰의 내용을 정리했습니다. <br> <a href="https://github.com/onlybooks/algorithm-interview">깃허브 주소</a>

{% asset_img algorithm.png 763 512 '"전체 개념 요약" "전체 개념 요약"' %}

# 21장: 그리디 알고리즘

> 그리디 알고리즘은 글로벌 최적을 찾기 위해 각 단계에서 로컬 최적의 선택을 하는 휴리스틱 문제 해결 알고리즘이다.

- 대부분의 경우에는 뛰어난 결과를 도출하지 못하지만, 드물게 최적해를 보장하는 경우도 있다.
- 그리디 알고리즘은 최적화 문제를 대상으로 한다.
  → 최적해를 찾을 수 있으면 그것을 목표로 삼고, 찾기 어려운 경우에는 주어진 시간 내에 그런대로 괜찮은 해를 찾는 것을 목표로 삼는다.
  - 물론 대부분의 문제들은 이런 로컬 최적해를 찾는 탐욕스런 방법으로는 문제를 해결할 수 없다.
  - 그러나 합리적인 시간 내에 최적에 가까운 답을 찾을 수 있다는 점에서 매우 유용한 알고리즘이기도 하다.
- 그리디 알고리즘이 잘 작동하는 문제들
  - **탐욕 선택 속성을 갖고 있는 최적 부분 구조인 문제**
  - **탐욕 선택 속성:** 앞의 선택이 이후 선택에 영향을 주지 않는 것
  - **최적 부분 구조:** 최적 해결 방법이 부분 문제에 대한 최적 해결 방법으로 구성되는 경우

### 배낭 문제

---

> 배낭 문제는 조합 최적화 분야의 매우 유명한 문제로, 배낭에 담을 수 있는 무게의 최댓값(15kg)이 정해져 있고 각각 짐의 가치(단위)와 무게가 있는 짐(kg 단위)들을 배낭에 넣을 때 가치의 합이 최대가 되도록, 즉 $의 가치가 최대가 되도록 짐을 고르는 방법을 찾는 문제다.

{% asset_img 1.png 400 300 '"분할 가능 배낭 문제" "분할 가능 배낭 문제"' %}

- 짐을 쪼갤 수 있는 경우(분할 가능 배낭 문제) → 그리디 알고리즘
- 짐을 쪼갤 수 없는 경우(0-1 배낭 문제) → 다이나믹 프로그래밍

- **분할 가능 배낭문제 풀이**

  ```python
  # cargo = [(4,12), (2,1), (10,4), (1,1), (2,2)] ($, kg)
  def fractional_knapsack(cargo):
  	capacity = 15
  	pack = []
  	# 단가 계산 역순 정렬
  	for c in cargo:
  			pack.append((c[0]/c[1], c[0], c[1]))
  	pack.sort(reverse=True)

  	# 단가 순 그리디 계산
  	total_value = 0
  	for p in pack:
  		if capacity - p[2] >= 0:
  			capacity -= p[2]
  			total_value += p[1]
  		else:
  			fraction = capacity / p[2]
  			total_value += p[1] * fraction
  			break

  return total_value
  ```

### 동전 바꾸기 문제

---

- 동전의 액면이 10원, 50원, 100원 처럼 증가하면서 이전 액면의 배수 이상이 되면 그리디 알고리즘으로 풀수 있다.
- 160원을 거슬러 주어야 한다면 100원부터 선택해서 50, 10원 순으로 거슬러 주면 된다
  - 그런데 만약 80원 짜리 동전이 있다면 불가능하다. → 80원짜리 2개 → 100원부터 선택하면 안됨
  - 이런 경우는 다이나믹 프로그래밍으로 풀어야 한다.
