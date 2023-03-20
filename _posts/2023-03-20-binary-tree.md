---
layout:     post
title:      "Binary Tree"
date:       2023-03-20 13:48 +0900
summary:    Notes about Implementing Binary Tree
categories: study
thumbnail: 
tags:
 - c
 - cpp
 - data-structure
 - binary-tree
---


Binary Tree!

Let's start with preparation.

```c
#define SURE_MALLOC(type, pointer) do (pointer) = (type ## *)malloc(sizeof(type)); while ((pointer) == NULL);
#define SAFE_FREE(pointer) if (pointer) { free(pointer); (pointer) = NULL; }

typedef struct _SNode {
	int data;
	struct _SNode* pLeft;
	struct _SNode* pRight;
} SNode;
```

# Create Node

Creating node itself is the same as before.

```c
SNode* CreateNode(int const _data) {
	SNode* node;
	SURE_MALLOC(SNode, node);
	node->data = _data;
	node->pLeft = NULL;
	node->pRight = NULL;
	return node;
}
```

However, now I want to append this node to its head.

```
SNode* AppendData(SNode* const _head, int const _data) {
	SNode* node = CreateNode(_data);
	AppendNode(_head, node);
	return node;
}
```

First, AppendData which accepts its `_head` and `_data`,
which creates node, appends it under `_head` and returns that node.

I have separated `AppendNode` function for later.

```c
void AppendNode(SNode* const _head, SNode* _node) {
	if (_head->data < _node->data) {
		if (_head->pRight == NULL) _head->pRight = _node;
		else AppendNode(_head->pRight, _node);
	}
	else {
		if (_head->pLeft == NULL) _head->pLeft = _node;
		else AppendNode(_head->pLeft, _node);
	}
}
```

If I were to only use this for building tree, I would not need
that NULL checkers and this didn't have to be recursive.

But this setup will come handy later.


# Building Tree

`BuildTree` function I made creates binary tree from
a *sorted* array.

```c
void BuildSubTree(SNode* const _head, int* const _pArr, int const _leftIdx, int const _rightIdx) {
	if (_head == NULL) return;
	if (_leftIdx > _rightIdx) return;
	int centerIdx = (_leftIdx + _rightIdx) >> 1;
	SNode* node = AppendNode(_head, _pArr[centerIdx]);
	if (_leftIdx == _rightIdx) return;
	BuildSubTree(node, _pArr, _leftIdx, centerIdx - 1);
	BuildSubTree(node, _pArr, centerIdx + 1, _rightIdx);
}
```

Sub-function.

5th line is not essential, but it does reduce some redundant calls.

The implementation reminds me of Binary Search, which makes sense.


```
void BuildTree(SNode** const _pHead, int* const _pArr, int const _len) {
	if (*_pHead != NULL) DestroyAll(_pHead);
	int centerIdx = _len >> 1;
	*_pHead = CreateNode(_pArr[centerIdx]);
	BuildSubTree(*_pHead, _pArr, 0, centerIdx - 1);
	BuildSubTree(*_pHead, _pArr, centerIdx + 1, _len - 1);
}
```

Main function is basically the same as the sub function but
with some handlers for the head.


# Print: Inorder Traversal

```
void PrintAll(SNode* const _head) {
	if (_head == NULL) { printf("(0)\n"); return; }
	printf("(%d)\n", PrintSub(_head));
}
```

Here's the function to call.

`PrintSub` sub-function also returns total count.

```c
int PrintSub(SNode* const _node) {
	if (_node == NULL) return 0;
	int count = 1;
	// Inorder Traversal
	count += PrintSub(_node->pLeft);
	printf("%d - ", _node->data);
	count += PrintSub(_node->pRight);
	return count;
}
```

This sub-function checks
left sub-tree -> root -> right sub-tree order.

This order is called **Inorder Traversal**.

Let's say the data we use for creating tree is this:

```c
int arr[] = { 1,2,3,4,5,6,7,8 };
```

And the result for Preorder, Inorder, and Postorder Traversal.
```
5 - 2 - 1 - 3 - 4 - 7 - 6 - 8 - (8) // Preorder
1 - 2 - 3 - 4 - 5 - 6 - 7 - 8 - (8) // Inorder
1 - 4 - 3 - 2 - 6 - 8 - 7 - 5 - (8) // Postorder
```

