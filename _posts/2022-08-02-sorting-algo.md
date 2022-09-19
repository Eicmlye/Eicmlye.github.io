---
layout:         post
title:          "排序算法模板"
subtitle:   	
post-date:      2022-08-02
update-date:    2022-09-15
author:         "Eicmlye"
header-img:     "img/em-post/20220802-SortAlgo.jpg"
catalog:        true
tags: # for multiple tags, tabs should be replaced by spaces before '-';
    - 算法
    - 排序
---

#### 0. 数据结构

本文中用到的数据结构定义为

```cpp
template <typename DataType>
struct LNode {
	DataType data;
	LNode<DataType>* next;

	LNode() : next(nullptr) { memset(&data, 0, sizeof(DataType)); };
	LNode(DataType val) : data(val), next(nullptr) {};
	LNode(DataType val, LNode<DataType>* p) : data(val), next(p) {};
};

template <typename DataType>
using LinkedList = LNode<DataType>*;

template <typename DataType, typename ListNodeType>
ListNodeType* createSingleList(vector<DataType> initData, bool hasHead = true, bool isCyclic = false)
{
    ListNodeType* list = new ListNodeType;
    ListNodeType* newNode = nullptr;

    if (!hasHead) {
        list->data = initData.at(0);
    }
    if (isCyclic) {
        list->next = list;
    }

    for (size_t index = 0; index < ((hasHead) ? initData.size() : initData.size() - 1); ++index) {
        newNode = new ListNodeType(initData.at(initData.size() - 1 - index), list->next);
        list->next = newNode;
    }

    return list;
}
```

#### 1. 插入排序

##### 1.1. 直接插入排序

```cpp
void insertSort(int* list, size_t size)
{
	for (size_t index = 0; index < size; ++index) {
		size_t jndex = 0;
		int cache = list[index];

		/* find inserting pos */
		while (jndex < index) {
			if (list[index] > list[jndex]) {
				++jndex;
			}
			else {
				break;
			}
		}

		/* shift */
		for (size_t kndex = index; kndex > jndex; --kndex) {
			list[kndex] = list[kndex - 1];
		}
		/* insert */
		list[jndex] = cache;
	}

	return;
}
```

##### 1.2. 二路插入排序

```cpp
void insertSort_2part(int* list, size_t size)
{
	if (size == 1) {
		return;
	}

	// now head and tail will not be identical when sorted;
	size_t head = 0;
	size_t tail = 0;
	int* cache = new int[size];

	// init;
	cache[0] = list[0];

	// sorting;
	for (size_t index = 1; index < size; ++index) {
		if (list[index] <= cache[head]) {
			head = (head + size - 1) % size; // avoid overflow;
			cache[head] = list[index];
		}
		else if (list[index] >= cache[tail]) {
			++tail; // tail will never overflow;
			cache[tail] = list[index];
		}
		else {
			// find insert pos;
			size_t jndex = 0;
			for (jndex = head; cache[jndex] < list[index]; jndex = (jndex + 1) % size);
			// shift;
			++tail;
			for (size_t kndex = tail; kndex != jndex; kndex = (kndex + size - 1) % size) {
				cache[kndex] = cache[(kndex + size - 1) % size];
			}
			// insert;
			cache[jndex] = list[index];
		}
	}

	// copy;
	for (size_t index = head; index != tail; index = (index + 1) % size) {
		list[(index + size - head) % size] = cache[index];
	}

	return;
}
```

#### 2. 交换排序

##### 2.1. 冒泡排序

```cpp
void bubbleSort(int* list, size_t size)
{
	for (size_t index = size - 1; index > 0; --index) {
		for (size_t jndex = 0; jndex < index; ++jndex) {
			if (list[jndex] > list[jndex + 1]) {
				list[jndex] += list[jndex + 1];
				list[jndex + 1] = list[jndex] - list[jndex + 1];
				list[jndex] -= list[jndex + 1];
			}
		}
	}

	return;
}
```

##### 2.2. 快速排序

###### 2.2.1. 从数据中随机选取枢轴量

该算法要求枢轴量必须为数据集中已存在的量, 因为递归的依据是每次划分将枢轴量放到正确的位置上.

**注意**: 由于快排的不稳定性, 实际业务使用随机枢轴量容易导致 bug 无法复现的问题, 可以考虑三点取中或九点取中.

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

