---
layout:         post
title:          "二叉树链式存储和顺序存储"
subtitle:   
post-date:      2022-08-12
update-date:    2022-08-14
author:         "Eicmlye"
header-img:     "img/em-post/20220812-BiTreeBuild.jpg"
catalog:        true
tags:
    - 算法
    - 二叉树
---

#### 0. 数据结构

```cpp
typedef struct TreeNode {
	int data = 0;
	TreeNode* left = nullptr;
	TreeNode* right = nullptr;
} TreeNode, * BiTree;
```

#### 1. 链式存储初始化

相当于读取字符串, 转存为整型数据.

```cpp
#define _CRT_SECURE_NO_WARNINGS

BiTree buildBiTree(size_t level = 0)
{
	TreeNode* node = new TreeNode;
	if (scanf("%d", &(node->data)) == 1) {
		for (size_t index = 0; index < level; ++index) {
			std::cout << "    "; // 4 spaces;
		}
		std::cout << "Lchild of " << node->data << ": ";
		node->left = buildBiTree(level + 1);
		for (size_t index = 0; index < level; ++index) {
			std::cout << "    ";
		}
		std::cout << "Rchild of " << node->data << ": ";
		node->right = buildBiTree(level + 1);
		return node;
	}
	else {
		while (getchar() != '\n') {
			continue;
		}
		std::cout << "\r\033[1A\033[K"; // 回车 (不换行), 光标上移一行 (因为输入会带一个回车), 清除当前行控制台输出;
		return nullptr;
	}
}
```

#### 2. 链式存储转为顺序存储

顺序存储下虚结点设为空指针, 整型数据转存为字符串.

```cpp
#include <cmath>
#define _CRT_SECURE_NO_WARNINGS

char** btree2Arr(BiTree tree, size_t& h)
{
	// queue push;
	#define QUEPUSH(elem) do { mov = new queNode; mov->tnode = elem; rear->next = mov; rear = rear->next; } while (0)

	// queue data structure;
	typedef struct queNode {
		TreeNode* tnode = nullptr;
		queNode* next = nullptr;
	} queNode, *queue;

	// init que;
	queue que = new queNode; // header node of queue;
	queNode* rear = que; // rear of queue;
	queNode* front = que; // front of queue;
	size_t queSize = 0;

	// set que;
	queNode* mov = new queNode;
	mov->tnode = tree;
	rear->next = mov;
	rear = rear->next;

	// init level traversal;
	size_t cacheSize = 0;
	front = que->next;
	h = 0; // count level of tree;
	++queSize;
	bool allNull = false; // traversal ending tag;

	// start level traversal;
	while (!allNull) {
		++h;
		cacheSize = queSize;
		queSize = 0;
		allNull = true;

		for (size_t index = 0; index < cacheSize; ++index) {
			if (front->tnode != nullptr) {
				if (front->tnode->left != nullptr) {
					QUEPUSH(front->tnode->left);
					allNull = false;
				}
				else {
					QUEPUSH(nullptr);
				}
				++queSize;

				if (front->tnode->right != nullptr) {
					QUEPUSH(front->tnode->right);
					allNull = false;
				}
				else {
					QUEPUSH(nullptr);
				}
				++queSize;
			}
			else {
				QUEPUSH(nullptr);
				++queSize;
				QUEPUSH(nullptr);
				++queSize;
			}

			front = front->next;
		}
	}

	size_t arrSize = (size_t)pow(2, h) - 1;
	char** result = new char*[arrSize];
	size_t dataSize = (size_t)log((size_t)pow(2, sizeof(int) * 8)) + 1;

	for (size_t index = 0; index < arrSize; ++index) {
		if (que->next->tnode != nullptr) {
			result[index] = new char[dataSize];
			for (size_t jndex = 0; jndex < dataSize; ++jndex) { // init; 
				result[index][jndex] = '\0';
			}

			// int to char*; one may print data to file as well;
			sprintf(result[index], "%d", que->next->tnode->data);
		}
		else { // empty node will be interpreted as nullptr;
			result[index] = nullptr;
		}

		// pop que; use rear as cache;
		rear = que->next;
		que->next = rear->next;
		delete rear;
	}

	return result;
}
```

#### 3. 顺序存储转为链式存储

```cpp
BiTree levBuildTree(char** arr, size_t h)
{
	if (h == 0) {
		return nullptr;
	}

	// now non-empty root;
	// push the first level, i.e. root;
	BiTree result = new TreeNode;
	result->data = atoi(arr[0]);
	size_t curInd = 1;
	TreeNode* newTreeNode = nullptr;

	// que push;
	#define QUEPUSH(elem) do { mov = new queNode; mov->tnode = elem; rear->next = mov; rear = rear->next; } while (0)

	typedef struct queNode {
		TreeNode* tnode;
		queNode* next;
	} queNode, *queue;

	queue que = new queNode; // header of queue;  
	queNode* rear = que;
	size_t queSize = 0;

	queNode* mov = new queNode;
	mov->tnode = result;
	rear->next = mov;
	rear = rear->next;
	++queSize;

	size_t cacheSize = 0;

	for (size_t curH = 1; curH < h; ++curH) {
		cacheSize = queSize;
		queSize = 0;
		for (size_t index = 0; index < cacheSize; ++index) {
			if (que->next->tnode != nullptr) {
				if (arr[curInd] != nullptr) {
					newTreeNode = new TreeNode;
					newTreeNode->data = atoi(arr[curInd]);
					que->next->tnode->left = newTreeNode;
					++curInd;
					QUEPUSH(newTreeNode);
				}
				else {
					++curInd;
					QUEPUSH(nullptr);
				}

				if (arr[curInd] != nullptr) {
					newTreeNode = new TreeNode;
					newTreeNode->data = atoi(arr[curInd]);
					que->next->tnode->right = newTreeNode;
					++curInd;
					QUEPUSH(newTreeNode);
				}
				else {
					++curInd;
					QUEPUSH(nullptr);
				}
			}
			else {
				++curInd;
				QUEPUSH(nullptr);
				++curInd;
				QUEPUSH(nullptr);
			}

			queSize += 2;

			// pop queue;
			queNode* cache = que->next;
			que->next = cache->next;
			delete cache;
		}
	}

	return result;
}
```
