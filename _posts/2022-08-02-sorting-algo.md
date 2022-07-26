---
layout:         post
title:          "排序算法模板"
subtitle:   	
post-date:      2022-08-02
update-date:    2022-09-29
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

**注意**: 由于快排的不稳定性, 实际业务使用随机枢轴量容易导致 bug 无法复现的问题, 可以考虑三点取中或九点取中. 将待取值放入数组局部排序输出中间值即可. 以**三点取中**为例: 

```cpp
template <typename DataType>
static size_t findMidInd(DataType* list, size_t head, size_t tail)
{
    DataType arr[3] = { list[head], list[tail], list[head + (tail - head) / 2] };
    size_t ind[3] = { head, tail, head + (tail - head) / 2 };

    for (size_t index = 3; index > 0; --index) {
        for (size_t jndex = 0; jndex < index - 1; ++jndex) {
            if (arr[jndex] > arr[jndex + 1]) {
                arr[jndex] += arr[jndex + 1];
                arr[jndex + 1] = arr[jndex] - arr[jndex + 1];
                arr[jndex] -= arr[jndex + 1];

                ind[jndex] += ind[jndex + 1];
                ind[jndex + 1] = ind[jndex] - ind[jndex + 1];
                ind[jndex] -= ind[jndex + 1];
            }
        }
    }

    return ind[1];
}
```

###### 2.2.2. 任意选取枢轴量

该算法允许枢轴量不为数据集中的值（如可选取平均值为枢轴量）, 但需为数据集最大值和最小值（包括端点）之间的值.

```cpp
void partition(int* list, size_t& head, size_t& tail)
{
    // 特定的枢轴量取法可能导致2个数据分划时无限循环; 
    // 故需单独处理 head + 1 == tail 的情况; 
    if (head + 1 == tail) {
        if (list[head] > list[tail]) {
            list[head] += list[tail]; 
            list[tail] = list[head] - list[tail]; 
            list[head] -= list[tail]; 
        }

        head += tail; 
        tail = head - tail; 
        head -= tail; 

        return; 
    }
    
    // 待分划数据至少为3个时; 
    int pivot = getPivot();

    while (head < tail) {
        while (head <= tail && list[tail] > pivot) {
            --tail;
        }
        while (head <= tail && list[head] < pivot) {
            ++head;
        }

        if (head < tail) {
            int cache = list[head];
            list[head] = list[tail];
            list[tail] = cache;
            --tail;
            ++head;
        }

        // 当探针在端点重合时, 判定重合值分配到哪一半数据中; 
        if (head == tail) {
            if (list[head] < pivot) {
                ++head;
            }
            else {
                --tail;
            }
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

###### 2.2.3. 非递归快速排序

```cpp
void partition(int* list, size_t& head, size_t& tail)
{
    // 单独处理 head + 1 == tail 的情况; 
    if (head + 1 == tail) {
        if (list[head] > list[tail]) {
            list[head] += list[tail]; 
            list[tail] = list[head] - list[tail]; 
            list[head] -= list[tail]; 
        }

        head += tail; 
        tail = head - tail; 
        head -= tail; 
    }
    else {
        int pivot = getPivot();

        while (head < tail) {
            while (head <= tail && list[tail] > pivot) {
                --tail;
            }
            while (head <= tail && list[head] < pivot) {
                ++head;
            }

            if (head < tail) {
                int cache = list[head];
                list[head] = list[tail];
                list[tail] = cache;
                --tail;
                ++head;
            }

            if (head == tail) {
                if (list[head] < pivot) {
                    ++head;
                }
                else {
                    --tail;
                }
            }
        }
    }

    return; 
}

