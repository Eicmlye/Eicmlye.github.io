---
layout:         post
title:          "二叉排序树相关算法"
subtitle:   	
post-date:      2022-09-01
update-date:    2022-10-09
author:         "Eicmlye"
header-img:     "img/em-post/20220901-BST.jpg"
catalog:        true
tags: # for multiple tags, tabs should be replaced by spaces before '-';
    - 算法
    - 二叉排序树 (BST)
---

#### 0. 数据结构

```cpp
typedef struct TreeNode {
	int data = 0;
	TreeNode* lchild = nullptr;
	TreeNode* rchild = nullptr;
} TreeNode, * BiTree;
```

#### 1. 构造函数

```cpp
BiTree buildBST(void)
{
	std::cout << "A binary search tree (BST) is being built. " << std::endl;
	std::cout << "Enter data in the node after prompting, " << std::endl;
	std::cout << "or enter any non-digit character except whitespaces to exit. " << std::endl << std::endl;

	BiTree result = nullptr;
	TreeNode* mov = nullptr;
	TreeNode* newNode = nullptr;
	TreeNode* tempLink = nullptr;
	int cache = 0;

	// fill in root;
	if (scanf("%d", &cache) == 1) {
		result = new TreeNode;
		result->data = cache;
	}
	else {
		return nullptr;
	}

	while (scanf("%d", &cache) == 1) {
		newNode = new TreeNode;
		newNode->data = cache;

		mov = result;
		while (mov->data != cache) {
			if (cache < mov->data) {
				if (mov->lchild == nullptr) {
					mov->lchild = newNode;
				}
				mov = mov->lchild;
			}
			else if (cache > mov->data) {
				if (mov->rchild == nullptr) {
					mov->rchild = newNode;
				}
				mov = mov->rchild;
			}
		}

		// Notice that mov->data == cache is not an appropriate condition here, 
		// because this will be satified in the end no matter cache is a new value or not; 
		// Better solution is to compare the address of leaf and newNode; 
		if (mov != newNode) {
			delete newNode; // omit values that already exist in the BST; 
		}
	}

	return result;
}
```

#### 2. 检验二叉树是否为 BST

##### 2.1. 按定义检验

```cpp
static bool isBST_buildin(BiTree tree, int& maxElem)
{
	// For empty tree, return true; 
	if (tree == nullptr) {
		maxElem = INT_MIN; 
		return true; 
	}

	// Now tree != nullptr; 
	bool result = true;
	// If lchild tree exists; 
	if (tree->lchild != nullptr) {
		// If lchild tree is not BST, return false; 
		if (!(result = isBST_buildin(tree->lchild, maxElem))) {
			return result;
		}
		// Now lchild tree is BST; 
		// Now maxElem is the max element of lchild tree; 
		// If cur node data is not greater than max data of lchild tree, return false; 
		if (!(result = tree->data > maxElem)) {
			return result;
		}
	}
	else {
		maxElem = tree->data; 
	}
	// If rchild tree exists; 
	if (tree->rchild != nullptr) {
		// If rchild tree is not BST, return false; 
		if (!(result = isBST_buildin(tree->rchild, maxElem))) {
			return result;
		}
		// Now rchild tree is BST; 
		// Now maxElem is the max element of rchild tree, 
		// which is also the max element of cur node tree; 
		// If cur node data is not less than max data of rchild tree, return false; 
		if (!(result = tree->data < maxElem)) {
			return result;
		}
	}
	else {
		maxElem = tree->data;
	}

	// Now pass the examination, return true; 
	// Now maxElem is the max element of cur node tree; 
	return result; 
}

bool isBST(BiTree tree)
{
	int maxElem = INT_MIN; 

	return isBST_buildin(tree, maxElem); 
}
```

##### 2.2. 按中序遍历序列检验

一棵二叉树为 BST 当且仅当其中序序列为严格递增序列. 

```cpp
#include <stack>

using std::stack; 

bool isBST(BiTree tree)
{
	if (tree == nullptr) { // empty tree;
		return true;
	}

	stack<TreeNode*> stk = {};
	TreeNode* mov = tree;
	TreeNode* pred = nullptr; 

	while (!stk.empty() || mov != nullptr) {
		if (mov != nullptr) {
			stk.push(mov);
			mov = mov->lchild;
		}
		else { // the current nullptr node is not in the stack yet;
			mov = stk.top();

			/* visit node */
			if (pred != nullptr) {
				if (pred->data >= mov->data) {
					return false; 
				}
			}
			pred = mov; 

			mov = mov->rchild;
			stk.pop();
		}
	}

	return true;
}
```

#### 3. 删除 BST 节点

```cpp
bool delBSTNode(BiTree tree, int value)
{
	if (tree == nullptr) {
		return false;
	}
	if (tree->data == value) {
		printf("This function cannot deal with root deleting. \n");
		return false;
	}

	// Now tree != nullptr;
	TreeNode* mov = tree;
	TreeNode* cache = tree; // temporary cache for parent; 
	// Find node with data value;
	while (mov != nullptr && mov->data != value) {
		cache = mov;
		mov = (mov->data > value ? mov->lchild : mov->rchild);
	}
	if (mov == nullptr) { // Target node not found;
		return false;
	}

	// Now mov->data == value;
	// Check children of mov and operate accordingly;
	if (mov->lchild == nullptr && mov->rchild == nullptr) {// leaf;
		// delete directly and done;
		if (cache->lchild == mov) {
			cache->lchild = nullptr;
		}
		else {
			cache->rchild = nullptr; 
		}

		delete mov;
	}
	// Now not both children are nullptr;
	else if (mov->lchild == nullptr) {// i.e. rchild != nullptr;
		if (cache->lchild == mov) {
			cache->lchild = mov->rchild;
		}
		else {
			cache->rchild = mov->rchild;
		}

		delete mov;
	}
	else if (mov->rchild == nullptr) {// i.e. lchild != nullptr;
		if (cache->lchild == mov) {
			cache->lchild = mov->lchild;
		}
		else {
			cache->rchild = mov->lchild;
		}

		delete mov;
	}
	// Now neither of its children is nullptr;
	// Copy the value of inorder-successor of mov to mov's place;
	// Then delete this inorder-successor; 
	else {
		// Find inorder-successor;
		cache = mov->rchild; 
		while (cache->lchild != nullptr) {
			cache = cache->lchild;
		}
		// Do not update mov->data here,
		// since delBSTNode(mov, cache->data) have to find the successor by value;
		int temp = cache->data;
		delBSTNode(mov, cache->data);

		// Now update mov->data;
		mov->data = temp; 
	}

	return true; 
}
```

#### 附录

##### 重要内容更新日志

2022-10-03 添加了两种 [BST 检验函数](#2-检验二叉树是否为-bst); 

2022-10-05 修复了 [BST 构造函数](#1-构造函数)中, 输入值与已有节点值相同时内存泄漏的问题; 添加了 [删除 BST 节点](#3-删除-bst-节点)的方法. 