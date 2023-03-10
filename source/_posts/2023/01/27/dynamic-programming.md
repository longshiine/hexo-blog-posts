---
title: <파이썬 알고리즘 인터뷰> - 23장. 다이나믹 프로그래밍
categories:
  - Tech
  - 알고리즘
tags: [DynamicProgramming]
date: 2023-01-27 20:50:38
thumbnail: https://user-images.githubusercontent.com/1250095/86745916-a62e9a00-c075-11ea-9aa5-8455e2527f87.png
---

> 도서 파이썬 알고리즘 인터뷰의 내용을 정리했습니다. <br> <a href="https://github.com/onlybooks/algorithm-interview">깃허브 주소</a>

{% asset_img algorithm.png 763 512 '"전체 개념 요약" "전체 개념 요약"' %}

# 23장: 다이나믹 프로그래밍

> 다이나믹 프로그래밍은 문제를 각각의 작은 문제로 나누어 해결한 결과를 저장해뒀다가 나중에 큰문제의 결과와 합하여 풀이하는 알고리즘이다.

{% asset_img 1.png 400 300 '"문제들의 특징" "문제들의 특징"' %}

- 다익스트라 알고리즘은 **최적 부분 구조, 중복된 하위 문제들, 탐욕 선택 속성**을 모두 갖는 알고리즘이다.
  - **보통 다이나믹 프로그래밍은 이전의 결과값을 활용해야 하고, 그리디 알고리즘은 그 상태만에서의 최적 선택을 한다**
  - 분할 정복의 경우에는 하위 문제 및 결과 값이 다를 수 있으므로, 다이나믹 프로그래밍에 해당하지 않는다.
- 대표적으로 피보나치 수열 문제 f(3)=f(2)+f(1), f(4)=f(3)+f(2)+f(1) 이 다이나믹 프로그래밍이다.

### 상향식

---

- 작은 하위 문제부터 차례로 정답을 풀어나가며 큰 문제의 정답을 만든다.
- 이 방식을 타뷸레이션(Tabulation)이라 하며, 이 방식만을 다이나믹 프로그래밍이라 지칭하는 경우도 있다.
- 타뷸레이션 = 데이터를 테이블 형태로 만들면서 문제를 풀이한다

```python
def fib(n):
	dp[0] = 0
	dp[1] = 1

	for i in range(2, n+1):
		dp[i] = dp[i-1] + dp[i-2]

	return dp[n]
```

### 하향식

---

- 하위 문제에 대한 정답을 계산했는지 확인해가며, 자연스럽게 재귀로 풀어나간다.
- 기존 재귀 풀이와 거의 동일하면서도, 이미 풀이했는지 확인하여 재확인하는 효율적인 방식으로 메모이제이션(Memoization) 방식이라고도 부른다.

```python
def fib(n):
	if n <= 1:
		return n

	if dp[n]:
		return dp[n]

	dp[n] = fib(n-1)+fib(n-2)
	return dp[n]
```
