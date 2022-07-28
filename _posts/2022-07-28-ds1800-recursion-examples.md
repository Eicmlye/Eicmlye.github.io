---
layout:         post
title:          "几个递归算法的例子"
subtitle:   	"排列、组合和 Ackermann 函数栈递归"
post-date:      2022-07-28
update-date:    
author:         "Eicmlye"
header-img:     "img/em-post/20220728-DS1800Example.png"
catalog:        true
tags:
    - 算法
---

1. 递归打印 n 元集合 A 中元素的 k 元组合.

	```cpp
	void recCombination(int* A, size_t n, int* cache, size_t len, size_t k)
	{
		if (n >= k) { // k-substring available;
			if (k != 1) { // not at the end of the combination;
				cache[len] = A[0];
				recCombination(A + 1, n - 1, cache, len + 1, k - 1);
			}
			else { // at the end, print the combination;
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
2. 递归打印集合元素的所有全排列.

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
3. 栈递归求解 Ackermann 函数.

	```cpp
	unsigned int recAck(unsigned int m, unsigned int n)
	{
		if (m == 0) {
			return n + 1;
		}
		// now m != 0;
		if (n == 0) {
			return recAck(m - 1, 1);
		}

		return recAck(m - 1, recAck(m, n - 1));
	}

	int Ack(int m, int n)
	{
		/* stack node definition */
		struct {
			int mval = -1;
			int nval = -1;
			int ack = -1;
		} stk[1000];

		/* target ack initialization */
		size_t top = 0;
		stk[top].mval = m;
		stk[top].nval = n;

		while (stk[0].ack == -1) {
			if (stk[top].ack == -1) { // not yet computed;
				if (stk[top].mval == 0) {
					stk[top].ack = stk[top].nval + 1;
				}
				else if (stk[top].nval == 0) {
					++top;
					stk[top].mval = stk[top - 1].mval - 1;
					stk[top].nval = 1;
					stk[top].ack = -1;
				}
				else {
					++top;
					stk[top].mval = stk[top - 1].mval - 1;
					stk[top].nval = -1;
					stk[top].ack = -1;

					++top;
					stk[top].mval = stk[top - 2].mval;
					stk[top].nval = stk[top - 2].nval - 1;
					stk[top].ack = -1;
				}
			}
			else { // computation done;
				if (stk[top - 1].nval == -1) {
					stk[top - 1].nval = stk[top].ack;
				}
				else {
					stk[top - 1].ack = stk[top].ack;
				}

				--top;
			}
		}

		return stk[0].ack;
	}
	```
