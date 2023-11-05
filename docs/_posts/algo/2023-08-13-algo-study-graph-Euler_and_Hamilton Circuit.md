---
title: 그래프 탐색 - 오일러 탐색와 해밀턴 탐색
date: 2023-08-13 08:34
category: algorithm
---

오일러 탐색 해밀턴 탐색은 그래프 탐색 알고리즘이다.

관점을 점에 두느냐, 간선에 두느냐의 기준으로 선택해 사용하게 된다.

> 오일러 : 모든 **간선**을 탐색한다.
> 해밀턴 : 모든 **정점**을 한번 씩 탐색한다.
> [wiki](https://ko.wikipedia.org/wiki/%ED%95%B4%EB%B0%80%ED%84%B4_%EA%B2%BD%EB%A1%9C)

## 오일러 탐색

오일러 회로는 한붓 그리기 경로를 생성하는 알고리즘이다.

오일러 탐색 경로가 존재할 수 있는 그래프의 기준이 있는데, 찾으려는게 회로인지 경로인지에 따라 조건이 다르다.

> 회로 : 모든 정점을 방문했을 때 시작점과 끝점이 같은 경로
> 경로 : 모든 정점을 방문

### 오일러 회로

각 정점에서 이동가능한 간선의 개수는 차수 (degree)라고 표현한다.

모든 간선을 탐색했을 때 시작점으로 돌아올 수 있는 그래프의 기준은 다음과 같다.

> 모든 정점의 차수가 짝수이다.

### 오일러 경로

모든 간선을 탐색할 수 있는 기준은 다음과 같다.

> 모든 정점의 차수가 짝수이거나, **홀수 차수인 정점이 2개다.**


### 소스코드

오일러 탐색을 구현하려면 정점 A와 B가 연결 되었는지 여부를 확인할 수 있는 자료형이 필요하다.

아래에서는 2차원 in 배열로 구현하였다.

메모리 공간을 효율적을 사용하려면 컨테이너 구현하는 방법도 있지만 인덱스를 추가로 저장해줘야 하는 번거로움이 있다.

```c++
# Euler.h
class Euler {
public :
	Euler();
	int N;									// 정점 개수
	vector<pair<int, int>> edges;			// 간선 리스트
	void Explore(bool type);

private :
	int conArr[501][501];					// 정점 인접 행렬

	vector<int> printData;					// 출력 용

	bool ExploreLogic_Backtracking(int num, int idx);
};
```


```c++
# Euler.cpp
Euler::Euler() {
	N = 4;
	edges = vector<pair<int, int>>{
		make_pair(1,2),
		make_pair(1, 3),
		make_pair(3, 2),
		make_pair(2, 4),
		make_pair(3, 4)
	};
	Explore(true);
}


void Euler::Explore(bool type) {

	// 커넥션 정보 만들기
	// 정점 갯수 찾기
	int oddDegreeCount = 0;
	int oddCount[501] = { false, };
	for (pair<int, int> edge: edges) {

		// 연결 행렬 만들기
		conArr[edge.first][edge.second] = 1;
		conArr[edge.second][edge.first] = 1;

		// 홀수 점 개수 갱신
		if (++(oddCount[edge.first]) % 2 == 1) oddDegreeCount++;
		else oddDegreeCount--;

		if (++(oddCount[edge.second]) % 2 == 1) oddDegreeCount++;
		else oddDegreeCount--;
	}

	if (oddDegreeCount != 0 && oddDegreeCount != 2) return;

	// 시작포인트 찾기
	int stPoint = -1;
	for (int i = 1; i <= N; i++) {
		if (oddDegreeCount == 2) {
			if (oddCount[i] % 2 == 1) {
				stPoint = i;
				break;
			}
		}
		else {
			stPoint = i;
			break;
		}
	}
	


	printData.push_back(stPoint);
	
	ExploreLogic_Backtracking(stPoint, 0);
	return;
}

bool Euler::ExploreLogic_Backtracking(int num, int level) {
	//cout << num << " " << level << endl;
	if (level == edges.size()) {
		for (auto num : printData) {
			cout << num << " " << " ";
		}
		cout << endl;
		return true;
	} 

	bool ret = false;
	// 간선 탐색
	for (int idx = 1; idx <= N; idx++) {

		// 탐색한 간선이면 패스
		if (conArr[num][idx] == 0)  continue;

		// 방문처리
		conArr[num][idx] = 0;
		conArr[idx][num] = 0;

		// 출력 항목 추가
		printData.push_back(idx);

		// 재귀함수
		ret = ExploreLogic_Backtracking(idx, level + 1);

		// 출력 항목 제거 
		if (printData.size() == 0) {
			cout << "wan!!!!!!" << endl;
		}
		printData.pop_back();

		conArr[num][idx] = 1;
		conArr[idx][num] = 1;
	}
	return ret;
}
```

## 해밀턴 탐색

해밀턴 탐색은 그래프의 정점을 한번씩만 방문하는 경로를 생성하는 알고리즘이다.

오일려 탐색과 다르게 모든 간선을 방문하지는 않아도 되지만, 역시 사용했던 간선을 다시 사용할 수는 없다.

해밀턴 탐색의 경우 코테에 자주나오는 DFS 문제에서 2차원 배열 탐색을 위해 자주 사용한다. (정점간 이동이 상하좌우로만 가능한 그래프 구현 문제가 된다.)

### 소스코드

오일러 탐색과 유사하지만 탐색 종료 조건을 간선에서 정점으로 바꿔 주고 중복 정점 탐색을 막아준다.


```c++
#pragma once
#include <iostream>
#include <vector>

using namespace std;

class Hamilton {
public:
	int N;									// 정점 개수
	vector<pair<int, int>> edges;			// 간선 리스트
	void Explore_Hamilton(bool type);
	Hamilton();

private:
	int conArr[501][501];					// 정점 인접 행렬
	int visitedNode[501] = { false, };

	vector<int> printData;					// 출력 용

	bool ExploreLogic_Hamilton(int num, int idx);
};
```

```c++
#include "Hamilton.h"

Hamilton::Hamilton() {
	N = 4;
	edges = vector<pair<int, int>>{
		make_pair(1,2),
		make_pair(1, 3),
		make_pair(3, 2),
		make_pair(2, 4),
		make_pair(3, 4)
	};

	Explore_Hamilton(true);
}

// 모든 정점을 탐색하는 메서드
void Hamilton::Explore_Hamilton(bool type) {

	// 인접 맵 생성

	for (pair<int, int> edge : edges) {
		conArr[edge.first][edge.second] = 1;
		conArr[edge.second][edge.first] = 1;
	}

	printData.push_back(1);
	ExploreLogic_Hamilton(1, 0);
	return;
}

bool Hamilton::ExploreLogic_Hamilton(int num, int level) {
	//cout << num << " " << level << endl;
	if (level == N) {
		for (auto num : printData) {
			cout << num << " " << " ";
		}
		cout << endl;
		return true;
	}

	bool ret = false;


	// 간선 탐색
	for (int idx = 1; idx <= N; idx++) {

		// 탐색한 간선이면 패스
		if (conArr[num][idx] == 0 || visitedNode[idx]) continue;


		// 방문처리
		conArr[num][idx] = 0;
		conArr[idx][num] = 0;

		// 출력 항목 추가
		printData.push_back(idx);

		visitedNode[idx] = true;

		// 재귀함수
		ret = ExploreLogic_Hamilton(idx, level + 1);

		printData.pop_back();

		visitedNode[idx] = false;

		conArr[num][idx] = 1;
		conArr[idx][num] = 1;
	}
	return ret;
}
```

## 기타

USACO 문제를 풀 때는 인접리스트를 활용해 그래프 문제를 푸는 경우가 잦았는데, 코테용 문제로는 체감상 잘 안나오는 것 같습니다.

본래 그래프 알고리즘을 선호하지만 선택과 집중을 위해 잘 안쓰게 되네요 ㅠ