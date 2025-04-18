# Day 15: Graphs (continued) - 상세 해설

## 1. Word Ladder

### 문제 정의
- 시작 단어 `beginWord`, 목표 단어 `endWord`, 그리고 단어 사전 `wordList`가 주어집니다.
- 단어 변환 규칙: 한 번에 한 글자만 변경할 수 있고, 변경된 단어는 `wordList`에 존재해야 합니다.
- `beginWord`에서 `endWord`로 변환하는 최소 단계 수를 구해야 합니다.
- 변환이 불가능하면 0을 반환합니다.

### 문제 접근 분석

#### 핵심 도전 과제
1. 유효한 단어 변환 경로를 찾아야 합니다.
2. 가능한 모든 변환 경로 중 가장 짧은 것을 찾아야 합니다.
3. 가능한 단어 변환이 매우 많을 수 있습니다.

#### 해결 전략
- **그래프 모델링**: 각 단어를 노드로, 한 글자만 다른 단어 사이에 간선을 추가합니다.
- **최단 경로 탐색**: 그래프에서 `beginWord`에서 `endWord`까지의 최단 경로를 찾기 위해 BFS를 사용합니다.
- **효율적인 단어 변환 탐색**: 각 위치에서 모든 알파벳으로 변경해보는 방식으로 인접 단어를 찾습니다.

### BFS 구현 상세 설명

```python
from collections import deque

def ladderLength(beginWord, endWord, wordList):
    # endWord가 wordList에 없으면 변환 불가능
    if endWord not in wordList:
        return 0
    
    # 효율적인 검색을 위해 집합으로 변환
    word_set = set(wordList)
    
    # BFS 시작
    queue = deque([(beginWord, 1)])  # (단어, 변환 길이)
    visited = set([beginWord])  # 방문한 단어를 추적
    
    while queue:
        word, length = queue.popleft()
        
        # 현재 단어가 목표 단어면 길이 반환
        if word == endWord:
            return length
        
        # 현재 단어의 각 위치를 a-z로 변경해보며 다음 단어 탐색
        for i in range(len(word)):
            # 원래 글자를 저장
            original_char = word[i]
            
            # a부터 z까지 모든 가능한 글자로 변경
            for c in 'abcdefghijklmnopqrstuvwxyz':
                # 원래 글자와 같으면 넘어감
                if c == original_char:
                    continue
                
                # 새 단어 생성
                new_word = word[:i] + c + word[i+1:]
                
                # 새 단어가 단어 집합에 있고 아직 방문하지 않았다면
                if new_word in word_set and new_word not in visited:
                    queue.append((new_word, length + 1))
                    visited.add(new_word)  # 방문 표시
    
    # 변환 불가능
    return 0
```

#### 단계별 실행 과정
1. **초기 검증**:
   - `endWord`가 `wordList`에 없으면 즉시 0 반환 (변환 불가능)
   
2. **BFS 초기화**:
   - 시작 단어와 변환 단계 수(1)를 큐에 추가
   - 방문한 단어를 추적하기 위한 집합 생성
   
3. **BFS 탐색**:
   - 큐에서 단어와 현재까지의 변환 단계 수를 꺼냄
   - 목표 단어에 도달했는지 확인
   - 현재 단어의 각 위치에서 모든 알파벳으로 변경 시도
   - 변경된 단어가 단어 목록에 있고 아직 방문하지 않았다면 큐에 추가
   
4. **결과 반환**:
   - 목표 단어에 도달하면 변환 단계 수 반환
   - 큐가 비면 (더 이상 탐색할 단어가 없으면) 0 반환

### 양방향 BFS 최적화 구현

```python
def ladderLength(beginWord, endWord, wordList):
    if endWord not in wordList:
        return 0
    
    # 단어 집합
    word_set = set(wordList)
    if beginWord in word_set:
        word_set.remove(beginWord)
    
    # 양방향 BFS를 위한 두 집합
    begin_set = {beginWord}
    end_set = {endWord}
    
    # 방문한 단어 추적
    visited = set()
    
    # 변환 단계 수
    length = 1
    
    # 양방향 BFS
    while begin_set and end_set:
        # 항상 더 작은 집합으로 BFS (효율성)
        if len(begin_set) > len(end_set):
            begin_set, end_set = end_set, begin_set
        
        # 다음 레벨의 단어들
        next_level = set()
        
        for word in begin_set:
            # 현재 단어의 각 위치를 a-z로 변경
            for i in range(len(word)):
                original_char = word[i]
                
                for c in 'abcdefghijklmnopqrstuvwxyz':
                    if c == original_char:
                        continue
                    
                    new_word = word[:i] + c + word[i+1:]
                    
                    # 다른 방향에서 이미 방문한 단어에 도달하면 완료
                    if new_word in end_set:
                        return length + 1
                    
                    # 단어 집합에 있고 아직 방문하지 않았다면
                    if new_word in word_set and new_word not in visited:
                        next_level.add(new_word)
                        visited.add(new_word)
        
        # 다음 레벨로 이동
        begin_set = next_level
        length += 1
    
    # 변환 불가능
    return 0
```

