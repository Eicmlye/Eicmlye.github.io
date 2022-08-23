---
layout:         post
title:          "二叉树存储方式转换"
subtitle:   	"存储方式转换及循环输入 API"
post-date:      2022-08-12
update-date:    2022-08-23
author:         "Eicmlye"
header-img:     "img/em-post/20220812-BiTreeBuild.jpg"
catalog:        true
tags: # for multiple tags, tabs should be replaced by spaces before '-';
    - 算法
    - 二叉树
---

#### 0. 数据结构

```cpp
typedef struct TreeNode {
	int data = 0;
	TreeNode* lchild = nullptr;
	TreeNode* rchild = nullptr;
} TreeNode, * BiTree;
```

#### 1. 链式存储初始化

##### 1.1. 一般二叉树 (控制台输入)

相当于读取字符串, 转存为整型数据.

```cpp
#define _CRT_SECURE_NO_WARNINGS

// The variable level is used for building levels
// and does not require user input.
// So using buildBiTree() is perfectly fine;
BiTree buildBiTree(size_t level = 1)
{
	#define CLRSTDIN \
		do {\
			while (getchar() != '\n') {\
				continue; \
			}\
		} while (0)
	#define INDENT(m_indsize) \
		do {\
			for (size_t index = 0; index < m_indsize; ++index) {\
				std::cout << "    "; \
			}\
		} while (0)

	// Welcome guide;
	if (level == 1) {
		std::cout << "A binary tree is being built. " << std::endl;
		std::cout << "Enter data in the node after prompting, " << std::endl;
		std::cout << "or enter any non-digit character except \'\\n\' for empty node. " << std::endl << std::endl;
		std::cout << "Root: ";
	}

	// test empty input and rewinding;
	char temp = '\0';
	while ((temp = getchar()) == '\n') {
		// move back to the end of the line;
		std::cout << "\033[1A";
		for (size_t index = 0; index < (level - 1) * 4 + 8; ++index) {
			std::cout << "\033[1C";
		}
		if (level == 1) {
			std::cout << "\033[2D";
		}
	}
	ungetc(temp, stdin);

	// load in data;
	TreeNode* node = new TreeNode;
	if (scanf("%d", &(node->data)) == 1) {
		// clear stdin for the next empty-input test;
		CLRSTDIN;

		INDENT(level);
		std::cout << "Lchild: ";
		node->lchild = buildBiTree(level + 1);
		if (node->lchild == nullptr) {
			std::cout << "\r\033[1A\033[K";
			INDENT(level);
			std::cout << "Lchild: NULL" << std::endl;
		}

		INDENT(level);
		std::cout << "Rchild: ";
		node->rchild = buildBiTree(level + 1);
		if (node->rchild == nullptr) {
			std::cout << "\r\033[1A\033[K";
			INDENT(level);
			std::cout << "Rchild: NULL" << std::endl;
		}

		if (node->lchild == nullptr && node->rchild == nullptr) {
			std::cout << "\r\033[1A\033[K";
			std::cout << "\r\033[1A\033[K";
		}

		return node;
	}
	else { // empty child;
		// clear stdin for the next empty-input test;
		CLRSTDIN;

		// std::cout << "\r\033[1A\033[K";
		// INDENT(level);
		// std::cout << "NULL" << std::endl;

		return nullptr;
	}
}
```

##### 1.2. 一般二叉树 (由遍历序列生成)

前序序列和中序序列可以确定一棵二叉树.

###### 1.2.1. 递归

