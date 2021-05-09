---
layout: post
title: MergeSort의 성능을 개선하는 방법
description: >
  Mergesort의 성능을 개선하는 방법을 알아보자. 특히 Switching sort!
sitemap: false
hide_last_modified: true
tags: []
---

얼마 전 자료구조 시험을 봤는데 시험지를 앞에 딱 제출하는 순간 풀이가 떠오른 문제가 있다. 내 기준에서는 꽤 흥미로운 알고리즘이라고 생각되서 기억이 살아있을 때 정리해놓고자 이 포스팅을 적는다. 내 15점 잘가😇 시험이 흥미로웠으니 되었다... ~~흥미롭지만... 그래도 교수님 미워요 엉엉~~
{:.message}

## 일반적으로 배우는 MergeSort

MergeSort의 경우 Average case와 Worst case time complexity 둘 모두 $$\Omega(nlogn)$$이기 때문에 Average case $$\Omega(n^2)$$ 인 기초적인 정렬 방식(BubbleSort, Selection Sort, InsertionSort)에 비해 우수한 성능을 보인다. 뿐만 아니라 QuickSort의 경우 Average case에서는 매우 뛰어난 성능을 보이지만 Worst case의 경우 $$\Omega(n^2)$$의 시간 복잡도를 가지기 때문에 최악의 경우를 고려했을 때 MergeSort는 꽤 매력적인 정렬 방식이다. 이번 포스팅에서는 MergeSort의 성능을 더욱 높일 수 있는 방법을 알아보자.

```java
// stably merge a[lo .. mid] with a[mid+1 ..hi] using aux[lo .. hi]
    private static void merge(Comparable[] a, Comparable[] aux, int lo, int mid, int hi) {
        // precondition: a[lo .. mid] and a[mid+1 .. hi] are sorted subarrays
        assert isSorted(a, lo, mid);
        assert isSorted(a, mid+1, hi);

        // copy to aux[]
        for (int k = lo; k <= hi; k++) {
            aux[k] = a[k];
        }

        // merge back to a[]
        int i = lo, j = mid+1;
        for (int k = lo; k <= hi; k++) {
            if      (i > mid)              a[k] = aux[j++];
            else if (j > hi)               a[k] = aux[i++];
            else if (less(aux[j], aux[i])) a[k] = aux[j++];
            else                           a[k] = aux[i++];
        }

        // postcondition: a[lo .. hi] is sorted
        assert isSorted(a, lo, hi);
    }

    // mergesort a[lo..hi] using auxiliary array aux[lo..hi]
    private static void sort(Comparable[] a, Comparable[] aux, int lo, int hi) {
        if (hi <= lo) return;
        int mid = lo + (hi - lo) / 2;
        sort(a, aux, lo, mid);
        sort(a, aux, mid + 1, hi);
        merge(a, aux, lo, mid, hi);
    }
```

### 방법 1. InsertionSort for small subarrays!

보편적으로 배우는 MergeSort의 경우 base case인 $$N = 1$$을 제외하고 모든 subarray에 대해서 재귀 호출을 시행한다. 이를 **subarray가 일정 크기 이하가 되면 재귀 호출 대신 InsertionSort를 사용하는 방식으로 변경** 시 (참고 자료에 따르면) 속도를 10-15% 정도 감축할 수 있다고 한다. InsertionSort의 경우 평균적으로 $$\Omega(n^2)$$의 시간복잡도를 가지고 MergeSort의 경우 $$\Omega(nlogn)$$의 시간복잡도를 가지기 때문에 크기가 큰 input의 경우 MergeSort가 더 우수한 성능을 보이지만, 크기가 작은 input의 경우 오히려 InsertionSort를 쓰는 것이 이득일 수 있다.

### 방법 2. Test whether array is already in order!

**`Merge()`를 재귀적으로 호출하기 전에 subarray가 정렬되었는지의 여부를 우선적으로 확인**하고 `a[mid] <= a[mid+1]` 일 경우 `merge()`의 호출을 생략한다. 이를 통해서 재귀적 호출은 여전히 일어나지만 sorted subarray에 대한 running time을 선형으로 개선할 수 있다. 경우 재귀적 호출은 여전히 일어나지만

### 방법 3. Eliminate the copy to the auxiliary array!

시험 문제에 나왔던 알고리즘! ~~물론 내 답지는 반쯤 완성되다 말았다...~~ 이 방식을 통해서는 병합 과정에서 auxiliary array를 복제하기 위해 소요되는 시간을 제거할 수 있다. 이 방법에서는 두 가지 sort method가 필요하다. 한 방법의 경우 ** gived array에서 input을 받아 auxiliary array에 집어넣고**, 또 다른 방법의 경우 **auxiliary array에서 input을 받아 sorted output을 given array에 집어넣는다.**

## 개선된 MergeSort

세 가지 개선 방법을 도입한 코드는 아래와 같다.
전체 코드는 출처에서 확인할 수 있다.

```java
 private static void merge(Comparable[] src, Comparable[] dst, int lo, int mid, int hi) {

        // precondition: src[lo .. mid] and src[mid+1 .. hi] are sorted subarrays
        assert isSorted(src, lo, mid);
        assert isSorted(src, mid+1, hi);

        int i = lo, j = mid+1;
        for (int k = lo; k <= hi; k++) {
            if      (i > mid)              dst[k] = src[j++];
            else if (j > hi)               dst[k] = src[i++];
            else if (less(src[j], src[i])) dst[k] = src[j++];   // to ensure stability
            else                           dst[k] = src[i++];
        }

        // postcondition: dst[lo .. hi] is sorted subarray
        assert isSorted(dst, lo, hi);
    }

    private static void sort(Comparable[] src, Comparable[] dst, int lo, int hi) {
        // if (hi <= lo) return;
        if (hi <= lo + CUTOFF) {
            insertionSort(dst, lo, hi);
            return;
        }
        int mid = lo + (hi - lo) / 2;
        sort(dst, src, lo, mid);
        sort(dst, src, mid+1, hi);

        // if (!less(src[mid+1], src[mid])) {
        //    for (int i = lo; i <= hi; i++) dst[i] = src[i];
        //    return;
        // }

        // using System.arraycopy() is a bit faster than the above loop
        if (!less(src[mid+1], src[mid])) {
            System.arraycopy(src, lo, dst, lo, hi - lo + 1);
            return;
        }

        merge(src, dst, lo, mid, hi);
    }
```

## Reference

전체적으로 [Princeton CSE](https://algs4.cs.princeton.edu/22mergesort/)의 글을 참고했다. 사실 쓰다보니 거의 번역에 가까워졌는데, 들어가보면 sorting 자체에 대한 시각화도 흥미롭게 되어있어서 한번 쯤 읽어볼 만한 글이라고 생각된다!