void quickSort(int* list, size_t n)
{
    typedef struct stackNode {
        size_t head = 0; 
        size_t tail = 0; 
        stackNode* next = nullptr; 
    } stackNode, *stack;

    stack stk = new stackNode; 
    stk->next = new stackNode; 
    stk->next->head = 0;
    stk->next->tail = n - 1;

    size_t backHead = 0; 
    size_t frontTail = n - 1;
    stackNode* newNode = nullptr; 
    stackNode* cache = nullptr; 

    while (stk->next != nullptr) {
        // save top node; 
        cache = stk->next;
        // pop top; 
        stk->next = stk->next->next;

        backHead = cache->head;
        frontTail = cache->tail;
        partition(list, backHead, frontTail);

        // 先右半再左半; 
        if (backHead < cache->tail) {
            newNode = new stackNode;
            newNode->head = backHead;
            newNode->tail = cache->tail;

            // push into stack;
            newNode->next = stk->next;
            stk->next = newNode;
        }
        if (cache->head < frontTail) {
            newNode = new stackNode; 
            newNode->head = cache->head; 
            newNode->tail = frontTail; 

            // push into stack;
            newNode->next = stk->next; 
            stk->next = newNode;
        }

        delete cache; 
    }

    return; 
}
```

###### 2.2.4. 快速排序搜索第 `k` 小的元素

```cpp
int qsFind(int* list, size_t head, size_t tail, size_t targetOrder)
{
    if (head <= tail) {
        size_t pivotInd = partition(list, head, tail);

        if (pivotInd == targetOrder - 1) {
            return list[pivotInd]; 
        }
        else if (pivotInd > targetOrder - 1 && pivotInd > head) {
            return qsFind(list, head, pivotInd - 1, targetOrder);
        }
        else if (pivotInd < targetOrder - 1 && pivotInd < tail) {
            return qsFind(list, pivotInd + 1, tail, targetOrder);
        }
    }

    return 0;
}
```

###### 2.2.5. 单链表的快速排序

本质上是按“小于枢轴”“枢轴”“大于枢轴”考虑的荷兰国旗问题, 用移动指针就可以解决. 如果不带头结点, 强行加一个就好了. 

#### 3. 选择排序

##### 3.1. 堆排序

###### 3.1.1. 堆排序

以大顶堆为例, 输出的顺序是降序输出, 但输出结果是升序序列. 首先自顶向下构建大顶堆: 

```cpp
// suppose list[0] to list[listLen - 2] is already heapified; 
// now add new element list[listLen - 1] and heapify list; 
// time complexity O(log listLen), space complexity O(1); 
void heapify(int* list, size_t listLen)
{
    if (listLen == 0) {
        return; 
    }

    // for maxheap, parent > anotherChild; 
    // so list[listLen] > parent is sufficient for exchanging; 
    size_t ind = listLen - 1; 
    while (ind != 0 && list[(ind - 1) / 2] < list[ind]) {
        list[(ind - 1) / 2] += list[ind]; 
        list[ind] = list[(ind - 1) / 2] - list[ind]; 
        list[(ind - 1) / 2] -= list[ind]; 

        ind = (ind - 1) / 2; 
    }

    return; 
}


void buildMaxHeap(int* list, size_t listLen)
{
    for (size_t len = 1; len <= listLen; ++len) {
        heapify(list, len); 
    }

    return; 
}
```

然后不断输出堆顶元素至堆尾, 并将剩余元素重建为大顶堆: 

```cpp
void heapSort(int* list, size_t listLen)
{
    for (size_t index = 0; index < listLen; ++index) {
        // (re)build maxheap; 
        buildMaxHeap(list, listLen - index);

        // output local max element to the end of the heap; 
        int cache = list[0];
        list[0] = list[listLen - index - 1];
        list[listLen - index - 1] = cache;
    }

    return;
}
```

###### 3.1.2. 优化建堆的过程

自底向上的建堆方式的时间复杂度更低（[证明](https://blog.csdn.net/USTCsunyue/article/details/111428684)）: 

```cpp
void heapify(int* list, size_t listLen, size_t root)
{
    while (root < listLen) {
        if (2 * root + 1 >= listLen) {
            return; 
        }

        size_t lchildInd = 2 * root + 1; 
        size_t rchildInd = lchildInd + 1; 
        size_t maxChildInd = (list[lchildInd] < list[(rchildInd < listLen) ? rchildInd : lchildInd]) ? rchildInd : lchildInd;

        if (list[root] >= list[maxChildInd]) {
            return; 
        }

        list[root] += list[maxChildInd];
        list[maxChildInd] = list[root] - list[maxChildInd];
        list[root] -= list[maxChildInd];

        root = maxChildInd; 
    }

    return; 
}