```cpp
BiTree buildPreInBiTree(int* preList, int* inList, size_t preHead, size_t preTail, size_t inHead, size_t inTail)
{
	TreeNode* result = new TreeNode;
	result->data = preList[preHead];

	if (preHead != preTail) {
		// find preList[preHead] in inList;  
		size_t indRoot = 0;
		for (indRoot = inHead; indRoot < inTail; ++indRoot) {
			if (preList[preHead] == inList[indRoot]) {
				break;
			}
		}

		result->lchild = (indRoot != inHead) ? buildPreInBiTree(preList, inList, preHead + 1, preHead + (indRoot - inHead), inHead, indRoot - 1) : nullptr;
		result->rchild = (indRoot != inTail) ? buildPreInBiTree(preList, inList, preHead + (indRoot - inHead) + 1, preTail, indRoot + 1, inTail) : nullptr;
	}

	return result;
}

// API;
BiTree buildBiTree(int* preList, int* inList, size_t size)
{
	return buildPreInBiTree(preList, inList, 0, size - 1, 0, size - 1);
}
```

###### 1.2.2. 非递归

```cpp
BiTree buildPreInBiTree(int* preList, int* inList, size_t n)
{
	if (n == 0) {
		return nullptr;
	}

	size_t preInd = 0;
	size_t inInd = 0;
	BiTree result = new TreeNode; // header of tree; in case that the root has no lchild;

	typedef struct stackNode {
		TreeNode* tnode = nullptr;
		stackNode* next = nullptr;
	} stackNode, * stack;

	#define STKPUSH(m_elem) do { mov = new stackNode; mov->tnode = m_elem; mov->next = stk->next; stk->next = mov; ++preInd; } while (0)

	#define STKPOP do { stk->next = mov->next; delete mov; mov = stk->next; ++inInd; } while (0)

	#define LBUILDPUSH(m_elem) do { m_elem = new TreeNode; m_elem->data = preList[preInd]; stk->next->tnode->lchild = m_elem; STKPUSH(m_elem); } while (0)

	#define RBUILDPUSH(m_elem) do { m_elem = new TreeNode; m_elem->data = preList[preInd]; stk->next->tnode->rchild = m_elem; STKPOP; STKPUSH(m_elem); } while (0)

	stack stk = new stackNode; // header of stk;
	stackNode* mov = nullptr;
	STKPUSH(result);
	--preInd;

	// build root;
	TreeNode* cache = nullptr;
	LBUILDPUSH(cache); // now preInd is the index of the preListNext of the node just having been built; we will keep this definition for preInd during the process;

	// preInd will always reach n before inInd does;
	while (preInd < n) {
		while (preInd < n && preList[preInd] != inList[inInd]) {
			LBUILDPUSH(cache);
		}
		// build leaf;
		LBUILDPUSH(cache);
		if (preInd == n) {
			break;
		}

		// trace back to a node with rchild;
		while (mov->next->tnode->data == inList[inInd + 1]) { // rchild does not exist;
			STKPOP;
		}

		// build rchild;
		RBUILDPUSH(cache);
		if (preInd == n) {
			break;
		}

		// check if lchild exists;
		if (mov->next->tnode->data == inList[inInd + 1]) { // in inList mov->tnode is followed by an ancester root;
			STKPOP;
			// build rchild;
			RBUILDPUSH(cache);
		}
		if (preInd == n) {
			break;
		}
	}

	return result->lchild;
}
```

##### 1.3. 正则二叉树 (由遍历序列生成)

正则二叉树没有度为 1 的结点, 可以利用前序序列和后序序列生成.

```cpp
BiTree buildNormalBiTree(int* preList, int* postList, size_t preHead, size_t preTail, size_t postHead, size_t postTail)
{
	BiTree result = new TreeNode;
	result->data = preList[preHead];

	if (preHead != preTail) {
		// find preList[preHead + 1] in postList;
		size_t indL = 0;
		for (indL = postHead; indL <= postTail; ++indL) {
			if (postList[indL] == preList[preHead + 1]) {
				break;
			}
		}
		// find postList[postHead - 1] in preList;
		size_t indR = 0;
		for (indR = preHead; indR <= preTail; ++indR) {
			if (preList[indR] == postList[postTail - 1]) {
				break;
			}
		}

		result->left = buildNormalBiTree(preList, postList, preHead + 1, indR - 1, postHead, indL);
		result->right = buildNormalBiTree(preList, postList, indR, preTail, indL + 1, postTail - 1);
	}
	else {
		result->left = nullptr;
		result->right = nullptr;
	}

	return result;
}
```

