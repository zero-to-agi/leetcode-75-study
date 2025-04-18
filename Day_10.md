# 1. Binary Tree Inorder Traversal

## 문제 설명
이진 트리의 루트가 주어졌을 때, 중위 순회(inorder traversal)를 수행하여 노드의 값을 배열로 반환하는 문제입니다.

## 접근 방법

### 중위 순회(Inorder Traversal)란?
중위 순회는 다음 순서로 트리를 방문합니다:
1. 왼쪽 서브트리 방문
2. 현재 노드 방문
3. 오른쪽 서브트리 방문

### 방법 1: 재귀적 접근

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

def inorderTraversal(root):
    result = []
    
    def inorder(node):
        if not node:
            return
        
        # 왼쪽 서브트리 방문
        inorder(node.left)
        
        # 현재 노드 방문
        result.append(node.val)
        
        # 오른쪽 서브트리 방문
        inorder(node.right)
    
    inorder(root)
    return result
```

### 방법 2: 반복적 접근 (스택 사용)

```python
def inorderTraversal(root):
    result = []
    stack = []
    current = root
    
    while current or stack:
        # 왼쪽 자식들을 모두 스택에 push
        while current:
            stack.append(current)
            current = current.left
        
        # 스택에서 노드를 꺼내서 처리
        current = stack.pop()
        result.append(current.val)
        
        # 오른쪽 자식으로 이동
        current = current.right
    
    return result
```

## 복잡도 분석
- 시간 복잡도: O(N) - 모든 노드를 한 번씩 방문
- 공간 복잡도: 
  - 재귀: O(H) - 트리의 높이에 비례하는 재귀 스택 사용
  - 반복: O(H) - 스택 저장공간 필요

# 2. Serialize and Deserialize Binary Tree

## 문제 설명
이진 트리를 직렬화(문자열로 변환)하고 역직렬화(문자열에서 트리로 복원)하는 방법을 구현하는 문제입니다.

## 접근 방법
레벨 순회(BFS) 또는 전위 순회(DFS)를 사용하여 트리를 직렬화하고 역직렬화할 수 있습니다. 여기서는 전위 순회 방식으로 구현합니다.

```python
class Codec:
    def serialize(self, root):
        """
        트리를 문자열로 직렬화
        """
        if not root:
            return "None,"
        
        # 전위 순회 방식으로 직렬화 (루트-왼쪽-오른쪽)
        return str(root.val) + "," + self.serialize(root.left) + self.serialize(root.right)
    
    def deserialize(self, data):
        """
        문자열에서 트리를 복원
        """
        def dfs(values):
            val = values.pop(0)
            if val == "None":
                return None
            
            root = TreeNode(int(val))
            root.left = dfs(values)
            root.right = dfs(values)
            return root
        
        values = data.split(',')
        return dfs(values)
```

### 직렬화 과정
1. 노드가 없으면 "None,"을 반환
2. 있으면 현재 노드의 값과 왼쪽/오른쪽 서브트리의 직렬화 결과를 결합

### 역직렬화 과정
1. 문자열을 쉼표로 분리하여 배열로 변환
2. 배열의 첫 번째 값을 처리하고, 재귀적으로 왼쪽/오른쪽 서브트리를 구성

## 복잡도 분석
- 시간 복잡도: O(N) - 모든 노드를 한 번씩 방문
- 공간 복잡도: O(N) - 직렬화된 문자열과 재귀 호출 스택 공간

# 3. Kth Smallest Element in a BST

## 문제 설명
이진 검색 트리(BST)의 루트와 정수 k가 주어졌을 때, BST에서 k번째로 작은 값을 찾는 문제입니다.

## 접근 방법
BST에서 중위 순회를 하면 노드들이 오름차순으로 방문됩니다. 따라서 중위 순회를 하면서 k번째 방문하는 노드를 찾으면 됩니다.

### 방법 1: 재귀적 접근

```python
def kthSmallest(root, k):
    result = []
    
    def inorder(node):
        if not node or len(result) >= k:
            return
        
        inorder(node.left)
        
        result.append(node.val)
        if len(result) == k:
            return
        
        inorder(node.right)
    
    inorder(root)
    return result[k-1]
```

### 방법 2: 반복적 접근 (더 효율적)

```python
def kthSmallest(root, k):
    stack = []
    current = root
    count = 0
    
    while current or stack:
        # 왼쪽 자식들을 모두 스택에 추가
        while current:
            stack.append(current)
            current = current.left
        
        current = stack.pop()
        count += 1
        
        # k번째 요소를 찾으면 반환
        if count == k:
            return current.val
        
        current = current.right
    
    return -1  # k가 트리의 노드 수보다 큰 경우
```

## 복잡도 분석
- 시간 복잡도: O(H + k) - H는 트리의 높이, k번째 요소를 찾을 때까지만 탐색
- 공간 복잡도: O(H) - 스택 공간은 트리의 높이에 비례

## 최적화 가능성
- 각 노드에 왼쪽 서브트리의 노드 수를 저장하면, O(H) 시간 내에 k번째 요소를 찾을 수 있습니다.
- 예를 들어, 루트의 왼쪽 서브트리에 n개의 노드가 있다면:
  - k <= n이면 왼쪽 서브트리에서 k번째 작은 값 찾기
  - k == n+1이면 루트 값 반환
  - k > n+1이면 오른쪽 서브트리에서 (k-n-1)번째 작은 값 찾기
