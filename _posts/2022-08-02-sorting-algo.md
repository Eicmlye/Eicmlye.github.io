---
layout:         post
title:          "排序算法模板"
subtitle:   	"快速排序和堆排序"
post-date:      2022-08-02
update-date:    2022-08-04
author:         "Eicmlye"
header-img:     "img/em-post/20220802-SortAlgo.jpg"
catalog:        true
tags:
    - 算法
	- 排序
---

##### 1. 快速排序

```cpp
/* qkSort.h */

#ifndef QKSORT_H_
#define QKSORT_H_

namespace QKSORT_
{
	template <typename DataType = int>
	static size_t partition(DataType* arr, size_t head, size_t tail, bool (*isnotlt)(DataType, DataType));

	template <typename DataType = int>
	static void quickSort(DataType* arr, size_t head, size_t tail, bool (*isnotlt)(DataType, DataType));

	template <typename DataType = int>
	void qksort(DataType* arr, size_t size, bool (*isnotlt)(DataType, DataType) = [](DataType left, DataType right) -> bool { return left >= right; });
}

// The definition and declaration
// of template functions or
// functions in template classes
// must be in the same file;
#include "qkSort.hpp"

#endif
```

```cpp
/* qkSort.hpp */

#ifdef QKSORT_H_
#include <iostream>
namespace QKSORT_

{
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
}
#endif
```

##### 2. 堆排序

```cpp
/* heapSort.h */

#ifndef HEAPSORT_H_
#define HEAPSORT_H_

namespace HPSORT_
{
	template <typename DataType = int>
	void heapify(DataType* arr, size_t ind, size_t heapSize, bool (*isnotlt)(DataType, DataType));

	// heap sort API;
	template <typename DataType = int>
	void heapSort(DataType* arr, size_t size, size_t heapSize, bool (*isnotlt)(DataType, DataType) = [](DataType left, DataType right) -> bool { return left >= right; });
}

// The definition and declaration
// of template functions or
// functions in template classes
// must be in the same file;
#include "heapSort.hpp"

#endif
```

```cpp
/* heapSort.hpp */
#ifdef HEAPSORT_H_

namespace HPSORT_
{
    // arr: the heap followed by unheapified elements;
    // ind: the node to be examined;
    // heapSize: the size of the heap;
    template <typename DataType>
    void heapify(DataType* arr, size_t ind, size_t heapSize, bool (*isnotlt)(DataType, DataType))
    {
        if (2 * ind + 1 >= heapSize) { // if leaf;
            return;
        }
        // check if rchild exists;
        // Notice that the data and the heap share the same array,
        // so one should avoid
        // misswaping single-child node with elements outside the heap;
		bool flag = false; // true for "the max child is lchild";
        if (2 * ind + 2 >= heapSize) { // if has no rchild;
            flag = true;
        }
		else {
        	flag = isnotlt(arr[2 * ind + 1], arr[2 * ind + 2]);
		}

        // find max child;
        size_t maxChildInd = flag ? 2 * ind + 1 : 2 * ind + 2;

        if (!isnotlt(arr[ind], arr[maxChildInd])) { // if non-heap, swap parent and max child;
            arr[ind] += arr[maxChildInd];
            arr[maxChildInd] = arr[ind] - arr[maxChildInd];
            arr[ind] -= arr[maxChildInd];

            // examine child;
            heapify(arr, maxChildInd, heapSize, isnotlt);
        }

        return;
    }

    // heap sort API; find the 1st-(heapSize)th min elements;
    // arr: the heap followed by unheapified elements;
    // size: the size of arr;
    // heapSize: the size of the heap;
    template <typename DataType>
    void heapSort(DataType* arr, size_t size, size_t heapSize, bool (*isnotlt)(DataType, DataType))
    {
        // initialize heap;
        for (size_t index = 0; index < heapSize; ++index) {
            heapify(arr, heapSize - index - 1, heapSize, isnotlt);
        }

        // sorting;
        for (size_t index = heapSize; index < size; ++index) {
            if (!isnotlt(arr[index], arr[0])) {
                arr[0] += arr[index];
                arr[index] = arr[0] - arr[index];
                arr[0] -= arr[index];

                for (size_t jndex = 0; jndex < heapSize; ++jndex) {
                    heapify(arr, heapSize - jndex - 1, heapSize, isnotlt);
                }
            }
        }

        return;
    }
}
#endif
```