#### 2. 链式存储转为顺序存储

顺序存储下虚结点设为空指针, 整型数据转存为字符串.

```cpp
#include <cmath>

char** btree2Arr(BiTree tree, size_t& h)
{
	// queue push;
	#define QUEPUSH(elem) do { mov = new queNode; mov->tnode = elem; rear->next = mov; rear = rear->next; ++queSize; } while (0)

	// queue data structure;
	typedef struct queNode {
		TreeNode* tnode = nullptr;
		queNode* next = nullptr;
	} queNode, *queue;

	// init que;
	queue que = new queNode; // header node of queue;
	queNode* front = que; // front of queue;
	queNode* rear = que; // rear of queue;
	size_t queSize = 0;

	// set que;
	queNode* mov = new queNode;
	mov->tnode = tree;
	rear->next = mov;
	rear = rear->next;
	++queSize;

	// init level traversal;
	size_t cacheSize = 0;
	front = que->next;
	h = 0; // count level of tree;
	bool allNull = false; // traversal ending tag;

	// start level traversal;
	while (!allNull) {
		++h;
		cacheSize = queSize; // freeze queSize;
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

				if (front->tnode->right != nullptr) {
					QUEPUSH(front->tnode->right);
					allNull = false;
				}
				else {
					QUEPUSH(nullptr);
				}
			}
			else {
				QUEPUSH(nullptr);
				QUEPUSH(nullptr);
			}

			front = front->next;
		}
	}

	// init array;
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

	// que push;
	#define QUEPUSH(elem) do { mov = new queNode; mov->tnode = elem; rear->next = mov; rear = rear->next; ++queSize; } while (0)

	typedef struct queNode {
		TreeNode* tnode;
		queNode* next;
	} queNode, *queue;

	// init queue;
	queue que = new queNode; // header of queue;  
	queNode* rear = que;
	size_t queSize = 0;

	// set queue;
	queNode* mov = new queNode;
	mov->tnode = result;
	rear->next = mov;
	rear = rear->next;
	++queSize;

	// init level traversal;
	size_t cacheSize = 0;
	TreeNode* newTreeNode = nullptr;

	for (size_t curH = 1; curH < h; ++curH) {
		cacheSize = queSize; // freeze queSize;
		queSize = 0;
		for (size_t index = 0; index < cacheSize; ++index) {
			if (que->next->tnode != nullptr) {
				if (arr[curInd] != nullptr) {
					newTreeNode = new TreeNode;
					newTreeNode->data = atoi(arr[curInd]);
					que->next->tnode->left = newTreeNode;
					QUEPUSH(newTreeNode);
				}
				else {
					QUEPUSH(nullptr);
				}
				++curInd;

				if (arr[curInd] != nullptr) {
					newTreeNode = new TreeNode;
					newTreeNode->data = atoi(arr[curInd]);
					que->next->tnode->right = newTreeNode;
					QUEPUSH(newTreeNode);
				}
				else {
					QUEPUSH(nullptr);
				}
				++curInd;
			}
			else {
				QUEPUSH(nullptr);
				++curInd;
				QUEPUSH(nullptr);
				++curInd;
			}

			// pop queue;
			queNode* cache = que->next;
			que->next = cache->next;
			delete cache;
		}
	}

	return result;
}
```

#### 4. 循环输入构建二叉树 API

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <iostream>

int main(void) {
	do {
		BiTree tree = buildBiTree();
		/* programs */

		cout << "Hit ENTER to exit. Enter any non-\'\\n\' character to build a new tree. " << endl << endl;
	} while (getchar() != '\n');
}
```
