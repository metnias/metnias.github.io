---
layout:     post
title:      "Starting This Blog"
date:       2023-03-08 20:53:00 +0900
summary:    Short writing to mark this blog's beginning.
categories: blog
thumbnail: 
tags:
 - csharp
 - cpp
 - c
 - ps
---


Okay, so let's start this thing.

I'll keep those posts from the template temporarily to check them from time to time.

So, coding! I began solving Baekjoon with `C++17`.
`C11` is also a thing but I find that pure C language has very limited usage, unlike `C++` in Unreal.

Yeah, I'll probably just use `C#` and `C++` for problem-solving in the following days.

To test this website's code display capability, let's show all three of them.

# 6558 on C#

Baekjoon [6558][B6558] is about (dis)proving Goldbach's conjecture.
Input is multiple even numbers between 6 and 1000000, and there will be numerous cases (less than 100000) terminated by zero.

```csharp
int X = 1000001, i = 2, j; var B = new bool[X];
for (; i < X; i++) if (!B[i]) for (j = i * 2; j < X; j += i) B[j] = true;
HashSet<int> S = new(); List<int> L = new();
for (i = 2; i < X; i++) if (!B[i]) { S.Add(i); L.Add(i); }
StreamReader R = new(new BufferedStream(Console.OpenStandardInput()));
StreamWriter W = new(new BufferedStream(Console.OpenStandardOutput()));
L: j = int.Parse(R.ReadLine());
if (j < 6) { R.Close(); W.Close(); return; }
for (i = 0; ; i++) if (S.Contains(j - L[i])) { W.WriteLine($"{j} = {L[i]} + {j - L[i]}"); goto L; }
```

So I pre-calculated all prime numbers under that max limit beforehand using the Sieve of Eratosthenes,
and store them in List (to use index) and HashSet (to use Contains with O(1)).

And for each input, I searched the minimum prime number that satisfies the condition.


# 1152 on C

Baekjoon [1152][B1152] is to count the number of words in the string (max length 1000000).

The input is separated by spaces, which never appear multiple times in a row.
However, it can appear on the front or the back, so just counting the number of spaces will require two edge cases handling.

```c
#include <stdio.h>
#include <string.h>
int main() {
    int i = 0, c = 0, n = 0;
    char S[1000001];
    gets(S);
    for (; i < strlen(S); i++) {
        if (S[i] == ' ') c = 0;
        else c++;
        if (c == 1) n++;
    }
    printf("%d", n);
}
```

I picked this one to see how `C` handles `string` (or how it does not) and how the `for` loop works for C.

Well, `C` and `C++`'s `for` are the same as `C#`'s, thank you very much.

Anyhow, the way I did it was, if a word starts, tick the number of words for 1st letter of the word.
So this covers both edge cases, and it can even handle multiple blank spaces.


# 25206 on C++

Baekjoon [25206][B25206] is to calculate the score from the given input.

Input is 20 rows in `SubjectName Credit Grade` format.

```cpp
#include <iostream>
int main() {
    char S[51], G[3];
    int i = 0;
    double m = 0, s = 0, g, c;
    for (; i < 20; i++) {
        std::cin >> S >> g >> G;
        if (G[0] == 'P') continue;
        c = 'E' - G[0];
        if (c < 0) c = 0;
        else if (G[1] == '+') c += 0.5;
        s += g; m += g * c;
    }
    std::cout << m / s;
}
```

Oh, this gives me a good opportunity to test the table function of this blog as well!

|  A+ | A0  | B+  | B0  | C+  | C0  | D+  | D0  | F   |
|:---:|-----|-----|-----|-----|-----|-----|-----|-----|
| 4.5 | 4.0 | 3.5 | 3.0 | 2.5 | 2.0 | 1.5 | 1.0 | 0.0 |

So that's how we get the grade, and I just subtracted the ASCII from `'E'` for the base,
and added `0.5` when the second letter is '+'.

Also, there's an edge case that when the grade is `'P'`, that line should be ignored.

# Conclusion

I'll probably use this blog to explain how I solved daily Baekjoon stuff.

Let's see if this post breaks the blog or not.


[B6558]: https://www.acmicpc.net/problem/6588
[B1152]: https://www.acmicpc.net/problem/1152
[B25206]: https://www.acmicpc.net/problem/25206
