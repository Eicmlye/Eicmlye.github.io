---
layout:         post
title:          "二叉树链式存储初始化"
subtitle:   
post-date:      2022-08-12
update-date:    
author:         "Eicmlye"
header-img:     "img/em-post/20220812-BiTreeBuild.jpg"
catalog:        true
tags:
    - 算法
    - 二叉树
---

```cpp
BiTree buildBiTree(size_t level)
{
	TreeNode* node = new TreeNode;
	if (scanf("%d", &(node->data)) == 1) {
		for (size_t index = 0; index < level; ++index) {
			std::cout << "    ";
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
		std::cout << "\r\033[1A\033[K";
		return nullptr;
	}
}
```
