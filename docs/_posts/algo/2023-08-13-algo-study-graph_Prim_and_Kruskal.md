---
title : 그래프 스패닝 트리 만들기 - 프림과 크루스칼
date: 2023-08-13 17:07
---

## 스패닝 트리

스패닝 트리는 사이클이 없는 그래프다. (수형도라고도 부른다)

각 노드에 접근하는 방법이 하나 뿐인 그래프이며, 사이클이 있는 그래프가 주어질 때 탐색 범위를 정하기 위해 사용한다.

그래프에서 최소 가중치를 갖는 스패닝 트리(Minimum Spanning Tree)를 찾는 알고리즘으로 Prim과 Kruskal이 있다.

## 프림

프림은 N^2 시간복잡도를 갖는 알고리즘으로, 간선의 개수가 많을 때 유리하다.

총 정점 개수 -1 만큼 반복문을 돌며 현재 스패닝 트리에서 다른 노드에 접근하는 가중치를 갱신 하는 방식으로 동작한다.

### 소스코드

```c++
# Prim.h
#pragma once

#include <iostream>
#include <vector>
using namespace std;

struct EdgeInfo {
	int s;
	int e;
	int v;
};

class Prim {
public :
	Prim();

private :
	int N;

	vector<EdgeInfo> edges;
	int conArr[51][51] = { 0, };		// 인접리스트

	// 탐색할 때 쓰는 자료형들
	vector<bool> visited;				// 현재 트리에 i 노드가 포함되었는지 여부
	vector<int> dist;					// 현재 트리에서 i 노드에 연결하는 비용
	vector<int> from;					// 현재 트리 i 노드가 연결된 노드, 없을시 -1

	void Do();
	void Explore_Prim();
};

```

```c++
Prim.cpp
Prim::Prim() {
	N = 4;

	edges = vector<EdgeInfo> {
		EdgeInfo { 1, 2, 3 },
		EdgeInfo { 1, 4, 1 },
		EdgeInfo { 2, 3, 4 },
		EdgeInfo { 2, 4, 2 },
		EdgeInfo { 3, 4, 5 }
	};

	Do();
}

void Prim::Do() {
	// 인접 행렬 만들기

	for (int i = 1; i <= N; i++) {
		for (int j = 1; j <= N; j++) {

			// 같은 노드로는 이동할 수 없음
			if (i == j) conArr[i][j] = -1;
			else {
				// 다른 노드 간 이동 거리는 최대값으로 초기화
				conArr[i][j] = 1e9;
			}
		}
	}
	for (EdgeInfo edge : edges) {
		conArr[edge.s][edge.e] = edge.v;
		conArr[edge.e][edge.s] = edge.v;
	}

	Explore_Prim();
}

void Prim::Explore_Prim() {
	

	// 초기화
	int initNode = 1;

	dist = vector<int>(conArr[initNode]+1, conArr[initNode] + N+1);
		// 스패닝 노드 내 init Node에서 i 노드에 접근하는 비용
	from = vector<int>(N+1, initNode); 
		// 스패닝 노드에서 i 노드에 접근하는 노드
	visited = vector<bool>(N+1, false);
		// 스패닝 노드 방문 여부

	dist.insert(dist.begin(), 0);

	cout << "Prim start " << endl;
	int num = 0; // 접근한 노드 개수
	visited[initNode] = true;
	while (num < N-1) {
		// 가장 가중치가 적은 노드 찾기
		int minNode = -1, minValue = 1e9;
		for (int i = 1; i <= N; i++) {
			if (visited[i]) continue;
			if (minValue > dist[i]) {
				minValue = dist[i];
				minNode = i;
			}
		}


		// 다음 노드 방문 
		/// 가중치 갱신
		cout << minNode << endl;
		visited[minNode] = true;

		/// 근원 노드 갱신
		for (int i = 1; i <= N; i++) {
			// 방문한 노드에서 접근할 수 있는 노드 중 가중치가 낮은 값이 있으면 반영
			if (!visited[i] && conArr[minNode][i] < dist[i]) {
				dist[i] = conArr[minNode][i];
				from[i] = minNode;
			}
		}
		num++;

		for (int i = 1; i <= N; i++) {
			cout << dist[i] << " ";
		}
		cout << endl;

		for (int i = 1; i <= N; i++) {
			cout << from[i] << " ";
		}
		cout << endl;
	}

	for (int i = 1; i <= N; i++) {
		if (from[i] != i) cout << "( " << i << " " << from[i] << " " << dist[i] <<  " )"  << endl;
	}
}
```