#### 양방향 BFS 실행 과정
1. **초기화**:
   - 시작 단어 집합(`begin_set`)과 목표 단어 집합(`end_set`) 생성
   
2. **양방향 탐색**:
   - 항상 더 작은 집합에서 BFS 수행 (효율성을 위해)
   - 한쪽 방향에서 확장할 때 다른 방향에서 이미 방문한 단어에 도달하면 경로 발견
   
3. **효율성 개선**:
   - 단방향 BFS에 비해 양방향 BFS는 탐색 공간을 크게 줄일 수 있음
   - 시간 복잡도는 동일하지만 실제 성능은 훨씬 좋을 수 있음

### 복잡도 분석
- **시간 복잡도**: O(M × N)
  - M은 단어의 길이, N은 단어 목록의 크기
  - 각 단어의, 각 위치에서, 26개 알파벳으로 변경 시도 → O(M × 26)
  - 최악의 경우 모든 단어를 방문 → O(N)
  - 따라서 총 시간 복잡도는 O(M × N)
  
- **공간 복잡도**: O(N)
  - 단어 집합, 방문 집합, 큐에 최대 N개의 단어 저장
  - 양방향 BFS에서도 동일한 공간 복잡도를 가짐

### 추가 최적화 및 고려사항
- **단어 생성 최적화**: 26개 알파벳으로 모든 변경을 시도하는 대신 단어 목록에서 한 글자만 다른 단어를 직접 찾는 방법도 가능합니다.
- **메모리 최적화**: 큰 단어 목록에서는 방문한 단어를 단어 집합에서 직접 제거하여 추가 방문 집합 없이 구현할 수 있습니다.
- **중간 노드 패턴**: 각 단어에서 한 글자를 와일드카드(*)로 치환한 패턴을 만들어 같은 패턴을 가진 단어들을 연결하는 방식으로 구현할 수도 있습니다.

## 2. Pacific Atlantic Water Flow

### 문제 정의
- m×n 정수 행렬 `heights`가 주어집니다. 각 값은 해당 지점의 높이를 나타냅니다.
- 행렬의 상단과 좌측 경계는 태평양(Pacific Ocean)에, 하단과 우측 경계는 대서양(Atlantic Ocean)에 인접해 있습니다.
- 비는 높거나 같은 곳에서 낮은 곳으로만 흘러갑니다.
- 빗물이 태평양과 대서양 모두로 흘러갈 수 있는 지점들의 좌표를 반환해야 합니다.

### 문제 접근 분석

#### 핵심 도전 과제
1. 각 지점에서 태평양과 대서양으로 물이 흘러갈 수 있는지 확인해야 합니다.
2. 모든 지점에서 모든 방향으로 탐색하는 것은 비효율적입니다.
3. 물의 흐름은 높은 곳에서 낮은 곳으로만 이동합니다.

#### 해결 전략
- **역방향 DFS/BFS**: 바다에서 시작하여 역방향으로 탐색합니다 (낮은 곳에서 높은 곳으로).
- **두 바다에서 각각 탐색**: 태평양과 대서양에서 각각 도달할 수 있는 지점들을 찾은 후, 두 집합의 교집합을 구합니다.
- **효율성**: 이 접근법은 모든 지점에서 출발하는 것보다 훨씬 효율적입니다.

### DFS 구현 상세 설명

