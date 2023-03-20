---
layout:     post
title:      "Linked List"
date:       2023-03-15 14:21 +0900
summary:    Notes about Structure and Linked List
categories: study
thumbnail: 
tags:
 - c
 - cpp
 - data-structure
 - struct
 - linked-list
---


Linked List.

I see the benefit of this, but I've never encountered
an opportunity to use this in practice.

(and also C#'s `LinkedList<T>` is a bit hard to figure out)


# Structure

Let's talk about `struct` first as we'll need them for
implementing Linked List.

```c
struct SExample {
    int i;        // Structure Member Variables
    int ii;
};
```

 * S in the name marks that it is a `struct`

They store data like an Array. So that's two ints, meaning the size
is 8B.

### Structure Padding

```c
struct SExample {
    int i; // 4B
    char c; // 1B
    char cc; // 1B
}; // : 8B
```

However, the size of struct is always a multiple of 4 Bytes.
So with `char` (1B), 2B is wasted in this example.

```c
struct SExample {
    char c; // 4B (1B)
    int i; // 4B
    char cc; // 4B
}; // : 12B
```

Meaning the order is important.

Also the size for padding is based on
the largest data in the structure.

```c
struct SExample {
    int i; // 8B (4B)
    double d; // 8B
    char c; // 8B (1B
    char cc; // +1B)
}; // : 24B
```

In this example with `double`, padding is based around 8B.

```c
struct SExample {
    int i;
    double d;
    struct SLarge str; // 24B
    char c;
    char cc;
}; // : 48B
```

Hmmmm, this example ignores the order, though.


### typedef struct

```c
struct _SNode {
    int data;
    struct _SNode* pNext;
};
typedef struct _SNode SNode;
```

Since writing `struct SNode` for each time is tedious,
we can use `typedef`.

```c
typedef struct _SNode {
    int data;
    struct _SNode* pNext;
} SNode;
```

This does the same thing.

You can remove `_SNode` but then you cannot use
`struct _SNode` anymore.

so let's go back to why we're making this SNode.


# Linked List in C

```c
void main() {
    SNode* pHead = NULL;

    for (int i = 0; i < 10; ++i) {
        AddNode(&pHead, i + 1);
        Print(pHead);
    }

    RemoveAll(&pHead);
}
```

I made the first node to be null to start with,
so that the length of this list equals the length of actual data.


### AddNode

```c
void AddNode(SNode** const _pHead, int const _data) {
    SNode* node;
    if (*_pHead == NULL) {
        SURE_MALLOC(SNode, *_pHead);
        node = *_pHead;
    }
    else {
        node = *_pHead;
        while (node->pNext) node = (*node).pNext;
        SURE_MALLOC(SNode, node->pNext);
        node = (*node).pNext;
    }
    node->data = _data;
    node->pNext = NULL;
}
```

The way I did meant that I needed a NULL checker.

Oh, for that `SURE_MALLOC`,

```c
#define SURE_MALLOC(t, p) do p = (t ## *)malloc(sizeof(t)); while (p == NULL);
#define SAFE_FREE(p) if (p) { free(p); (p) = NULL; }
```


### RemoveAll

Originally I used iterator for this:

```c
void RemoveAll(SNode** const _pHead) {
    if (*_pHead == NULL) return;
    SNode* node = *_pHead;
    SNode* lastNode = node;
    while (node->pNext) {
        lastNode = node;
        node = (*node).pNext;
        SAFE_FREE(lastNode);
    }
    SAFE_FREE(node);
} // This removes nodes from front to end
```

But, yeah, you can use recursive function which is way simpler.

```c
void RemoveAll(SNode** const _pHead) {
    if (*_pHead == NULL) return;
    if ((*_pHead)->pNext) RemoveAll(&((*_pHead)->pNext));
    SAFE_FREE(*_pHead);
} // This removes nodes from end to front
```


### Print

```c
void Print(SNode* const _pHead) {
    if (_pHead == NULL) return;
    SNode* node = _pHead;
    int count = 0;
    while (1) {
        printf("%d - ", node->data); ++count;
        if (node->pNext == NULL) break;
        node = node->pNext;
    }
    printf("(%d)\n", count);
}
```

Straightforward on this one.


# LinkedList in C#

`System.Collections.Generic.LinkedList<T>` is doubly linked list,
which means you can go backwards as well.
(The one we've implemented in C is one-way)

Baekjoon [5397][BOJ5397] is a good way to practice this.

```csharp
LinkedList<char> L = new();
var n = L.AddFirst('0');
// ... //
n = L.AddAfter(n, I[i]);
```

Linked List allows adding and removing an item in the middle in O(1),
as long as you hold the node.

However, if you didn't, searching node takes O(n).

It is a data structure for very specific situations like this.


 
[BOJ5397]: https://www.acmicpc.net/problem/5397
