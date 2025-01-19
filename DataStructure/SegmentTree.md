## 1️⃣ **세그먼트 트리란?**

세그먼트 트리는 특정 범위에 대한 연산(예: 합, 최소값, 최대값)을 효율적으로 수행하기 위한 완전 이진 트리 기반 자료구조. 원래 배열의 구간 데이터를 저장하고 처리하는 데 사용됨.

- 시간 복잡도:
  - **빌드**: O(n)
  - **쿼리**: O(logn)
  - **업데이트**: O(logn)

## 2️⃣ **세그먼트 트리의 구조**

- 트리는 1차원 배열을 기반으로 구현됨.
- 각 노드는 특정 구간을 대표
  - 리프 노드: 배열의 개별 원소를 나타냄.
  - 내부 노드: 자식 노드의 구간 값을 결합한 결과 저장.

### 예시

- 배열: [1, 3, 5, 7, 9, 11]
- 세그먼트 트리(구간 합):

```markdown
                  36
               /     \
             9         27
           /   \      /    \
         4      5   16     11
        / \         / \
       1   3      7   9
```

## 3️⃣ **구현**

### **(1) 트리 빌드**

- 입력 배열을 기반으로 트리를 구성
- **리프 노드**: 배열의 원소를 저장.
- **내부 노드**: 자식 노드의 값을 결합한 결과 저장.

```cpp
void buildTree(vector<int> &arr, vector<int> &tree, int node, int start, int end) {
    if (start == end) { // 리프 노드
        tree[node] = arr[start];
    } else {
        int mid = (start + end) / 2;
        buildTree(arr, tree, 2 * node, start, mid); // 왼쪽 자식
        buildTree(arr, tree, 2 * node + 1, mid + 1, end); // 오른쪽 자식
        tree[node] = tree[2 * node] + tree[2 * node + 1]; // 구간 합
    }
}
```

### **(2) 쿼리**

- **구간 정보 계산**
  특정 구간 `[l, r]`에 대한 합, 최소값, 최대값 등을 계산함.
- **조건에 따라 탐색 최적화**
  - **완전히 포함된 경우**: 계산 결과를 바로 반환.
  - **겹치지 않는 경우**: 해당 구간을 무시하고 탐색 종료.
  - **일부만 겹치는 경우**: 구간을 분할하여 필요한 부분만 탐색.

```cpp
int query(vector<int> &tree, int node, int start, int end, int l, int r) {
    if (r < start || end < l) { // 완전히 겹치지 않음
        return 0;
    }
    if (l <= start && end <= r) { // 완전히 포함됨
        return tree[node];
    }
    int mid = (start + end) / 2;
    int left_sum = query(tree, 2 * node, start, mid, l, r); // 왼쪽 자식
    int right_sum = query(tree, 2 * node + 1, mid + 1, end, l, r); // 오른쪽 자식
    return left_sum + right_sum;
}
```

### **(3) 업데이트**

- 특정 원소를 수정한 후 관련 노드 갱신

```cpp
void update(vector<int> &tree, int node, int start, int end, int idx, int val) {
    if (start == end) { // 리프 노드
        tree[node] = val;
    } else {
        int mid = (start + end) / 2;
        if (start <= idx && idx <= mid) { // 왼쪽 자식 갱신
            update(tree, 2 * node, start, mid, idx, val);
        } else { // 오른쪽 자식 갱신
            update(tree, 2 * node + 1, mid + 1, end, idx, val);
        }
        tree[node] = tree[2 * node] + tree[2 * node + 1]; // 부모 노드 갱신
    }
}
```

### 4️⃣ **세그먼트 트리의 장단점**

- **장점**:
  - 빠른 구간 연산 수행 (O(logn)).
  - 다양한 유형의 구간 연산 지원.
- **단점**:
  - 메모리 사용량이 큼(4n).
  - 간단한 문제에서는 구현 부담이 클 수 있음.
