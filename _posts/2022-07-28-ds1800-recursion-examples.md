---
layout:         post
title:          "几个递归算法的例子"
subtitle:   	"排列与组合"
post-date:      2022-07-28
update-date:    2022-07-31
author:         "Eicmlye"
header-img:     "img/em-post/20220728-DS1800Example.png"
catalog:        true
tags:
    - 算法
---

##### 1. 递归打印 n 元集合 A 中元素的 k 元组合.

```cpp
/*
	A: the set of elements to be combined;
	n: the size of array A;
	cache: memorizing completed part of the current combination;
	len: the size of cache;
	k: number of elements to be combined to the current combination;

	Dynamic Programming:
		To find all the k-combinations,
		one should find all the (k-1)-combinations leaded by all possible elements,
		i.e. the first n-k+1 elements;
*/
void recCombi(int* A, size_t n, int* cache, size_t len, size_t k)
{
	if (n >= k) { // if k-substring available, i.e. "possible leading elements";
		if (k != 1) { // if not the last element of the current combination;
			// push a new element into the current combination;
			cache[len] = A[0];
			// go to the subcombination;
			recCombi(A + 1, n - 1, cache, len + 1, k - 1);
		}
		else { // if at the end, output the combination;
				// one may copy and output the array itself as well;
			for (size_t index = 0; index < len; ++index) {
				std::cout << cache[index] << ' ';
			}
			std::cout << A[0] << std::endl;
		}

		recCombi(A + 1, n - 1, cache, len, k); // go to the next leading element;
	}

	return;
}
```

##### 2. 递归打印集合元素的所有全排列.

```cpp
/*
	A: the set of elements to be permuted;
	flag: true if the element is already in the current permutation;
	size: the size of A;
	arr: the current permutation;
	len: the size of arr;
*/

void recPermu(int* A, bool* flag, size_t size, int* arr, size_t len)
{
	if (len == size) { // if the current permutation ended, output the permutation;
			// one may copy and output the array itself as well;
		for (size_t index = 0; index < len; ++index) {
			std::cout << arr[index] << ' ';
		}
		std::cout << std::endl;
	}
	else { // if not ended, push a new element;
		for (size_t index = 0; index < size; ++index) {
			// push a new element into the current permutation;
			if (!flag[index]) {
				continue;
			}
			arr[len] = A[index];

			// update flag;
			flag[index] = false;

			recPermu(A, flag, size, arr, len + 1);

			// recover flag, because flag is public for all recursions;
			flag[index] = true;
		}
	}

	return;
}
```

##### 3. 递归打印集合的幂集 (未测试)

```cpp
/*
	** note: '\0' is excluded in all the sizes below;
	
	A: the original set of elements;
	n: the size of A;
	cur: the current permutation;
	len: the size of cur;
	ind: the index of the last element of cur in A;
	P: the powerset;
	size: the size of P;
*/

void recPowerSet(char* A, size_t n, char* cur, size_t len, size_t ind, char** P, size_t size)
{
	// create copy of cur;
	char* newElem = new int[len + 1];
	for (size_t index = 0; index < len; ++index) {
		newElem[index] = cur[index];
	}
	newElem[len] = '\0';
	// push into P;
	P[size] = newElem;
	++size;

	for (size_t index = ind + 1; index < n; ++index) {
		// push a new element into cur;
		cur[len] = A[index];
		recPowerSet(A, n, cur, len + 1, index, P, size);
	}

	return;
}
```
