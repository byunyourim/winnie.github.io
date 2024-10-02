---
layout: post
title: Backtracking 
categories: [Algorithm]
---

**가능한 모든 경우의 수를 탐색하는** 알고리즘 기법  
  
모든 가능한 경우의 수 중에서 특정 조건을 만족하는 경우만 탐색한다. 조건을 만족하지 않으면 이전 단계로 돌아가
다른 해를 탐색한다.
불필요한 경로를 빨리 제거할 수 있어, 효율적이다.  



<br>
![backtracking.png](https://github.com/user-attachments/assets/b756e965-1c06-4de9-9a58-311184ed0136)

- 해를 부분적으로 구성해 나가면서, 해당 해가 유망하지 않은 경우 탐색을 중단하고 이전 단계로 돌아간다.
- 각 단계에서 선택을 하고, 그 선택에 따라 다시 문제를 재귀적으로 탐색한다.
- Pruning 가지치기


<br>


### 동작
1. 가능한 해를 하나씩 만들며 조건을 만족하는지 확인한다.
2. 각 단계에서 유망성을 확인한다.
3. 유효하지 않으면 해당 경로의 탐색을 중단하고 이전 단계로 돌아간다.
4. 유효한 해인 경우 결과에 추가한다.
5. 위의 과정을 반복하여 가능한 모든 경오를 탐색한다.


<br>


## 구현 예시
숫자 n이 주어졌을 때, n개의 쌍으로 된 모든 유효한 괄호 조합을 리턴하는 함수 구현
```java
class Solution_BackTracking {
    public List<String> backtracking(int n) {
        List<String> res = new ArrayList<>();
        backtrack(res, "", 0, 0, n);
        return res;
    }

    private void backtrack(List<String> result, String currentStr, int openCnt, int closeCnt, int max) {
        if (currentStr.length() == max*2) {
            result.add(currentStr);
            return;
        }
        if (openCnt < max) {
            backtrack(result, currentStr + "(", openCnt + 1, closeCnt, max);
        }
        if (closeCnt < openCnt) {
            backtrack(result, currentStr + ")", openCnt, closeCnt + 1, max);
        }
    }
}
```

