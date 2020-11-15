# 题目地址

https://www.lintcode.com/problem/sort-integers



# 题目描述

Given an integer array, sort it in ascending order. Use selection sort, bubble sort, insertion sort or any O(n2) algorithm.

### Example

```
Example 1:
	Input:  [3, 2, 1, 4, 5]
	Output: [1, 2, 3, 4, 5]
	
	Explanation: 
	return the sorted array.

Example 2:
	Input:  [1, 1, 2, 1, 1]
	Output: [1, 1, 1, 1, 2]
	
	Explanation: 
	return the sorted array.
```





# 思路

+ 归并排序

  时间复杂度O(nlogn), 空间复杂度O(n),<font color = blue>空间复杂度来自temp数组</font>

+ 快速排序

  平均时间复杂度O(nlogn),空间复杂度O(logn)

  最坏时间复杂度O(n<sup>2</sup>),空间复杂度O(n)

  具体内容见[归并排序和快速排序](/Users/parallax/Documents/CS/九章算法课/02 归并排序和快速排序.md)

  

# 题解

### 快速排序

```java
public void sortIntegers(int[] A) {
    // write your code here

    if (A == null || A.length == 0) {
        return;
    }

    quickSort(A, 0, A.length - 1);
}

private void quickSort(int[] A, int start, int end) {

    if (start >= end) {
        return;
    }
    int left = start, right = end;
    int pivot = A[start + (end - start) / 2];

    while (left <= right) {

        while (left <= right && A[left] < pivot) {
            left++;
        }

        while (left <= right && A[right] > pivot) {
            right--;
        }
        if (left <= right) {
            int temp = A[left];
            A[left] = A[right];
            A[right] = temp;
            left++;
            right--;
        }
    }

    quickSort(A, start, right);
    quickSort(A, left, end);
}
```



### 归并排序

```java
public void sortIntegers(int[] A) {
    // write your code here
    int[] temp = new int[A.length];
    if (A == null || A.length == 0) {
        return;
    }

    mergeSort(A, 0, A.length - 1, temp);
}

private void mergeSort(int[] A, int start, int end, int[] temp) {

    if (start >= end) {
        return;
    }

    mergeSort(A, start, start + (end - start) / 2, temp);
    mergeSort(A, start + (end - start) / 2 + 1, end, temp);
    merge(A, start, end, temp);
}

private void merge(int[] A, int start, int end,int[] temp) {
    int mid = start + (end - start) / 2;
    int leftIndex = start;
    int rightIndex = mid + 1;
    int index = start;

    while (leftIndex <= mid && rightIndex <= end) {
        if (A[leftIndex] < A[rightIndex]) {
            temp[index++] = A[leftIndex++];
        } else {
            temp[index++] = A[rightIndex++];
        }
    }

    while (leftIndex <= mid) {
        temp[index++] = A[leftIndex++];
    }

    while (rightIndex <= end) {
        temp[index++] = A[rightIndex++];
    }

    for (int k = start; k <= end; k++) {
        A[k] = temp[k];
    }
}
```

