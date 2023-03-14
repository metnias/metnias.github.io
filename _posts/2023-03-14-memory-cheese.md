---
layout:     post
title:      "Memory Cheese"
date:       2023-03-14 16:36 +0900
summary:    More research about Memory Allocate
categories: blog
thumbnail: 
tags:
 - c
 - malloc
 - memory-allocate
 - heap
 - stack
---

```c
const int LEN = 10;
int* pArr;
do pArr = (int*)malloc(sizeof(int) * LEN); while (pArr == NULL);
pArr[0] = 10;
*(pArr + 1) = 20;
if (pArr != NULL) { free(pArr); pArr = NULL; } // freeing memory
```

I'll bring the same example from the [last post][LastPost].

When I'm inputting index for these array,
it does not check whether there's IndexOutOfRange error unlike C#.

So with C, you may access memory you are not supposed to.

This gave me an idea: since the number of memory address is finite,
if I were to scan memory 0 to end to find specific value (brute force),
can I find that memory location and edit that value?


# Cheese

Basically I'm trying very rough Cheat-O-Matic implementation,
for the science of course.

```c
#include <stdio.h>
#include <malloc.h>

char* str = "My Private Data";

int main() {
    printf("Data: %s(%p)\n", str, &str);

    char* find = "My Private";
    int i = 0; long long t = 0; int len = 10;
    char* address = malloc(sizeof(find));
    char* origAddress = address;
    if (address == NULL) { free(origAddress); return 1; }
    printf("Began search from %p\n", address);
    while (++t) {
        if (address[0] == find[i]) {
            ++i; ++address;
            if (i == len) { address -= len; break; }
        }
        else { i = 0; ++address; }
    }
    printf("Found [%s] at %p (Try: %zd)\n", address, address, t);
    char* replace = "YourStolen";
    for (i = 0; i < len; ++i) *(address + i) = replace[i];

    printf("Data: %s(%p)\n", str, &str);
    free(origAddress);
    return 0;
}
```

So, what did this get me?

```
Data: YourStolen Data(00007FF6841FC000)
Began search from 000001C16B0DD550
Found [My Private Data(00007FF6841FC000)
Began search from 000001C16B0DD550
Found [] at 000001C16B0DED06 (Try: 6080)
Data: My Private Data(00007FF6841FC000)
```

It searched and found what's been outputted with `printf` so far,
starting from Ln 1 Col 7!

```
address:

My Private Data(00007FF6B0A4C000)
Began search from 000002586F2DD310
Found [
```

Then it replaced what's been printed as well!

```
address[0] to [9] replaced to "YourStolen":

YourStolen Data(00007FF6B0A4C000)
Began search from 000002586F2DD310
Found [
```

Not expected, but this did explain how Heap and Memory are separated.

Also this tells me that Console outputs are stored in Heap.

Yes, let's talk about Heap.


Side note: Removing the initial `printf` gave me access violation
which is a fun police.


# Stack and Heap

![HeapImage](/images/230314-HOY4C.png)

I found that image from [stackoverflow][HeapStackOverflow].

So those are stored in virtual memory.
Stack is managed by the compiler, and Heap is managed by us.

Also there are Static Data and Instructions.


# Read-only?

```c
#include <stdio.h>

char* str = "My Private Data";

int main() {
    printf("Data: [%s](%p)\n", str, &str);

    char* find = "My Private";
    int i = 0; long long t = 0; int len = 10;
    char* address = find;
    printf("Began search from %p\n", --address);
    while (++t) {
        if (address[0] == find[i]) {
            ++i; ++address;
            if (i == len) { address -= len; break; }
        }
        else { --address; address -= i; i = 0; }
    }
    printf("Found [%s] at %p (Try: %zd)\n", address, address, t);
    char* replace = "YourStolen";
    for (i = 0; i < len; ++i) *(address + i) = replace[i];

    printf("Data: [%s](%p)\n", str, &str);
    return 0;
}
```

So I tried starting from the same memory space this time,
which is Stack.

This did not go well.

Actually, any attempt to edit `str` (like `str[0] = 'A'`)
threw an error.

```
Data: [My Private Data](00007FF66189C000)
Began search from 00007FF661899C5F
Found [My Private Data] at 00007FF661899BB0 (Try: 185)
Data: [My Private Data](00007FF66189C000)
```

So the hypothesis I can think of is,
that `char*` is Immutable data thus any way of editing that,
even with *valid* access methods, is forbidden.



[LastPost]: /study/2023/03/14/memory-allocate/
[HeapStackOverflow]: https://stackoverflow.com/questions/32418750/stack-and-heap-locations-in-ram/32418775