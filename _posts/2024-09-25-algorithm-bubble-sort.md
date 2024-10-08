---
layout: post
title: 버블 정렬 Bubble Sort 알고리즘
categories: [Algorithm]
---

정렬 알고리즘 중 하나로,
인접한 두 요소를 비교하고 필요에 따라 자리를 교환하면서 배열을 정렬하는 방식이다.



<br>  

### 동작 방식
![bubble_sort.png](https://github.com/user-attachments/assets/383cb2ab-d46b-44e6-a674-ee114ad7f15b)

1. 배열의 첫 번째 요소와 두 번째 요소를 비교한다.
2. 첫 번째 요소가 더 크면 두 요소와 자리를 바꾼다.
3. 그런 다음 두 번째 요소와 세 번째 요소를 비교한다.
4. 이 과정을 배열 끝까지 반복한다. 한 번의 반복이 끝나면 가장 큰 요소가 배열의 마지막 자리에 위치한다.
5. 두 번째 반복에서는 마지막 요소는 이미 정렬되어있기 때문, 그 전까지만 반복한다.
6. 위의 과정을 배열이 정렬될 때까지 반복한다.

<br><br>

### 구현 
```java
public class 버블정렬 {
    public static void main(String[] args) {
        int[] array = {5, 3, 8, 4, 2};
        bubbleSort(array);
        System.out.println("정렬된 배열: " + Arrays.toString(array));
    }

    private static int[] bubbleSort(int[] array) {
        int n = array.length;
        boolean swapped;  // 교환이 일어나지 않은 경우 루프 종료

        for (int i=0; i<n-1; i++) { // n 번째 회전
            swapped = false;
            for (int j=0; j<n-i-1; j++) {
                if (array[j] > array[j+1]) {
                    int temp = array[j];
                    array[j] = array[j+1];
                    array[j+1] = temp;
                    swapped = true;
                }
            }
            if (!swapped) break;;
        }
        return array;
    }
}
```

<br><br><br>


### 시간 복잡도 
- 최악의 경우: O(n²)  
  – 배열이 역순일 때(완전히 정렬되지 않은 경우) 가장 느리게 작동한다.
- 최선의 경우: O(n)   
  – 배열이 이미 정렬된 상태인 경우, 한 번의 회 후 교환이 발생하지 않기 때문에 바로 종료된다.

<br><br><br>


### 장단점
#### 장점
- 구현이 매우 간단하고 이해하기 쉽다
- 이미 정렬된 배열에 대해서는 효율적으로 동작할 수 있다 (O(n)).

#### 단점:
- 시간 복잡도가 O(n²)으로, 데이터가 많은 경우 성능이 떨어진다.
- 실제 많이 사용되지 않으며, 더 나은 성능의 정렬 알고리즘(퀵 정렬, 병합 정렬 등)이 많이 사용된다.
