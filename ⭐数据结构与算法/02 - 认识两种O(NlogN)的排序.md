# 2025年10月22日

## 递归
### master公式计算递归的时间复杂度
- master公式
$$
\begin{align}
T(N)=a\times T(\frac{N}{b}) + O(N^d) 
\\\\
\begin{cases}
&\log_{b}a<d && O(N^d)
\\
&\log_{b}a>d && O(N^{\log_{b}a})
\\
&\log_{b}a==d && O(N^d\times logN)
\end{cases}

\end{align}
$$


## 归并排序
- 相当于进栈。
- 空间复杂度$O(N)$。
```js
    function mergeSort(arr, L = 0, R = arr.length - 1) {
      if (L >= R) return;
      const mid = L + ((R - L) >> 1);
      mergeSort(arr, L, mid);
      mergeSort(arr, mid + 1, R);
      mergeTwoParts(arr, L, mid, R);
    }

    function mergeTwoParts(arr, L, M, R) {
      const help = [];
      let i = 0;
      let p1 = L;
      let p2 = M + 1;
      while (p1 <= M && p2 <= R) {
        help[i++] = arr[p1] < arr[p2] ? arr[p1++] : arr[p2++];
      }
      while (p1 <= M) help[i++] = arr[p1++];
      while (p2 <= R) help[i++] = arr[p2++];
      for (let i = 0; i < help.length; i++) {
        arr[L + i] = help[i];
      }
    }
```
#### 求中点
- `mid = (L + R) / 2`，`L + R`可能溢出
- `mid = L + (R - L) /2`
- `mid = L + (R - L) >> 1`，更快

## 小和问题和逆序对
> 小和：一个数组中，每个元素左边比它小的元素之和的总和。
> 逆序对：在一个数组中，满足前面的数大于后面的数的元素对。


```js
    function mergeSmallSum(arr, L, M, R) {
      const help = [];
      let i = 0;
      let p1 = L;
      let p2 = M + 1;
      let res = 0;
      while (p1 <= M && p2 <= R) {
        res += arr[p1] < arr[p2] ? (r - p2 + 1) * arr[p1] : 0;
        help[i++] = arr[p1] < arr[p2] ? arr[p1++] : arr[p2++];
      }
      while (p1 <= M) help[i++] = arr[p1++];
      while (p2 <= R) help[i++] = arr[p2++];
      for (let i = 0; i < help.length; i++) {
        arr[L + i] = help[i];
      }
      return res;
    }
    
    function smallSum(arr) {
      if (arr === null || arr.length < 2) return 0;
      return process(arr, 0, arr.length - 1)
    }
    
    function process(arr, l, r) {
      if (l === r) return 0;
      const mid = l + ((r - l) >> 1);
      return process(arr, l, mid) + process(arr, mid + 1, r) + mergeSmallSum(arr, 1, mid, r)
    }
```


## 利用荷兰国旗问题进行快速排序
### 快排1.0
> 面临坏的划分，时间复杂度依然是$O(n^2)$。
- 【题目】设num，小于等于num放左边，大于num放右边。

### 快排2.0
> 面临坏的划分，时间复杂度依然是$O(n^2)$。
- 【题目】设num，小于num放左边，等于num放中间，大于num放右边。
### 快排3.0(最终版)
> 随机找一个划分，根据数学逻辑，时间复杂度是$O(N\log N)$。
- 空间复杂度$O(\log N)$。
![[Pasted image 20251030125719.png|200]]
```js
    function swap(arr, a, b) {
      if (a == b) return;
      arr[a] = arr[a] ^ arr[b];
      arr[b] = arr[a] ^ arr[b];
      arr[a] = arr[a] ^ arr[b];
    }

    function quickSort(arr, L = 0, R = arr.length - 1) {
      if (L < R) {
        swap(arr, Math.floor(Math.random() * (R - L + 1)) + L, R);
        const p = partition(arr, L, R);
        quickSort(arr, L, p[0] - 1);
        quickSort(arr, p[1] + 1, R);
      }
    }

    function partition(arr, L, R) {
      let less = L - 1;
      let more = R;
      while (L < more) {
        if (arr[L] < arr[R]) {
          swap(arr, ++less, L++);
        } else if (arr[L] > arr[R]) {
          swap(arr, --more, L);
        } else {
          L++;
        }
      }
      swap(arr, more, R)
      return [less + 1, more]
    }
```