There's also Level Order Traverse, which is traversing
by node order, but this one requires using Queue.


### PrintWithLevel

```c
int PrintWithLevelSub(SNode* const _node, int const _targetLevel, int const _curLevel) {
	if (_node == NULL) return 0;
	if (_targetLevel == _curLevel) {
		printf("%d - ", _node->data);
		return 1;
	}
	int count = 0;
	count += PrintWithLevelSub(_node->pLeft, _targetLevel, _curLevel + 1);
	count += PrintWithLevelSub(_node->pRight, _targetLevel, _curLevel + 1);
}

void PrintWithLevel(SNode* const _head, int const _level) {
	if (_head == NULL) { printf("(0)\n"); return; }
	printf("(%d)\n", PrintWithLevelSub(_head, _level, 0));
}
```

Slightly modified version of `PrintAll`.

The method I used to implemented this reminds me of backtracking.

Actually, this *is* backtracking.


# Remove All: Postorder Traversal

```c
void RemoveAll(SNode** const _pHead) {
	if (*_pHead == NULL) return;
	RemoveAll(&((*_pHead)->pLeft));
	RemoveAll(&((*_pHead)->pRight));
	SAFE_FREE(*_pHead);
}
```

This one is straight-forward.


# Count and Depth

```c
int Count(SNode* const _head) {
	if (_head == NULL) return 0;
	int count = 1;
	count += Count(_head->pLeft);
	count += Count(_head->pRight);
	return count;
}
```

I already implemented Count in `PrintAll`
so I just had to copy that and removed redundant codes.

```c
int SubDepth(SNode* const _node, int const _depth) {
	if (_node == NULL) return 0;
	int d = _depth, e;
	e = SubDepth(_node->pLeft, _depth + 1);
	if (d < e) d = e;
	e = SubDepth(_node->pRight, _depth + 1);
	if (d < e) d = e;
	return d;
}

int Depth(SNode* const _head) {
	if (_head == NULL) return 0;
	return SubDepth(_head, 0);
}
```

But Depth function is slightly different.

I could've added `#include <math.h>` and used `max` function
but that's only two `max` I needed so I just used `if`s.

This will come handy later as well.


# Node Getter

```c
SNode* GetNode(SNode* const _head, int const _data) {
	if (_head == NULL) return NULL;
	if (_head->data == _data) return _head;
	if (_head->data > _data) return GetNode(_head->pLeft, _data);
	return GetNode(_head->pRight, _data);
}
```

Straightforward recursive method.

This will come handy for the next one.


# Node Remover

The big one.

```c
void Remove(SNode** const _pHead, int const _data) {
	if (*_pHead == NULL) return;
	if ((*_pHead)->data == _data) { RemoveNode(_pHead);	return; }
	if ((*_pHead)->data > _data) Remove(&((*_pHead)->pLeft), _data);
	else Remove(&((*_pHead)->pRight), _data);
}
```

That's all, folks!


...

anyhow,

```c
void RemoveNode(SNode** const _pNode) {
	if (*_pNode == NULL) return;
	SNode* nodeTop; SNode* nodeBtm;
	if (Depth((*_pNode)->pLeft) > Depth((*_pNode)->pRight)) {
		nodeTop = (*_pNode)->pLeft;
		nodeBtm = (*_pNode)->pRight;
	}
	else {
		nodeTop = (*_pNode)->pRight;
		nodeBtm = (*_pNode)->pLeft;
	}
	if (nodeTop == NULL) { nodeTop = nodeBtm; nodeBtm = NULL; }
	SAFE_FREE(*_pNode);
	*_pNode = nodeTop;
	if (*_pNode != NULL && nodeBtm != NULL) AppendNode(*_pNode, nodeBtm);
}
```

I used `Depth` from earlier so that the one goes deeper
climbs up to the empty spot.

Then used `AppendNode` to append the other node under it.

The result?

```
2 - 5 - 8 - 10 - 15 - 19 - 25 - 31 - (8)
Removed 25: 2 - 5 - 8 - 10 - 15 - 19 - 31 - (7)
```

Clean.