```python
def pacificAtlantic(heights):
    if not heights or not heights[0]:
        return []
    
    rows, cols = len(heights), len(heights[0])
    
    # 태평양과 대서양에서 도달할 수 있는 지점들
    pacific_reachable = set()
    atlantic_reachable = set()
    
    def dfs(r, c, reachable):
        # 현재 셀을 방문 가능으로 표시
        reachable.add((r, c))
        
        # 상하좌우 네 방향
        directions = [(1, 0), (-1, 0), (0, 1), (0, -1)]
        
        for dr, dc in directions:
            nr, nc = r + dr, c + dc
            
            # 1. 경계 내부인지 확인
            # 2. 아직 방문하지 않았는지 확인
            # 3. 현재 위치보다 높이가 같거나 높은지 확인 (역방향 흐름)
            if (0 <= nr < rows and 0 <= nc < cols and 
                (nr, nc) not in reachable and 
                heights[nr][nc] >= heights[r][c]):
                dfs(nr, nc, reachable)
    
    # 태평양 경계(상단과 좌측)에서 DFS
    for c in range(cols):
        dfs(0, c, pacific_reachable)  # 상단 행
    
    for r in range(1, rows):  # 1부터 시작하여 중복 방지
        dfs(r, 0, pacific_reachable)  # 좌측 열
    
    # 대서양 경계(하단과 우측)에서 DFS
    for c in range(cols):
        dfs(rows-1, c, atlantic_reachable)  # 하단 행
    
    for r in range(rows-1):  # rows-1까지만 반복하여 중복 방지
        dfs(r, cols-1, atlantic_reachable)  # 우측 열
    
    # 두 바다 모두에서 도달 가능한 지점들
    return list(pacific_reachable & atlantic_reachable)
```

#### 단계별 실행 과정
1. **초기화**:
   - 태평양과 대서양에서 각각 도달할 수 있는 지점들을 저장할 집합 생성
   
2. **태평양 DFS**:
   - 태평양에 인접한 경계(상단 행과 좌측 열)에서 DFS 시작
   - 역방향으로 탐색: 현재 위치보다 높이가 같거나 높은 인접 지점으로만 이동
   
3. **대서양 DFS**:
   - 대서양에 인접한 경계(하단 행과 우측 열)에서 DFS 시작
   - 태평양 DFS와 마찬가지로 역방향으로 탐색
   
4. **결과 계산**:
   - 두 집합의 교집합을 구하여 두 바다 모두에 도달할 수 있는 지점들 반환

### BFS 구현 상세 설명

```python
from collections import deque

def pacificAtlantic(heights):
    if not heights or not heights[0]:
        return []
    
    rows, cols = len(heights), len(heights[0])
    pacific_reachable = set()
    atlantic_reachable = set()
    
    def bfs(queue, reachable):
        directions = [(1, 0), (-1, 0), (0, 1), (0, -1)]
        
        while queue:
            r, c = queue.popleft()
            
            for dr, dc in directions:
                nr, nc = r + dr, c + dc
                
                # 경계 내부, 미방문, 높이가 같거나 높은 지점
                if (0 <= nr < rows and 0 <= nc < cols and 
                    (nr, nc) not in reachable and 
                    heights[nr][nc] >= heights[r][c]):
                    reachable.add((nr, nc))
                    queue.append((nr, nc))
    
    # 태평양 BFS 초기화 (상단과 좌측 경계)
    pacific_queue = deque()
    for c in range(cols):
        pacific_queue.append((0, c))
        pacific_reachable.add((0, c))
    
    for r in range(1, rows):
        pacific_queue.append((r, 0))
        pacific_reachable.add((r, 0))
    
    # 대서양 BFS 초기화 (하단과 우측 경계)
    atlantic_queue = deque()
    for c in range(cols):
        atlantic_queue.append((rows-1, c))
        atlantic_reachable.add((rows-1, c))
    
    for r in range(rows-1):
        atlantic_queue.append((r, cols-1))
        atlantic_reachable.add((r, cols-1))
    
    # BFS 실행
    bfs(pacific_queue, pacific_reachable)
    bfs(atlantic_queue, atlantic_reachable)
    
    # 결과 계산
    return list(pacific_reachable & atlantic_reachable)
```

#### BFS 실행 과정
1. **초기화**:
   - 태평양과 대서양 경계 지점들을 각각의 큐와 집합에 추가
   
2. **BFS 탐색**:
   - 큐에서 지점을 꺼내 상하좌우 네 방향 탐색
   - 경계 내부이고, 미방문이며, 높이가 같거나 높은 인접 지점을 큐에 추가
   
3. **결과 계산**:
   - 두 집합의 교집합을 구하여 반환

### 복잡도 분석
- **시간 복잡도**: O(m × n)
  - m은 행의 수, n은 열의 수
  - 각 셀은 최대 두 번만 방문됨 (태평양 탐색과 대서양 탐색에서 각각 한 번)
  
- **공간 복잡도**: O(m × n)
  - 방문 집합과 큐에 최대 m × n개의 지점 저장