###### 2.2.2. 任意选取枢轴量

该算法允许枢轴量不为数据集中的值（如可选取平均值为枢轴量）.

```cpp
void partition(int* list, size_t& head, size_t& tail)
{
	// get pivot;
	int pivot = getPivot();

	// partitioning;
	while (head < tail) {
		while (head <= tail && list[tail] > pivot) {
			--tail;
		}

		while (head <= tail && list[head] < pivot) {
			++head;
		}

		if (head < tail) {
			int cache = list[tail];
			list[tail] = list[head];
			list[head] = cache;
			++head;
			--tail;
		}
	}

	return;
}

void quickSort(int* list, size_t head, size_t tail)
{
	if (head < tail) {
		size_t backHead = head;
		size_t frontTail = tail;
		partition(list, backHead, frontTail);

		if (head < frontTail) {
			quickSort(list, head, frontTail);
		}
		if (backHead < tail) {
			quickSort(list, backHead, tail);
		}
	}

	return;
}
```

#### 3. 选择排序

##### 3.1. 堆排序

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

#### 4. 归并排序

##### 4.1. 二路归并

##### 4.2. 分段二路归并

该算法先将原数据集分为若干有序段, 再对这些数据段进行归并. 该算法是针对**链表**设计的. 

```cpp
LNode<int>** buildPList(LinkedList<int>& list, size_t& len)
{
    if (len == 0) {
        return nullptr; 
    }

    LNode<int>* pre = list; 
    LNode<int>* mov = list->next; 
    LNode<int>** pList = new LNode<int>*[len];
    len = 0; 

    pList[len] = mov;
    pre = pre->next; 
    mov = mov->next;
    ++len;

    while (mov != nullptr) {
        if (pre->data > mov->data) {
            pList[len] = mov;
            ++len;
        }

        pre = pre->next;
        mov = mov->next;
    }

    return pList; 
}

void mergeSort(LinkedList<int>& list, size_t n)
{
    size_t len = n; 
    LNode<int>** pList = buildPList(list, len); 
    LNode<int>** cacheList = nullptr; 
    LNode<int>* head = nullptr; 
    LNode<int>* rear = nullptr; 

    size_t index = 0; 
    while (len != 1) {
        index = 0; 
        cacheList = new LNode<int>*[len / 2 + len % 2];
        while (2 * index + 1 < len) {
            size_t jndex = 0;
            size_t kndex = 0;
            head = new LNode<int>; 
            rear = head;
            LNode<int>* mov1 = pList[2 * index];
            LNode<int>* mov2 = pList[2 * index + 1];

            // merge pList[2 * index] and pList[2 * index + 1]; 
            while (mov1 != nullptr && mov1 != pList[2 * index + 1] && mov2 != nullptr && mov2 != pList[2 * index + 2]) {
                if (mov1->data <= mov2->data) {
                    rear->next = new LNode<int>; 
                    rear->next->data = mov1->data;
                    mov1 = mov1->next; 
                    rear = rear->next; 
                }
                else {
                    rear->next = new LNode<int>;
                    rear->next->data = mov2->data;
                    mov2 = mov2->next;
                    rear = rear->next;
                }
            }
            while (mov1 != nullptr && mov1 != pList[2 * index + 1]) {
                rear->next = new LNode<int>;
                rear->next->data = mov1->data;
                mov1 = mov1->next;
                rear = rear->next;
            }
            while (mov2 != nullptr && mov2 != pList[2 * index + 2]) {
                rear->next = new LNode<int>;
                rear->next->data = mov2->data;
                mov2 = mov2->next;
                rear = rear->next;
            }

            // update new section to cache; 
            cacheList[index] = head->next; 
            delete head; 

            ++index; 
        }
        if (2 * index < len) {
            cacheList[index] = pList[2 * index];
        }

        // update len; 
        len = len / 2 + len % 2;

        // update cache to pList; 
        delete[] pList; 
        pList = new LNode<int>*[len];
        for (size_t qndex = 0; qndex < len; ++qndex) {
            pList[qndex] = cacheList[qndex];
        }
        delete[] cacheList; 
    }

    // update pList to list; 
    for (size_t qndex = 0; qndex < len; ++qndex) {
        list[qndex] = pList[0][qndex];
    }

    delete[] pList; 
    return; 
}
```

#### 5. 基数排序
