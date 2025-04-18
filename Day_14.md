# Day 14: Graphs - 상세 해설

## 1. Clone Graph

### 문제 정의
- 연결된 무방향 그래프가 주어집니다.
- 각 노드는 값(val)과 이웃 노드 목록(neighbors)을 가집니다.
- 이 그래프의 깊은 복사본(deep copy)을 만들어 반환해야 합니다.

### 문제 접근 분석

#### 핵심 도전 과제
1. 모든 노드를 정확히 복제해야 합니다.
2. 모든 연결 관계(간선)를 보존해야 합니다.
3. 중복 복제를 방지해야 합니다 - 이미 복제한 노드를 다시 복제하면 안 됩니다.

#### 해결 전략
- **해시맵 사용**: 원본 노드와 복제 노드 간의 매핑을 유지합니다.
- **그래프 순회**: 그래프의 모든 노드를 방문하기 위해 BFS 또는 DFS를 사용합니다.

### BFS 구현 상세 설명

```python
class Node:
    def __init__(self, val=0, neighbors=None):
        self.val = val
        self.neighbors = neighbors if neighbors is not None else []

def cloneGraph(node):
    # 예외 처리: 빈 그래프
    if not node:
        return None
    
    # 해시맵: 원본 노드 -> 복제 노드
    visited = {}
    
    # BFS 시작
    queue = [node]
    # 시작 노드 복제 및 해시맵에 추가
    visited[node] = Node(node.val)
    
    while queue:
        # 현재 처리할 노드
        curr = queue.pop(0)
        
        # 현재 노드의 모든 이웃 노드 처리
        for neighbor in curr.neighbors:
            # 이웃 노드가 아직 복제되지 않았다면
            if neighbor not in visited:
                # 이웃 노드 복제
                visited[neighbor] = Node(neighbor.val)
                # 다음에 처리할 수 있도록 큐에 추가
                queue.append(neighbor)
            
            # 현재 노드의 복제본에 이웃 노드의 복제본을 연결
            visited[curr].neighbors.append(visited[neighbor])
    
    # 시작 노드의 복제본 반환
    return visited[node]
```

#### 단계별 실행 과정
1. **초기화**:
   - 해시맵 `visited`를 생성하여 원본 노드와 복제 노드 간의 매핑 유지
   - 시작 노드를 복제하고 해시맵에 추가
   - 시작 노드를 BFS 큐에 추가

2. **BFS 순회**:
   - 큐에서 노드를 하나씩 꺼내 처리
   - 현재 노드의 모든 이웃 노드에 대해:
     - 아직 복제되지 않았다면 복제하고 해시맵에 추가
     - 해당 이웃 노드를 큐에 추가하여 나중에 처리
     - 현재 노드의 복제본과 이웃 노드의 복제본 사이에 간선 추가

3. **반환**:
   - 시작 노드의 복제본 반환

### DFS 구현 상세 설명

```python
def cloneGraph(node):
    if not node:
        return None
    
    # 해시맵: 원본 노드 -> 복제 노드
    visited = {}
    
    def dfs(original):
        # 이미 복제된 노드면 반환
        if original in visited:
            return visited[original]
        
        # 현재 노드 복제
        clone = Node(original.val)
        # 해시맵에 추가
        visited[original] = clone
        
        # 이웃 노드 복제 및 연결
        for neighbor in original.neighbors:
            clone.neighbors.append(dfs(neighbor))
        
        return clone
    
    return dfs(node)
```

#### 단계별 실행 과정
1. **DFS 함수 정의**:
   - 원본 노드를 입력으로 받아 복제된 노드를 반환하는 재귀 함수
   - 이미 복제된 노드는 다시 복제하지 않고 해시맵에서 바로 반환

2. **노드 처리**:
   - 현재 노드 복제 및 해시맵에 추가
   - 각 이웃 노드에 대해 재귀적으로 DFS 호출
   - 복제된 이웃 노드를 현재 복제 노드의 neighbors 목록에 추가

3. **반환**:
   - 시작 노드의 복제본 반환

### 복잡도 분석
- **시간 복잡도**: O(V + E)
  - 모든 노드(V)와 모든 간선(E)을 한 번씩 순회
