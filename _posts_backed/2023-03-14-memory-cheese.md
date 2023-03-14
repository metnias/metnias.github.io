---
layout:     post
title:      "Memory Cheese"
date:       2023-03-14 18:09 +0900
summary:    Notes about Memory Allocate and Vector
categories: blog
thumbnail: 
tags:
 - c
 - cpp
 - malloc
 - memory-allocate
---

```c
const int LEN = 10;
int* pArr;
do pArr = (int*)malloc(sizeof(int) * LEN); while (pArr == NULL);
pArr[0] = 10;
*(pArr + 1) = 20;
if (pArr != NULL) {	free(pArr);	pArr = NULL; } // freeing memory
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

	char find[5] = "Data";
	int i = 0; long long t = 0; int len = 4;
	char* address = malloc(sizeof(find));
	char* origAddress = address;
	if (address == NULL) { free(origAddress); return 1; }
	printf("Began search from %p\n", address);
	while (++t) {
		if (address[0] == find[i]) {
			++i;
			if (i == len) { address -= len; break; }
			++address;
		}
		else { i = 0; address += sizeof(char); }
	}
	char replace[5] = "LULZ";
	printf("Found [%s] at %p (Try: %zd)\n", address, address, t);

	printf("Data: %s(%p)\n", str, &str);
	free(origAddress);
	return 0;
}
```

So, what did this get me?

It searched and found what's been outputted with `printf` so far!

Not expected, but this did explain how Heap and Memory are separated.
(Removing the initial `printf` gave me access violation which is
a fun police)

Yes, let's talk about them.


# Memory, Stack, Heap



[LastPost]: /study/2023/03/14/memory-allocate/