---
layout:     post
title:      "Memory Allocate and Vector"
date:       2023-03-14 15:41 +0900
summary:    Notes about Memory Allocate and Vector
categories: study
thumbnail: 
tags:
 - c
 - cpp
 - malloc
 - memory-allocate
 - data-structure
 - vector
 - macro
---

The first time I learned about this is when I tried to create
a bool array with 1000001 size in `C++` to solve Baekjoon [17103][[BOJ17103]].

IDE suggested me to use `malloc`, which made me research,
and thus, this is [my code][BOJ17103Solution] using that.


# Dynamic Memory Allocate

```c
const int LEN = 10;
int* pArr;
do pArr = (int*)malloc(sizeof(int) * LEN); while (pArr == NULL);
pArr[0] = 10;
*(pArr + 1) = 20;
if (pArr != NULL) { free(pArr); pArr = NULL; } // freeing memory
```

Hmmmm...?

I found something intriguing but
I'll talk more about that in the [following post][NextPost].


# Vector

Or `List<T>` in `C#`.

The instructor said it's equivalent of `ArrayList`,
as many other blogs online have claimed,
but in my experience, it is not.

`ArrayList` accepts *any* data type in it.

But `List<T>` accepts one data type for each, like `vector`.

(Calling that a vector is quite confusing, especially with
Unity's `Vector2D` which is a two `float` container)


### Macro Function

```c
#define SAFE_FREE(p)\
if (p) {\
        free(p);\
        (p) = NULL;\
    } // \ in the end of line joins the next line
```

I don't think the instructor taught us the difference
between [macro function][Macro] and normal one.

Macro function is *NOT* technically a function,
but replaces itself its definition on **Preprocessing**.

This is why we're using `(p)` instead of `p`.

```c
#define MULTIFY(x,y) x*y
// ... //
printf("%d", MULTIFY(2 + 3, 3 + 2));
// => printf("%d", 2 + 3 * 3 + 2); => 13
```

In cases like this, as MULTIFY is converted to its definition
on compile, the result may not be what we desire.

```c
#define MULTIFY(x,y) (x)*(y)
// ... //
printf("%d", MULTIFY(2 + 3, 3 + 2));
// => printf("%d", (2 + 3) * (3 + 2)); => 25
```

Also, they do not care about the type of its parameters.

There are other things like `#` (converts to string)
and `##` (concat).


### Create Array

```c
void CreateArray(void** const _pArr, const size_t _size) {
    SAFE_FREE(*_pArr);
    do *_pArr = malloc(_size); while (*_pArr == NULL);
}
```

in `C#`, it'd be just `new int[_size];`
but in `C`, we need a lot of writing to just do that.

`C` may be faster for computer, but
in the sense of programmers' productivity, neigh.


### Expand Array

```c
void ExpandArray(int** _pArr, int* const _pArrLen) {
    int* newArr = NULL;
    CreateArray(&newArr, *_pArrLen * sizeof(int) * 2);
    for (int i = 0; i < *_pArrLen; ++i) newArr[i] = (*_pArr)[i]; // clone
    *_pArrLen *= 2;
    SAFE_FREE(*_pArr);
    *_pArr = newArr;
}
```

Or in `C#`, `Array.Resize(ref arr, arr.Length * 2)`.

Obviously, if you need to use `Array.Resize` in C#,
you're doing this very wrong.

...or you're a modder like me
and trying to add new item to where it's not supposed to.


### Add and Print

```c
void Add(int** _pArr, int* const _pArrLen, int* const _pIndex, const int _data) {
    if (*_pIndex >= *_pArrLen) ExpandArray(_pArr, _pArrLen);
    (*_pArr)[*_pIndex] = _data;
    ++(*_pIndex);
}
```

With `ExpandArray` being in separate function,
the code is self-explanatory.

For naming functions, it is important that naming them
for their intent, not how they work.
You'd know how they work by looking at the code.


```c
void Print(const int* _pArr, const int _index) {
    for (int i = 0; i < _index; ++i) printf("%d - ", _pArr[i]);
    printf("(%d)\n", _index);
}
```

Also this.


[BOJ17103]: https://www.acmicpc.net/problem/17103
[BOJ17103Solution]: https://www.acmicpc.net/source/57272891
[Macro]: https://learn.microsoft.com/en-us/cpp/preprocessor/macros-c-cpp?view=msvc-170
[NextPost]: /blog/2023/03/14/memory-cheese/