void buildMaxHeap(int* list, size_t listLen) 
{
    size_t index = listLen / 2; // the first leaf node; 

    do {
        --index; 

        heapify(list, listLen, index); 

    } while (index != 0); 

    return; 
}
```

注意到除第一次新建堆外, 重建堆的过程实际上只需移动新的堆顶元素, 故可以在重建时直接追踪新的堆顶. 此时如果仍用原来的 `buildMaxHeap` , 其复杂度也会退化为 `O(log listLen)` , 因为只有涉及到新堆顶元素节点时才需要交换. 

最终得到的堆排序如下

```cpp
void heapSort(int* list, size_t listLen)
{
    // build maxheap; 
    buildMaxHeap(list, listLen);

    for (size_t index = 0; index < listLen; ++index) {
        // output local max element to the end of the heap; 
        int cache = list[0];
        list[0] = list[listLen - index - 1];
        list[listLen - index - 1] = cache;

        heapify(list, listLen - index - 1, 0);
    }

    return;
}
```

###### 3.1.3. 搜索第 `k` 小的元素

这里沿用[3.1.1.](#311-堆排序)节中的建堆过程 `buildMaxHeap()` 和[3.1.2.](#312-优化建堆的过程)节中的重建过程 `rebuildMaxHeap()` . 

```cpp
// k: the function will find the k-th minimal element; 
int hsFind(int* list, size_t listLen, size_t k)
{
    if (listLen < k) {
        throw std::bad_alloc(); 
    }

    buildMaxHeap(list, k);

    for (size_t index = k; index < listLen; ++index) {
        if (list[index] >= list[0]) {
            continue;
        }

        int cache = list[0];
        list[0] = list[index];
        list[index] = cache;

        heapify(list, k, 0);
    }

    return list[0];
}
```

#### 4. 归并排序

##### 4.1. 二路归并

##### 4.2. 分段二路归并

该算法先将原数据集分为若干有序段, 再对这些数据段进行归并. 该实现是针对**链表**设计的. 

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

基数排序不依赖于比较和交换, 常用于链表排序. 

```cpp
// sortDigit: usually means the max number of digits an element can have; 
// base: the base system in use; 
void radixSort(LinkedList<unsigned int> list, size_t sortDigit, size_t base = 10)
{
    // init buckets; 
    LinkedList<unsigned int>* buckets = new LinkedList<unsigned int>[base];
    LNode<unsigned int>** rears = new LNode<unsigned int>*[base];
    for (size_t index = 0; index < base; ++index) {
        buckets[index] = new LNode<unsigned int>;
        rears[index] = buckets[index]; 
    }

    size_t countSortedDigit = 0; 
    while (countSortedDigit < sortDigit) {
        while (list->next != nullptr) {
            // find the digit to be sorted; 
            size_t tarDigit = list->next->data; 
            for (size_t index = 0; index < countSortedDigit; ++index) {
                tarDigit /= base;
            }
            tarDigit %= base;

            // push into corresponding queue; 
            rears[tarDigit]->next = list->next;

            list->next = list->next->next;
            rears[tarDigit] = rears[tarDigit]->next;
            
            rears[tarDigit]->next = nullptr; 
        }

        // recombine list; 
        LNode<unsigned int>* rear = list; 
        for (size_t index = 0; index < base; ++index) {
            rear->next = buckets[index]->next;
            if (rear->next != nullptr) {
                rear = rears[index]; 
            }
        }

        // clear buckets and rears; 
        for (size_t index = 0; index < base; ++index) {
            buckets[index]->next = nullptr;
            rears[index] = buckets[index];
        }

        ++countSortedDigit; 
    }



    // del buckets and rears; 
    delete[] rears; 
    for (size_t index = 0; index < base; ++index) {
        delete buckets[index]; 
    }
    delete[] buckets; 

    return; 
}
```

#### 附录

##### 重要内容更新日志

2022-09-22 标准化了快速排序三点取中指标函数 `findMidInd` .

2022-09-24 增加了[堆排序](#311-堆排序)并进行了[简单优化](#312-优化建堆的过程), 利用新的堆排序模块重写了[搜索第 `k` 小的元素](#313-搜索第-k-小的元素)的方法. 

2022-09-25 增加了自底向上的堆排序, 并修改了其它相关方法的实现. 

2022-09-29 增加了[基数排序](#5-基数排序). 