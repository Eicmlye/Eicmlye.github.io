---
layout:         post
title:          "二叉树的遍历算法"
subtitle:   
post-date:      2022-08-08
update-date:    2022-08-19
author:         "Eicmlye"
header-img:     "img/em-post/20220808-BiTreeTrav.jpg"
catalog:        true
tags: # for multiple tags, tabs should be replaced by spaces before '-';
    - 算法
    - 二叉树
---

### 0. 数据结构

```cpp
template <typename DataType>
class BTNode
{
	DataType val_;
	BTNode<DataType>* lchild_;
	BTNode<DataType>* rchild_;
};

template <typename DataType>
using BTree = BTNode<DataType>*; // without header node;
```

### 1. 递归遍历

#### 1.1. 先序遍历

```cpp
template <typename DataType>
void preOrdTraversal(BTree<DataType> tree)
{
	if (tree != nullptr) {
		/* visit node */


		preOrdTraversal(tree->lchild_);
		preOrdTraversal(tree->rchild_);
	}
	return;
}
```

#### 1.2. 中序遍历

```cpp
template <typename DataType>
void inOrdTraversal(BTree<DataType> tree)
{
	if (tree != nullptr) {
		inOrdTraversal(tree->lchild_);
		/* visit node */


		inOrdTraversal(tree->rchild_);
	}
	return;
}
```

#### 1.3. 后序遍历

```cpp
template <typename DataType>
void postOrdTraversal(BTree<DataType> tree)
{
	if (tree != nullptr) {
		postOrdTraversal(tree->lchild_);
		postOrdTraversal(tree->rchild_);
		/* visit node */


	}
	return;
}
```

### 2. 非递归遍历

#### 2.1. 先序遍历

##### 2.1.1. 栈法

```cpp
template <typename DataType>
void preOrdTraversal(BTree<DataType> tree)
{
	if (tree == nullptr) { // empty tree;
		return;
	}

	stack<BTNode<DataType>*> stk = {};
	BTNode<DataType>* mov = tree;

	while (!stk.empty() || mov != nullptr) {
		if (mov != nullptr) {
			stk.push(mov);

			/* visit node */

			mov = mov->lchild_;
		}
		else {	// the current nullptr node is not in the stack yet;
			mov = stk.top();
			mov = mov->rchild_;
			stk.pop();
		}
	}

	return;
}
```

##### 2.1.2. 孩子双亲表示法

该方法需要给每个结点增加双亲指针 `TreeNode* parent` , 初值为 `nullptr` .

```cpp
void preOrdTrav(BiTree tree)
{
	if (tree == nullptr) {
		return;
	}

	TreeNode* mov = tree;
	TreeNode* pred = new TreeNode; // header indicating empty stack;
	while (mov != tree->parent) {
		while (mov != nullptr) {
			/* visit node */
			std::cout << mov->data << ' ';

			mov->parent = pred;
			pred = mov;
			mov = mov->lchild;
		}

		// now mov == nullptr;
		mov = pred;
		pred = pred->parent;
		while (mov != tree->parent && (mov->rchild == nullptr || mov->rchild->parent != nullptr)) { // non-null parent indicates that the node has been visited;
			mov = mov->parent;
			pred = pred->parent;
		}
		if (mov == tree->parent) {
			break;
		}
		pred = mov;
		mov = mov->rchild;
	}

	delete tree->parent;
	tree->parent = nullptr;
	std::cout << std::endl;

	return;
}
```

#### 2.2. 中序遍历

```cpp
template <typename DataType>
void inOrdTraversal(BTree<DataType> tree)
{
	if (tree == nullptr) { // empty tree;
		return;
	}

	stack<BTNode<DataType>*> stk = {};
	BTNode<DataType>* mov = tree;

	while (!stk.empty() || mov != nullptr) {
		if (mov != nullptr) {
			stk.push(mov);
			mov = mov->lchild_;
		}
		else {	// the current nullptr node is not in the stack yet;
			mov = stk.top();

			/* visit node */

			mov = mov->rchild_;
			stk.pop();
		}
	}

	return;
}
```

#### 2.3. 后序遍历

```cpp
template <typename DataType>
void postOrdTraversal(BTree<DataType> tree)
{
	if (tree == nullptr) { // empty tree;
		return;
	}

	stack<BTNode<DataType>*> stk = {};
	BTNode<DataType>* mov = tree;
	// set probe for most recently visited node;
	BTNode<DataType>* rtag = nullptr;

	while (!stk.empty() || mov != nullptr) {
		if (mov != nullptr) {
			stk.push(mov);
			mov = mov->lchild_;
		}
		else { // the current nullptr node is not in the stack yet;
			mov = stk.top();
			if (mov->rchild_ != nullptr && mov->rchild_ != rtag) {
				mov = mov->rchild_;
			}
			else {
				/* visit node */

				stk.pop();
				rtag = mov; // update rtag;
				mov = nullptr;
			}
		}
	}

	return;
}
```

#### 2.4. 层序遍历

```cpp
template <typename DataType>
void levOrdTraversal(BTree<DataType> tree)
{
	if (tree == nullptr) { // empty tree;
		return;
	}

	queue<BTNode<DataType>*> que = {};
	que.push(tree);
	BTNode<DataType>* mov = nullptr;
	/* visit root */

	while (!que.empty()) {
		size_t len = que.size(); // freeze size;

		for (size_t index = 0; index < len; ++index) {
			mov = que.front();

			if (mov->lchild_ != nullptr) {
				/* visit lchild node */

				que.push(mov->lchild_);
			}
			if (mov->rchild_ != nullptr) {
				/* visit rchild node */

				que.push(mov->rchild_);
			}

			que.pop();
		}
	}

	return;
}
```
