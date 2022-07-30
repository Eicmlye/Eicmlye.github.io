---
layout:         post
title:          "几个递归算法的例子"
subtitle:   	"排列与组合"
post-date:      2022-07-28
update-date:    2022-07-30
author:         "Eicmlye"
header-img:     "img/em-post/20220728-DS1800Example.png"
catalog:        true
tags:
    - 算法
---

##### 1. 递归打印 n 元集合 A 中元素的 k 元组合.

```cpp
void recCombination(int* A, size_t n, int* cache, size_t len, size_t k)
{
	if (n >= k) { // k-substring available;
		if (k != 1) { // not at the end of the combination;
			cache[len] = A[0];
			recCombination(A + 1, n - 1, cache, len + 1, k - 1);
		}
		else { // at the end, print the combination;
			// one may output the result in arrays as well; 
			for (size_t index = 0; index < len; ++index) {
				std::cout << cache[index] << ' ';
			}
			std::cout << A[0] << std::endl;
		}

		recCombination(A + 1, n - 1, cache, len, k);
	}

	return;
}
```

##### 2. 递归打印集合元素的所有全排列.

```cpp
void recPermu(int* rem, bool* flag, size_t size, int* arr, size_t len)
{
	if (len == size) { // current permutation ended;
		for (size_t index = 0; index < len; ++index) {
			std::cout << arr[index] << ' ';
		}
		std::cout << std::endl;
	}
	else {
		for (size_t index = 0; index < size; ++index) {
			if (!flag[index]) {
				continue;
			}
			arr[len] = rem[index];
			++len;
			flag[index] = false;

			recPermu(rem, flag, size, arr, len);

			// refresh;
			--len;
			flag[index] = true;
		}
	}

	return;
}
```
