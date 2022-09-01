---
layout:         post
title:          "线索二叉树相关算法"
subtitle:   	"二叉树线索化及利用线索遍历二叉树"
post-date:      2022-08-28
update-date:    2022-09-01
author:         "Eicmlye"
header-img:     "img/em-post/20220828-BiTreeThread.jpg"
catalog:        true
tags: # for multiple tags, tabs should be replaced by spaces before '-';
    - 算法
    - 线索二叉树
---

#### 0. 数据结构

```cpp
typedef struct TreeNode {
	int data = 0;
	TreeNode* lchild = nullptr;
	TreeNode* rchild = nullptr;
} TreeNode, * BiTree;

typedef struct ThreadTNode {
	int data = 0;
	bool ltag = false; // true if child is a thread;
	bool rtag = false; // true if child is a thread;
	ThreadTNode* lchild = nullptr;
	ThreadTNode* rchild = nullptr;
} ThreadTNode, * ThreadBTree;
```

#### 1. 线索化

##### 1.1. 先序线索化

```cpp
ThreadBTree buildPreOrdThread(BiTree src)
{
	if (src == nullptr) {
		return nullptr;
	}

	ThreadBTree result = new ThreadTNode;

	typedef struct stackNode {
		TreeNode* tnode = nullptr;
		stackNode* next = nullptr;
	} stackNode, * stack;

	typedef struct ThreadStackNode {
		ThreadTNode* tnode = nullptr;
		ThreadStackNode* next = nullptr;
	} ThreadStackNode, * ThreadStack;

	#define STKPUSH(m_stk, m_nodeMode, m_topcache, m_tnode) do { m_topcache = new m_nodeMode; m_topcache->tnode = m_tnode; m_topcache->next = m_stk->next; m_stk->next = m_topcache; } while (0)
	#define SRCSTKPUSH(m_tnode) STKPUSH(stkSrc, stackNode, topSrc, m_tnode)
	#define TARSTKPUSH(m_tnode) STKPUSH(stkTar, ThreadStackNode, topTar, m_tnode)

	#define STKPOP(m_stk, m_topcache) do { m_stk->next = m_topcache->next; delete m_topcache; m_topcache = m_stk->next; } while (0)
	#define SRCSTKPOP STKPOP(stkSrc, topSrc)
	#define TARSTKPOP STKPOP(stkTar, topTar)

	stack stkSrc = new stackNode;
	stackNode* topSrc = nullptr;

	ThreadStack stkTar = new ThreadStackNode;
	ThreadStackNode* topTar = nullptr;

	TreeNode* movSrc = src;
	ThreadTNode* movTar = result;
	ThreadTNode* threadPred = nullptr;

	while (stkSrc->next != nullptr || movSrc != nullptr) {
		if (movSrc != nullptr) {
			SRCSTKPUSH(movSrc);

			/* visit node */
			movTar->data = movSrc->data;
			TARSTKPUSH(movTar);
			// init tags;
			movTar->ltag = (movSrc->lchild == nullptr);
			movTar->rtag = (movSrc->rchild == nullptr);
			// modify threads;
			if (movTar->ltag) {
				movTar->lchild = threadPred;
			}
			if (threadPred != nullptr && threadPred->rtag) {
				threadPred->rchild = movTar;
			}
			// update threadPred;
			threadPred = movTar;

			movSrc = movSrc->lchild;
			if (movSrc != nullptr) {
				movTar->lchild = new ThreadTNode;
				movTar = movTar->lchild;
			}
		}
		else {
			movSrc = topSrc->tnode->rchild;
			SRCSTKPOP;
			movTar = topTar->tnode;
			if (movSrc != nullptr) {
				movTar->rchild = new ThreadTNode;
				movTar = movTar->rchild;
			}
			TARSTKPOP;
		}
	}

	return result;
}
```

##### 1.2. 中序线索化

```cpp
ThreadBTree buildInOrdThread(BiTree src)
{
	if (src == nullptr) {
		return nullptr;
	}

	ThreadBTree result = new ThreadTNode;

	typedef struct stackNode {
		TreeNode* tnode = nullptr;
		stackNode* next = nullptr;
	} stackNode, * stack;

	typedef struct ThreadStackNode {
		ThreadTNode* tnode = nullptr;
		ThreadStackNode* next = nullptr;
	} ThreadStackNode, * ThreadStack;

	#define STKPUSH(m_stk, m_nodeMode, m_topcache, m_tnode) do { m_topcache = new m_nodeMode; m_topcache->tnode = m_tnode; m_topcache->next = m_stk->next; m_stk->next = m_topcache; } while (0)
	#define SRCSTKPUSH(m_tnode) STKPUSH(stkSrc, stackNode, topSrc, m_tnode)
	#define TARSTKPUSH(m_tnode) STKPUSH(stkTar, ThreadStackNode, topTar, m_tnode)

	#define STKPOP(m_stk, m_topcache) do { m_stk->next = m_topcache->next; delete m_topcache; m_topcache = m_stk->next; } while (0)
	#define SRCSTKPOP STKPOP(stkSrc, topSrc)
	#define TARSTKPOP STKPOP(stkTar, topTar)

	stack stkSrc = new stackNode;
	stackNode* topSrc = nullptr;

	ThreadStack stkTar = new ThreadStackNode;
	ThreadStackNode* topTar = nullptr;

	TreeNode* movSrc = src;
	ThreadTNode* movTar = result;
	ThreadTNode* threadPred = nullptr;

	while (stkSrc->next != nullptr || movSrc != nullptr) {
		if (movSrc != nullptr) {
			SRCSTKPUSH(movSrc);

			/* visit node */
			movTar->data = movSrc->data;
			TARSTKPUSH(movTar);
			// init tags;
			movTar->ltag = (movSrc->lchild == nullptr);
			movTar->rtag = (movSrc->rchild == nullptr);

			movSrc = movSrc->lchild;
			if (movSrc != nullptr) {
				movTar->lchild = new ThreadTNode;
				movTar = movTar->lchild;
			}
		}
		else {
			movSrc = topSrc->tnode;
			movTar = topTar->tnode;

			// modify threads;
			if (movTar->ltag) {
				movTar->lchild = threadPred;
			}
			if (threadPred != nullptr && threadPred->rtag) {
				threadPred->rchild = movTar;
			}
			// update threadPred;
			threadPred = movTar;

			movSrc = movSrc->rchild;
			SRCSTKPOP;
			if (movSrc != nullptr) {
				movTar->rchild = new ThreadTNode;
				movTar = movTar->rchild;
			}
			TARSTKPOP;
		}
	}

	return result;
}
```

