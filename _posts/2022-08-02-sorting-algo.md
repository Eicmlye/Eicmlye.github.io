---
layout:         post
title:          "排序算法模板"
subtitle:   	"快速排序和堆排序"
post-date:      2022-08-02
update-date:    
author:         "Eicmlye"
header-img:     "img/em-post/20220802-SortAlgo.jpg"
catalog:        true
tags:
    - 算法
---

##### 1. 快速排序

```cpp
/* qkSort.h */

#ifndef QKSORT_H_
#define QKSORT_H_

namespace QKSORT_
{
	template <typename DataType = int>
	static size_t partition(DataType* arr, size_t head, size_t tail, bool (*isnotlt)(DataType, DataType) = [](DataType left, DataType right) -> bool { return left >= right; });
	// The function pointer "isnotlt" must not be "isgtr".
	// Excluding the equivalence situation
	// will result in infinite loop when identical elements exists.

	template <typename DataType = int>
	static void quickSort(DataType* arr, size_t head, size_t tail, bool (*isnotlt)(DataType, DataType) = [](DataType left, DataType right) -> bool { return left >= right; });

	template <typename DataType = int>
	void qksort(DataType* arr, size_t size, bool (*isnotlt)(DataType, DataType) = [](DataType left, DataType right) -> bool { return left >= right; });

	// For functions in template classes
	// or template functions in namespaces,
	// the definition and declaration must be in the same file.
	#include "qkSort.hpp"

}

#endif
```


```cpp
/* qkSort.hpp */

#include <iostream>

template <typename DataType>
size_t partition(DataType* arr, size_t head, size_t tail, bool (*isnotlt)(DataType, DataType))
{
    // randomly choose the pivot to optimize run time;
    size_t pivotInd = head + (size_t)((tail - head) * (rand() / (double)RAND_MAX));
    // swap the pivot element to the head position;
    DataType pivot = arr[pivotInd];
    arr[pivotInd] = arr[head];
    arr[head] = pivot;

    // partitioning;
    while (head < tail) {
        while (head < tail && isnotlt(arr[tail], pivot)) {
            --tail;
        }
        arr[head] = arr[tail];
        while (head < tail && isnotlt(pivot, arr[head])) {
            ++head;
        }
        arr[tail] = arr[head];
    }
    arr[head] = pivot;

    return head;
}

template <typename DataType>
void quickSort(DataType* arr, size_t head, size_t tail, bool (*isnotlt)(DataType, DataType))
{
    if (head < tail) {
        size_t pivotInd = partition<DataType>(arr, head, tail, isnotlt);

        // deal with the front part;
        if (head + 1 < pivotInd) { // to avoid size_t overflow;
            quickSort<DataType>(arr, head, pivotInd - 1, isnotlt);
        }
        // deal with the rear part;
        if (pivotInd < tail - 1) { // to avoid size_t overflow;
            quickSort<DataType>(arr, pivotInd + 1, tail, isnotlt);
        }
    }

    return;
}

template <typename DataType>
void qksort(DataType* arr, size_t size, bool (*isnotlt)(DataType, DataType))
{ // quick sort API;
    quickSort<DataType>(arr, 0, size - 1, isnotlt);

    return;
}
```

##### 2. 堆排序

```cpp
/* heapSort.h */

#ifndef HEAPSORT_H_
#define HEAPSORT_H_

namespace HEAPSORT_H_
{


	// For functions in template classes
	// or template functions in namespaces,
	// the definition and declaration must be in the same file.
	#include "heapSort.hpp"
	
}

#endif
```

```cpp
/* heapSort.hpp */

template <typename DataType>
```