- **공간 복잡도**: O(V)
  - 해시맵과 큐/스택에 최대 V개의 노드 저장

### 추가 최적화 및 고려사항
- BFS는 메모리에 더 효율적일 수 있지만 DFS는 구현이 더 간결합니다.
- 양방향 간선을 처리할 때 중복 연결을 방지하기 위한 로직이 중요합니다.
- 해시맵을 사용한 추적은 그래프 복제에서 핵심 테크닉입니다.

## 2. Course Schedule

### 문제 정의
- n개의 과목(0부터 n-1까지 번호 매김)과 선수 과목 관계 배열 prerequisites가 주어집니다.
- prerequisites[i] = [ai, bi]는 "과목 ai를 수강하기 위해 먼저 과목 bi를 들어야 한다"는 관계를 나타냅니다.
- 모든 과목을 수강할 수 있는지 여부(true/false)를 반환해야 합니다.

### 문제 접근 분석

#### 핵심 도전 과제
1. 모든 과목 간의 선수 관계를 파악해야 합니다.
2. 순환 의존성(사이클)이 존재하는지 확인해야 합니다.
3. 사이클이 있다면 모든 과목을 수강할 수 없습니다.

#### 해결 전략
1. **방향 그래프 모델링**: 각 과목은 노드, 선수 관계는 방향 간선으로 표현합니다.
2. **사이클 탐지 방법**:
   - **위상 정렬(Kahn's 알고리즘)**: 진입 차수가 0인 노드부터 시작하여 그래프에서 제거하는 방식
   - **DFS 사이클 탐지**: 방문 상태를 추적하여 사이클을 감지하는 방식

### 위상 정렬 구현 상세 설명

```python
def canFinish(numCourses, prerequisites):
    # 진입 차수(in-degree) 초기화
    in_degree = [0] * numCourses
    # 인접 리스트로 그래프 표현
    graph = [[] for _ in range(numCourses)]
    
    # 그래프 구성 및 진입 차수 계산
    for course, prereq in prerequisites:
        graph[prereq].append(course)  # prereq -> course 방향 간선
        in_degree[course] += 1  # course의 진입 차수 증가
    
    # 진입 차수가 0인 과목으로 시작 (선수 과목이 없는 과목)
    queue = []
    for i in range(numCourses):
        if in_degree[i] == 0:
            queue.append(i)
    
    # 수강 가능한 과목 수
    count = 0
    
    # BFS를 통한 위상 정렬
    while queue:
        course = queue.pop(0)
        count += 1  # 현재 과목을 수강 가능하므로 카운트 증가
        
        # 현재 과목 이수 후 이수 가능한 다음 과목들 처리
        for next_course in graph[course]:
            in_degree[next_course] -= 1  # 선수 과목 하나 이수로 진입 차수 감소
            # 모든 선수 과목을 이수했다면
            if in_degree[next_course] == 0:
                queue.append(next_course)
    
    # 모든 과목을 수강할 수 있는지 확인
    # count == numCourses이면 모든 과목 수강 가능
    return count == numCourses
```

#### 단계별 실행 과정
1. **그래프 구성**:
   - 인접 리스트로 방향 그래프 표현
   - 각 과목의 진입 차수(선수 과목 수) 계산

2. **위상 정렬 시작**:
   - 진입 차수가 0인 모든 과목(선수 과목이 없는)을 큐에 추가

3. **BFS 수행**:
   - 큐에서 과목을 하나씩 꺼내면서 "수강" 처리
   - 해당 과목을 선수 과목으로 하는 모든 과목의 진입 차수 감소
   - 진입 차수가 0이 된 과목을 큐에 추가

4. **결과 확인**:
   - 수강 가능한 과목 수가 전체 과목 수와 같은지 확인
   - 같지 않다면 사이클이 존재하여 모든 과목을 수강할 수 없음

### DFS 사이클 탐지 구현 상세 설명

```python
def canFinish(numCourses, prerequisites):
    # 인접 리스트로 그래프 표현
    graph = [[] for _ in range(numCourses)]
    
    for course, prereq in prerequisites:
        graph[prereq].append(course)
    
    # 방문 상태 배열
    # 0: 미방문, 1: 현재 DFS 경로에서 방문 중, 2: 이미 처리 완료
    visited = [0] * numCourses
    
    def has_cycle(course):
        # 현재 DFS 경로에서 이미 방문했다면 사이클
        if visited[course] == 1:
            return True
        # 이미 처리 완료된 노드라면 안전
        if visited[course] == 2:
            return False
        
        # 현재 DFS 경로에서 방문 중으로 표시
        visited[course] = 1
        
        # 다음 과목들에 대해 재귀적으로 사이클 확인
        for next_course in graph[course]:
            if has_cycle(next_course):
                return True
        
        # 처리 완료로 표시
        visited[course] = 2
        return False
    
    # 모든 미방문 과목에 대해 사이클 확인
    for course in range(numCourses):
        if visited[course] == 0:
            if has_cycle(course):
                return False  # 사이클 존재하면 모든 과목 수강 불가능
    
    return True  # 사이클 없으면 모든 과목 수강 가능
```

#### 단계별 실행 과정
1. **그래프 구성**:
   - 인접 리스트로 방향 그래프 표현

2. **DFS 사이클 탐지 함수 정의**:
   - 방문 상태를 3가지로 추적: 미방문(0), 현재 DFS 경로에서 방문 중(1), 처리 완료(2)
   - 현재 DFS 경로에서 이미 방문한 노드를 다시 방문하면 사이클 발견

3. **모든 노드에 대해 DFS 실행**:
   - 아직 방문하지 않은 모든 과목에 대해 DFS 시작
   - 사이클이 발견되면 즉시 false 반환

4. **결과 확인**:
   - 모든 DFS 탐색이 사이클 없이 완료되면 true 반환

### 복잡도 분석
- **시간 복잡도**: O(V + E)
  - V는 과목 수, E는 선수 관계의 수
  - 모든 노드와 간선을 한 번씩 방문
- **공간 복잡도**: O(V + E)
  - 그래프 저장에 O(V + E)
  - 방문 상태 배열과 큐/스택에 O(V)

### 추가 최적화 및 고려사항
- 위상 정렬은 선수 과목 순서를 생성해야 할 때 유용합니다.
- DFS 사이클 탐지는 코드가 간결하지만 모든 과목의 수강 순서를 얻을 수는 없습니다.
- 실제 교육 시스템에서는 이러한 알고리즘으로 커리큘럼 계획을 검증할 수 있습니다.

## 3. Number of Islands

### 문제 정의
- '1'(육지)과 '0'(물)으로 이루어진 2D 격자 맵이 주어집니다.
- 섬은 수평과 수직으로 인접한 육지('1')들의 집합입니다.
- 맵에 있는 섬의 개수를 구해야 합니다.

### 문제 접근 분석

#### 핵심 도전 과제
1. 격자에서 연결된 육지('1')들을 식별해야 합니다.
2. 각 섬을 한 번씩만 세야 합니다.
3. 격자 내에서 효율적으로 탐색해야 합니다.

#### 해결 전략
- **연결 요소 세기**: 이 문제는 그래프에서 연결 요소(connected components)의 개수를 세는 문제로 모델링할 수 있습니다.
- **탐색 방법**: DFS나 BFS를 사용하여 각 육지('1')에서 시작해 연결된 모든 육지를 방문합니다.
- **방문 표시**: 방문한 육지는 물('0')로 표시하여 중복 계산을 방지합니다.

### DFS 구현 상세 설명

```python
def numIslands(grid):
    if not grid or not grid[0]:
        return 0
    
    rows, cols = len(grid), len(grid[0])
    count = 0
    
    def dfs(r, c):
        # 경계 검사 또는 물('0')인 경우 종료
        if r < 0 or r >= rows or c < 0 or c >= cols or grid[r][c] == '0':
            return
        
        # 방문한 육지를 물('0')로 표시 (방문 표시)
        grid[r][c] = '0'
        
        # 상하좌우 네 방향으로 DFS 탐색
        dfs(r+1, c)  # 아래
        dfs(r-1, c)  # 위
        dfs(r, c+1)  # 오른쪽
        dfs(r, c-1)  # 왼쪽
    
    # 모든 셀을 순회하며 미방문 육지('1')에서 DFS 시작
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1':
                count += 1  # 새로운 섬 발견
                dfs(r, c)   # 현재 섬의 모든 육지 방문 표시
    
    return count
```

#### 단계별 실행 과정
1. **격자 순회**:
   - 격자의 모든 셀을 행과 열 순서로 순회합니다.
   - 육지('1')를 발견하면 새로운 섬의 시작점으로 간주합니다.

2. **DFS 탐색**:
   - 현재 육지에서 DFS를 시작합니다.
   - 방문한 육지는 물('0')로 표시합니다.
   - 상하좌우 네 방향으로 연결된 모든 육지를 재귀적으로 방문합니다.

3. **카운팅**:
   - DFS가 완료될 때마다 하나의 섬을 완전히 탐색한 것이므로 섬 개수를 증가시킵니다.

4. **반환**:
   - 최종 섬 개수를 반환합니다.

### BFS 구현 상세 설명

```python
from collections import deque

def numIslands(grid):
    if not grid or not grid[0]:
        return 0
    
    rows, cols = len(grid), len(grid[0])
    count = 0
    
    def bfs(r, c):
        queue = deque([(r, c)])
        grid[r][c] = '0'  # 시작점 방문 표시
        
        while queue:
            curr_r, curr_c = queue.popleft()
            
            # 상하좌우 네 방향
            directions = [(1, 0), (-1, 0), (0, 1), (0, -1)]
            
            for dr, dc in directions:
                nr, nc = curr_r + dr, curr_c + dc
                
                # 경계 내부이고 육지('1')인 경우
                if 0 <= nr < rows and 0 <= nc < cols and grid[nr][nc] == '1':
                    queue.append((nr, nc))
                    grid[nr][nc] = '0'  # 방문 표시
    
    # 모든 셀을 순회하며 미방문 육지('1')에서 BFS 시작
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1':
                count += 1
                bfs(r, c)
    
    return count
```

#### 단계별 실행 과정
1. **격자 순회**:
   - 격자의 모든 셀을 순회합니다.
   - 육지('1')를 발견하면 새로운 섬의 시작점으로 간주합니다.

2. **BFS 탐색**:
   - 시작점을 큐에 추가하고 방문 표시합니다.
   - 큐에서 노드를 꺼내 상하좌우로 연결된 육지를 검사합니다.
   - 연결된 모든 육지를 큐에 추가하고 방문 표시합니다.

3. **카운팅**:
   - BFS가 완료될 때마다 하나의 섬을 완전히 탐색한 것이므로 섬 개수를 증가시킵니다.

4. **반환**:
   - 최종 섬 개수를 반환합니다.

### 복잡도 분석
- **시간 복잡도**: O(M × N)
  - M은 행 수, N은 열 수
  - 각 셀은 최대 한 번씩만 방문됩니다.
- **공간 복잡도**: O(min(M, N)) ~ O(M × N)
  - 최악의 경우, BFS/DFS에서 필요한 큐/스택의 크기는 격자 크기에 비례할 수 있습니다.
  - 실제로는 최대 크기가 min(M, N)에 가까울 수 있습니다 (최악의 경우 지그재그 모양의 섬).

### 추가 최적화 및 고려사항
- 입력 그리드를 수정하지 않아야 하는 경우 별도의 방문 배열을 사용해야 합니다.
- Union-Find 자료구조를 사용하여 해결할 수도 있지만, BFS/DFS가 더 직관적이고 일반적입니다.
- 대형 격자에서는 메모리 효율성을 위해 BFS를 사용하는 것이 좋을 수 있습니다.
- 실제 응용 프로그램에서는 이미지 분석, 네트워크 구획화 등에 이 알고리즘을 활용할 수 있습니다.

## 그래프 문제 해결의 핵심 포인트

1. **그래프 표현 방식**: 인접 행렬, 인접 리스트, 또는 암시적 그래프(격자, 트리 등)
2. **탐색 알고리즘 선택**: 문제의 특성에 따라 BFS 또는 DFS 선택
   - BFS: 최단 경로, 레벨별 탐색에 적합
   - DFS: 사이클 탐지, 백트래킹, 연결 요소에 적합
3. **방문 관리**: 방문 배열/해시맵 또는 입력 수정을 통한 방문 표시
4. **특수 알고리즘**: 위상 정렬, 최단 경로, 최소 신장 트리 등 문제에 맞는 알고리즘 선택
