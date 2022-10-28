---
layout:         post
title:          "散列表基本操作"
subtitle:   	
post-date:      2022-10-12
update-date:    
author:         "Eicmlye"
header-img:     "img/em-post/20221012-hash.jpg"
catalog:        true
tags: # for multiple tags, tabs should be replaced by spaces before '-';
    - 算法
    - 散列表
    - 哈希表
---

### 0. 数据结构

```cpp
enum hashFlag: short
{
	EMPTY = 0,
	INUSE = 1,
	DELETED = 2
};

typedef struct hashNode {
	int data_ = 0;
	hashFlag flag_ = EMPTY;
} hashNode;

typedef struct ht {
	hashNode* head_ = nullptr;
	size_t size_ = 0;
	size_t capacity_ = 0;
} *hashTable;
```

### 1. 基本操作（以开放定址线性探测为例）

#### 1.1. 构造与析构

```cpp
hashTable initHash(size_t capacity = 10)
{
	hashTable HT = new ht;
	HT->capacity_ = capacity;
	HT->size_ = 0;
	HT->head_ = new hashNode[HT->capacity_];
	for (size_t index = 0; index < HT->capacity_; ++index) {
		HT->head_[index].data_ = 0;
		HT->head_[index].flag_ = EMPTY;
	}

	return HT;
}

void destroyHash(hashTable table)
{
	delete[] table->head_;
	delete table;

	return;
}
```

#### 1.2. 插入、查找和删除

```cpp
bool hashFind(hashTable table, int data)
{
	size_t inc = 0;
	do {
		if (table->head_[(data + inc) % table->capacity_].flag_ == INUSE) {
			if (table->head_[(data + inc) % table->capacity_].data_ == data) {
				return true;
			}
		}

		++inc;
	} while (table->head_[(data + inc) % table->capacity_].flag_ != EMPTY && inc != table->capacity_);

	return false;
}

bool hashInsert(hashTable table, int data)
{
	size_t inc = 0;
	do {
		if (table->head_[(data + inc) % table->capacity_].flag_ != INUSE) {
			table->head_[(data + inc) % table->capacity_].data_ = data;
			table->head_[(data + inc) % table->capacity_].flag_ = INUSE;

			return true;
		}

		++inc;
	} while (inc != table->capacity_);

	return false;
}

bool hashDelete(hashTable table, int data)
{
	size_t inc = 0;
	do {
		if (table->head_[(data + inc) % table->capacity_].flag_ == INUSE) {
			if (table->head_[(data + inc) % table->capacity_].data_ == data) {
				table->head_[(data + inc) % table->capacity_].flag_ = DELETED;

				return true;
			}
		}

		++inc;
	} while (table->head_[(data + inc) % table->capacity_].flag_ != EMPTY && inc != table->capacity_);

	return false;
}
```

### 附录

#### 重要内容更新日志
