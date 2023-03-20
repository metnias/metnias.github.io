---
layout:     post
title:      "Double Linked List"
date:       2023-03-20 12:25 +0900
summary:    Notes about Implementing Double Linked List
categories: study
thumbnail: 
tags:
 - c
 - cpp
 - data-structure
 - linked-list
---


Unlike in the [last post][LastPost], 
this time, we did Double Linked List.

```c
typedef struct _SNode {
	int data;
	struct _SNode* pNext;
	struct _SNode* pPrev;
} SNode;
```

Also I made this for Try functions.

```c
typedef int bool;
#define true 1
#define false 0
```

Let's do this.

# Commonly used sub-functions

```c
SNode* CreateNode(int const _data) {
	SNode* node;
	SURE_MALLOC(SNode, node);
	node->data = _data;
	node->pPrev = NULL;
	node->pNext = NULL;
	return node;
}
```

int data to SNode pointer converter.

```c
void AttachNodes(SNode* const _front, SNode* const _back) {
	if (_front == NULL) {
		if (_back == NULL) return;
		_back->pNext = NULL; return;
	}
	_front->pNext = _back;
	if (_front->pNext) _front->pNext->pPrev = _front;
}
```

Then, node attachers with NULL handler.

These will be useful for following functions.


# Node Inserter

```c
void InsertNodeAfter(SNode* const _node, int const _data) {
	if (_node == NULL) return;
	SNode* node = CreateNode(_data);
	SNode* pNext = _node->pNext;
	AttachNodes(_node, node);
	AttachNodes(node, pNext);
}
```

Inserts node after the node.
This one's the same as the one I made in Single Linked List.

```c
void InsertNodeBefore(SNode* const _node, int const _data) {
	if (_node == NULL) return;
	SNode* node = CreateNode(_data);
	SNode* pPrev = _node->pPrev;
	AttachNodes(node, _node);
	AttachNodes(pPrev, node);
}
```

But now I can make Insert Node before the current one with ease.

```c
void InsertAt(SNode** const _pHead, int const _index, int const _data) {
	InsertNodeAt(_pHead, _index, CreateNode(_data));
}

void InsertNodeAt(SNode** const _pHead, int const _index, SNode* _node) {
	if (*_pHead == NULL) return;
	if (_index < 1) {
		AttachNodes(_node, *_pHead);
		*_pHead = _node;
		return;
	}
	SNode* prev;
	if (!TryGetNodeAt(*_pHead, _index - 1, &prev)) return;
	AttachNodes(_node, prev->pNext);
	AttachNodes(prev, _node);
}
```

Node Insert at index.
O(n) obviously.


# Node Remover

```c
void RemoveAt(SNode** const _pHead, int const _index) {
	if (*_pHead == NULL) return;
	if (_index < 1) {
		SNode* newHead = (*_pHead)->pNext;
		SAFE_FREE(*_pHead);
		*_pHead = newHead;
		return;
	}
	SNode* target;
	if (TryGetNodeAt(*_pHead, _index, &target)) RemoveNode(target);
}
```

Removes node with index. As such, O(n).


```c
void RemoveNode(SNode** const _pNode) {
	if (*_pNode == NULL) return;
	AttachNodes((*_pNode)->pPrev, (*_pNode)->pNext);
	SAFE_FREE(*_pNode);
}
```

And now, as we don't need to trace from all the way back to get
previous node, this one becomes O(1).

Although, this cannot remove head or tail.
(as I've saved them in local variable so I cannot move them in here)


[LastPost]: /study/2023/03/16/linked-list-part-two/