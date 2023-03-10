---
title: <파이썬 알고리즘 인터뷰> - 4장. 빅오, 자료형
categories:
  - Tech
  - 알고리즘
tags: [Big-O, DataType]
date: 2023-01-11 20:55:38
thumbnail: https://user-images.githubusercontent.com/1250095/86745916-a62e9a00-c075-11ea-9aa5-8455e2527f87.png
---

> 도서 파이썬 알고리즘 인터뷰의 내용을 정리했습니다. <br> <a href="https://github.com/onlybooks/algorithm-interview">깃허브 주소</a>

{% asset_img algorithm.png 763 512 '"전체 개념 요약" "전체 개념 요약"' %}

# 4장: 빅오, 자료형

빅오(big-O)는 입력값이 커질 때 알고리즘의 실행 시간(시간 복잡도)과 함께 공간 요구사항(공간 복잡도)이 어떻게 증가하는지를 분류하는 데 사용되며, 알고리즘의 효율성을 분석하는 데에도 매우 유용하게 활용된다.

### 1. 빅오

> 빅오(O, big-O)란 입력값이 무한대로 향할때 함수의 상한을 설명하는 수학적 표기 방법이다.

- O(1): 입력값이 아무리 커도 실행 시간은 일정하다
  - 해시 테이블의 조회 및 삽입
- O(log n): 입력 값에 크게 영향을 받지 않는 편
  - 이진 검색
- O(n): 입력값에 실행시간이 비례, 선형시간 알고리즘
  - 정렬되지 않은 리스트에서 최댓값, 최솟값을 찾는 경우
- O(nlogn): 병합 정렬 등 효율 좋은 정렬 알고리즘이 해당한다.
  - 병합 정렬, 퀵 정렬 등
- O(n^2): 버블 정렬 같은 비효율적인 정렬 알고리즘
  - 버블 정렬, 삽입 정렬 등
- O(2^n): 피보나치 수를 재귀로 계산하는 알고리즘 등
- O(n!): 가장 느린 알고리즘

  - 각 도시를 방문하고 돌아오는 가장 짧은 경로를 찾는 외판원 문제를 브루트 포스로 풀이할 때

- 시간 복잡도와 공간 복잡도는 대체로 트레이드-오프 관계다

### 2. 상한과 최악

- 빅오(O)는 상한을 의미한다.
- 이외에도 하안을 나타내는 빅 오메가(W), 평균을 의미하는 빅세타가 있는데 업계에서는 주로 빅오로 표현한다.

### 3. 자료형

{% asset_img datatype.jpeg 320 400 '"파이썬 자료형" "파이썬 자료형"' %}

- Set

  ```python
  >>> a = {'a', 'b', 'c'}
  >>> type(a)
  <class 'set'>

  >>> a = {'a': 'A', 'b': 'B', 'c': 'C'}
  >>> type(a)
  <class 'dict'>
  ```