### 추가 최적화 및 고려사항
- **메모리 최적화**: 집합 대신 방문 배열을 사용하여 공간을 절약할 수 있습니다.
- **동시 탐색**: 두 바다의 탐색을 병합하여 한 번에 수행할 수도 있지만, 코드가 더 복잡해집니다.
- **실제 응용**: 이 문제는 지형 분석, 분수령 결정, 홍수 시뮬레이션 등의 실제 응용 분야와 관련이 있습니다.

## 3. Alien Dictionary

### 문제 정의
- 외계 언어의 단어 배열 `words`가 사전식(lexicographically) 순서로 정렬되어 있습니다.
- 이 언어의 알파벳 순서를 추론해야 합니다.
- 추론된 알파벳 순서를 문자열로 반환합니다. 유효한 순서가 여러 개 있을 수 있으며, 그 중 하나만 반환하면 됩니다.
- 유효한 순서가 없으면 빈 문자열을 반환합니다.

### 문제 접근 분석

#### 핵심 도전 과제
1. 단어 간의 상대적 순서를 통해 문자 간의 순서 관계를 추론해야 합니다.
2. 문자 간의 순서 관계가 모순되지 않아야 합니다 (사이클이 없어야 함).
3. 모든 문자에 대한 순서를 결정해야 합니다.

#### 해결 전략
- **방향 그래프 모델링**: 각 문자를 노드로, 순서 관계를 방향 간선으로 표현합니다.
- **순서 관계 추출**: 인접한 단어 쌍을 비교하여 첫 번째 다른 문자 사이의 순서 관계를 그래프에 추가합니다.
- **위상 정렬**: 그래프를 위상 정렬하여 문자들의 순서를 결정합니다.

### 위상 정렬 구현 상세 설명

```python
from collections import defaultdict, Counter, deque

def alienOrder(words):
    # 모든 문자 식별
    chars = set(''.join(words))
    
    # 그래프 구성 (인접 리스트)
    graph = defaultdict(set)
    in_degree = Counter({c: 0 for c in chars})
    
    # 인접한 단어들을 비교하여 문자 순서 추론
    for i in range(len(words) - 1):
        w1, w2 = words[i], words[i+1]
        
        # 접두사 확인 (예: "abc"와 "ab"가 이 순서로 있다면 유효하지 않음)
        if len(w1) > len(w2) and w1.startswith(w2):
            return ""
        
        # 첫 번째 다른 문자를 찾아 순서 관계 추가
        for c1, c2 in zip(w1, w2):
            if c1 != c2:
                # 새로운 순서 관계인 경우에만 진입 차수 증가
                if c2 not in graph[c1]:
                    graph[c1].add(c2)
                    in_degree[c2] += 1
                break
    
    # 위상 정렬 (Kahn's 알고리즘)
    result = []
    queue = deque([c for c in chars if in_degree[c] == 0])
    
    while queue:
        c = queue.popleft()
        result.append(c)
        
        for next_c in graph[c]:
            in_degree[next_c] -= 1
            if in_degree[next_c] == 0:
                queue.append(next_c)
    
    # 사이클 검증 (모든 문자가 처리되었는지)
    if len(result) != len(chars):
        return ""  # 사이클 존재
    
    return ''.join(result)
```

#### 단계별 실행 과정
1. **문자 식별**:
   - 모든 단어에서 사용된 고유한 문자들을 수집
   
2. **그래프 구성**:
   - 인접한 단어 쌍을 비교하여 첫 번째 다른 문자 사이에 방향 간선 추가
   - 예: "wrt"와 "wrf"를 비교하면 't' -> 'f' 관계 추가
   - 각 문자의 진입 차수(in-degree) 계산
   
3. **유효성 검사**:
   - 긴 단어가 짧은 단어의 접두사인 경우 유효하지 않음 (예: ["abc", "ab"])
   
4. **위상 정렬**:
   - 진입 차수가 0인 문자들로 시작
   - 처리한 문자의 이웃 문자들의 진입 차수 감소
   - 진입 차수가 0이 된 문자들을 큐에 추가
   
5. **결과 검증**:
   - 모든 문자가 처리되지 않았다면 사이클이 존재하므로 빈 문자열 반환
   - 모든 문자가 처리되었다면 순서대로 문자열 생성

### DFS 기반 위상 정렬 구현