#### 2. 利用线索二叉树遍历

##### 2.1. 前序遍历中序线索二叉树

```cpp
/* 利用左子树最右结点的右线索回到根节点 */
void preOrdTrav(ThreadBTree threadTree)
{
	ThreadTNode* mov = threadTree;
	bool prevTag = false;
	while (mov != nullptr) {
		if (!prevTag) {
			/* visit node */
			std::cout << mov->data << ' ';
		}

		if (!prevTag && !mov->ltag) {
			prevTag = mov->ltag;
			mov = mov->lchild;
		}
		else {
			prevTag = mov->rtag;
			mov = mov->rchild;
		}
	}

	return;
}
```

通用的思考方式是, 先找到所需遍历方式的第一个节点, 然后考察如何在给定线索二叉树中找到该遍历方式的后继. 循环即可得到结果.

```cpp
void preOrdTrav(ThreadBTree threadTree)
{
	if (threadTree == nullptr) {
		return;
	}

	ThreadTNode* mov = threadTree;
	bool prevTag = false;

	// get the first node in pre-order trav.
	// here threadTree is exactly the one we want;

	// loop: visit and get next;
	while (mov != nullptr) {
		/* visit node */
		std::cout << mov->data << ' ';

		/* get next */
		if (!mov->ltag) {
			mov = mov->lchild;
		}
		else if (!mov->rtag) {
			mov = mov->rchild;
		}
		else { // now mov->rtag == true;
			while (mov != nullptr && mov->rtag) {
				mov = mov->rchild; // go to root;
			}
			if (mov == nullptr) {
				return;
			}
			mov = mov->rchild; // left subtree done, go to right subtree;
		}
	}

	return;
}
```

##### 2.2. 中序遍历中序线索二叉树

```cpp
void inOrdTrav(ThreadBTree threadTree)
{
	ThreadTNode* mov = threadTree;
	bool prevTag = false;

	while (mov != nullptr) {
		if (!prevTag) { // the pred of mov is its actual parent rather than simply a thread predecessor;
			while (!mov->ltag) {
				mov = mov->lchild;
			}
		}

		/* visit node */
		std::cout << mov->data << ' ';

		prevTag = mov->rtag;
		mov = mov->rchild;
	}
	std::cout << std::endl;

	return;
}
```

通用的思考方式是, 先找到所需遍历方式的第一个节点, 然后考察如何在给定线索二叉树中找到该遍历方式的后继. 循环即可得到结果.

```cpp
void inOrdTrav(ThreadBTree threadTree)
{
	ThreadTNode* mov = threadTree;

	// get the first node in in-order trav.
	while (!mov->ltag) {
		mov = mov->lchild;
	}

	// loop: visit and get next;
	while (mov != nullptr) {
		/* visit node */
		std::cout << mov->data << ' ';

		if (!mov->rtag) {
			mov = mov->rchild;
			while (!mov->ltag) {
				mov = mov->lchild;
			}
		}
		else {
			mov = mov->rchild;
		}
	}

	return;
}
```

##### 2.3. 后序遍历中序线索二叉树

通用的思考方式是, 先找到所需遍历方式的第一个节点, 然后考察如何在给定线索二叉树中找到该遍历方式的后继. 循环即可得到结果.

```cpp
void postOrdTrav(ThreadBTree threadTree)
{
	ThreadTNode* mov = threadTree;
	ThreadTNode* ancestor = nullptr;

	// Get the first node in post-order trav.
	// find the highest node with lchild and go to the left-most node;
	// if node with lchild does not exist, find right-most leaf;
	while (mov->ltag && !mov->rtag) {
		mov = mov->rchild;
	}
	while (!mov->ltag) {
		mov = mov->lchild;
	}

	// loop: visit and get next;
	while (mov != nullptr) {
		/* visit node */
		std::cout << mov->data << ' ';

		/* find ancestor */
		ancestor = mov;
		while (!ancestor->ltag) {
			ancestor = ancestor->lchild;
		}
		ancestor = ancestor->lchild;

		if (ancestor == nullptr || ancestor->rchild != mov) {
			ancestor = mov;
			while (!ancestor->rtag) {
				ancestor = ancestor->rchild;
			}
			ancestor = ancestor->rchild;

			if (ancestor == nullptr) {
				return;
			}
			if (ancestor->rtag) {
				mov = ancestor;
			}
			else {
				mov = ancestor->rchild; // now mov is the root of the right subtree of ancestor;
				// find the highest node with lchild and go to the left-most node;
				// if node with lchild does not exist, find right-most leaf;
				while (mov->ltag && !mov->rtag) {
					mov = mov->rchild;
				}
				while (!mov->ltag) {
					mov = mov->lchild;
				}
			}
		}
		else {
			mov = ancestor;
		}
	}

	return;
}
```
