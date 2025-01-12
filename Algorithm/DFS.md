# 그래프 탐색 알고리즘

## DFS

- 한 정점에서 시작
- 현재 정점과 인접한 정점 중 아직 방문하지 않은 정점을 하나 선택해 방문
- 만약 인접한 정점을 모두 방문한 상태라면 이전 정점으로 복귀
  - 스택이나 재귀를 이용해서 구현할 수 있음
  - 코테(PS)에서는 보통 재귀를 사용해서 구현함
  - 스택을 사용하는 구현은 함수 호출 스택을 직접 구현
- 보통은 인접 리스트(c++의 vector)를 이용
  ![image](https://github.com/user-attachments/assets/c148ba70-dfcd-45ba-89bd-8a5513d56b4e)

- 위와 같은 방식으로 구현
- C배열은 방문 체크(check)용 배열

## DFS 와 백트래킹

- 둘 다 재귀함수를 이용해서 구현할 수 있음
- DFS는 그래프의 모든 정점을 방문해봐야하는 경우 사용
- 백트래킹은 봐야하는 부분만 보는 경우 주로 사용

## 예시 문제

![image](https://github.com/user-attachments/assets/a1753b84-5aed-4638-b4b7-5928f7e11360)

- 이분 그래프는 1학년 컴퓨터수학2과목에 다루기 때문에 생략(컴학기준)
- https://ko.wikipedia.org/wiki/%EC%9D%B4%EB%B6%84_%EA%B7%B8%EB%9E%98%ED%94%84
- 혹은 위의 링크 참조
- 요점은, 인접한 노드의 색이 달라야함
- 그래서 단순히 방문을 하는게 아니라, 색깔을 주면서 방문처리를 하면됨
  ![[Pasted image 20250105211437.png]]

## 추천 문제

https://www.acmicpc.net/problem/1260 : DFS와 BFS
https://www.acmicpc.net/problem/2644 : 촌수계산
https://www.acmicpc.net/problem/1707 : 이분 그래프