```python
def alienOrder(words):
    # 모든 문자 식별
    chars = set(''.join(words))
    
    # 그래프 구성
    graph = {c: set() for c in chars}
    
    # 인접한 단어들로부터 순서 관계 추출
    for i in range(len(words) - 1):
        w1, w2 = words[i], words[i+1]
        
        # 접두사 확인
        if len(w1) > len(w2) and w1.startswith(w2):
            return ""
        
        # 첫 번째 다른 문자 찾기
        for j in range(min(len(w1), len(w2))):
            if w1[j] != w2[j]:
                graph[w1[j]].add(w2[j])
                break
    
    # 방문 상태: 0=미방문, 1=처리 중, 2=처리 완료
    visited = {c: 0 for c in chars}
    result = []
    
    # DFS 기반 위상 정렬
    def dfs(c):
        # 현재 경로에서 이미 방문 중이면 사이클
        if visited[c] == 1:
            return False
        # 이미 처리 완료된 문자면 건너뛰기
        if visited[c] == 2:
            return True
        
        # 방문 중으로 표시
        visited[c] = 1
        
        # 이웃 문자들 탐색
        for next_c in graph[c]:
            if not dfs(next_c):
                return False
        
        # 처리 완료로 표시
        visited[c] = 2
        # 결과 리스트에 추가 (역순)
        result.append(c)
        return True
    
    # 모든 문자에 대해 DFS 수행
    for c in chars:
        if visited[c] == 0:
            if not dfs(c):
                return ""  # 사이클 발견
    
    # 결과 리스트를 역순으로 반환 (DFS 후위 순회 특성)
    return ''.join(result[::-1])
```

#### DFS 위상 정렬 실행 과정
1. **그래프 구성**:
   - BFS 방식과 동일하게 문자 간 순서 관계 추출
   
2. **DFS 위상 정렬**:
   - 각 문자에 대해 DFS 수행
   - 방문 상태를 추적하여 사이클 감지
   - 처리 완료된 문자를 결과 리스트에 추가 (후위 순회)
   
3. **결과 반환**:
   - DFS 후위 순회의 특성상 결과 리스트를 역순으로 반환

### 복잡도 분석
- **시간 복잡도**: O(C)
  - C는 모든 단어의 총 문자 수
  - 단어 비교, 그래프 구성, 위상 정렬 모두 O(C) 시간이 소요됨
  
- **공간 복잡도**: O(1) 또는 O(U)
  - U는 고유 문자 수 (알파벳 크기에 제한이 있다면 O(1))
  - 그래프, 진입 차수 배열, 큐/스택 등에 사용되는 공간

### 추가 최적화 및 고려사항
- **다중 제약 조건**: 단어 간의 상대적 순서가 모호할 수 있으며, 이 경우 여러 유효한 순서가 가능합니다.
- **완전성 검증**: 입력만으로는 모든 문자 간의 순서 관계를 결정할 수 없을 수 있습니다.
- **실제 응용**: 이 문제는 언어학, 컴파일러 설계, 종속성 해결 등의 분야와 관련이 있습니다.

## 그래프 문제 고급 전략

Day 15의 문제들은 Day 14보다 더 복잡한 그래프 알고리즘과 응용을 요구합니다. 다음은 이러한, 더 고급 그래프 문제를 해결하기 위한 전략입니다:

### 1. 문제 모델링
- **그래프 유형 식별**: 방향/무방향, 가중치 있음/없음, 사이클 허용/금지
- **노드와 간선 정의**: 문제의 요소들을 그래프 구성 요소로 매핑
- **암시적 그래프 인식**: 격자, 단어 관계, 상태 공간 등 다양한 형태로 그래프가 표현될 수 있음

### 2. 적절한 알고리즘 선택
- **BFS**: 최단 경로, 레벨별 탐색 (Word Ladder)
- **DFS**: 사이클 탐지, 연결 요소, 위상 정렬 (Alien Dictionary)
- **양방향/다중 시작점 탐색**: 효율성 향상 (Pacific Atlantic Water Flow)
- **위상 정렬**: 순서 관계 해결 (Alien Dictionary)

### 3. 효율성 고려
- **방문 상태 관리**: 단순 방문/미방문이 아닌 여러 상태 추적
- **불필요한 탐색 제거**: 조기 종료 조건, 가지치기 전략
- **메모리 효율성**: 불필요한 데이터 구조 제거, 메모리 재사용

### 4. 구현 기술
- **그래프 표현 선택**: 인접 리스트, 인접 행렬, 암시적 그래프
- **경계 케이스 처리**: 빈 그래프, 단일 노드, 모순된 제약 조건
- **디버깅 전략**: 단계별 실행, 중간 상태 확인
