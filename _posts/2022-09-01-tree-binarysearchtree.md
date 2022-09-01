---
layout:         post
title:          "二叉排序树相关算法"
subtitle:   	
post-date:      2022-09-01
update-date:    
author:         "Eicmlye"
header-img:     "img/em-post/20220901-BST.jpg"
catalog:        true
tags: # for multiple tags, tabs should be replaced by spaces before '-';
    - 算法
    - 二叉排序树
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
	std::cout << "or enter any non-digit character except \'\\n\' to exit. " << std::endl << std::endl;

	BiTree result = new TreeNode;
	TreeNode* mov = nullptr;
	TreeNode* newNode = nullptr;
	TreeNode* tempLink = nullptr;
	int cache = 0;

	// fill in root;
	if (scanf("%d", &cache) == 1) {
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
	}

	return result;
}
```
