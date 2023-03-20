---
layout:     post
title:      "Linked List Pt 2"
date:       2023-03-16 13:49 +0900
summary:    Notes about Implementing Linked List's functions
categories: study
thumbnail: 
tags:
 - c
 - cpp
 - data-structure
 - linked-list
---


Continuing from the [last post][LastPost], 
now we insert and remove individual nodes.


# Node Getter

```c
char TryGetNodeAt(SNode* const _pHead, int const _index, SNode** pNode) {
    if (_pHead == NULL) return 0;
    *pNode = _pHead;
    for (int i = 0; i < _index; ++i) {
        *pNode = (*pNode)->pNext;
        if (*pNode == NULL) return 0;
    }
    return 1;
}
```

```c
char TryGetNode(SNode* const _pHead, int const _data, SNode** pNode) {
    if (_pHead == NULL) return 0;
    *pNode = _pHead;
    while (1) {
        if ((*pNode)->data == _data) break;
        *pNode = (*pNode)->pNext;
        if (*pNode == NULL) return 0;
    }
    return 1;
}
```

These two are very similar.
O(n).


# Node Inserter

```c
void InsertNodeAfter(SNode* const _pNode, int const _data) {
    if (_pNode == NULL) return;
    SNode* node;
    SURE_MALLOC(SNode, node);
    node->data = _data;
    SNode* pNext = _pNode->pNext;
    _pNode->pNext = node; // Insert
    node->pNext = pNext;
}
```

Hey, O(1)!

Now we're getting somewhere.


# Node Remover

```c
void RemoveNodeAfter(SNode* const _pPrev) {
    if (_pPrev == NULL) return;
    SNode* pCur;
    if (_pPrev->pNext) pCur = _pPrev->pNext;
    else return; // no next
    _pPrev->pNext = pCur->pNext; // Pull
    SAFE_FREE(pCur);
}
```

This one's also O(1).

However, the instructor wanted us to remove the node we input,
not the one after.

Which means we need to find the node previous to this,
and as this is Single Linked List, I have to scan the list to
find that, meaning it's O(n).

```c
void RemoveNode(SNode** _pHead, SNode** const _pNode) {
    if (_pHead == NULL || _pNode == NULL) return;
    if (_pHead == _pNode) { RemoveNodeAt(_pHead, 0); return; }
    SNode* pPrev = *_pHead;
    while (1) {
        if (pPrev->pNext == NULL) return;
        if (pPrev->pNext == *_pNode) break;
        pPrev = pPrev->pNext;
    }
    RemoveNodeAfter(pPrev);
}
```

Nasty.

Count function was too simple.
I just had to remove some parts from `Print`.
O(n).


[LastPost]: /study/2023/03/15/linked-list/