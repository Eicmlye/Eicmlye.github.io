---
layout:         post
title:          "稀疏矩阵相关算法"
subtitle:   	"快速转置"
post-date:      2022-08-07
update-date:    
author:         "Eicmlye"
header-img:     "img/em-post/20220807-SparseMat.jpg"
catalog:        true
tags: # for multiple tags, tabs should be replaced by spaces before '-';
    - 算法
    - 稀疏矩阵
---

##### 1. 三元顺序表稀疏矩阵快速转置 (TC: O(n + t))

```cpp
struct SparMatNode {
	size_t row_ = 0; // from 1 to m;
	size_t col_ = 0; // from 1 to n;
	int data_ = 0;
};

using SparMat = SparMatNode*; // no header node;

// mat is a m * n sparse matrix;
// The number of non-zero elements in mat is t;
SparMat fastTranspose(SparMat mat, size_t n, size_t t)
{
	if (t == 0) { // zero matrix;
		return mat;
	}

	SparMat result = new SparMatNode[t];
	size_t* numCol = new size_t[n];
	size_t index = 0;
	for (index = 0; index < n; ++index) {
		numCol[index] = 0;
	}

	for (index = 0; index < t; ++index) {
		// count elements in each column;
		if (mat[index].col_ != n) {
			++numCol[mat[index].col_ - 1];
		}
	}
	// find the index of the first element of each column in result;
	for (index = 1; index < n - 1; ++index) {
		numCol[index] += numCol[index - 1];
	}
	for (index = 0; index < n - 1; ++index) {
		numCol[n - index - 1] = numCol[n - index - 2];
	}
	numCol[0] = 0;

	size_t curCol = 0;
	for (index = 0; index < t; ++index) {
		curCol = numCol[mat[index].col_ - 1];
		result[curCol].row_ = mat[index].col_;
		result[curCol].col_ = mat[index].row_;
		result[curCol].data_ = mat[index].data_;
		++numCol[mat[index].col_ - 1];
	}

	return result;
}
```
