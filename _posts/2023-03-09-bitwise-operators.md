---
layout:     post
title:      "Bitwise Operators"
date:       2023-03-09 13:37:00 +0900
summary:    Notes about bitwise operators
categories: study
thumbnail: 
tags:
 - csharp
 - cpp
 - bitwise-operators
 - bitmask
---

Bitwise operators!

Wow, today's class could have been much more disasterous for me
if I haven't studied this already thanks to Baekjoon.

# Non-conditonal Boolean logical operators

Let's start by [Boolean operators][BoolGuide] first, our beloved.

```csharp
bool B(string text)
{ Console.WriteLine(text); return true; }
Console.WriteLine(false & B("AND"));
Console.WriteLine(false && B("Conditional AND"));

Console.WriteLine(true | B("OR"));
Console.WriteLine(true || B("Conditional OR"));

/// Output:
/// False
/// AND
/// False
/// True
/// OR
/// True
```

Which looks exactly the same as bitwise operators, and behaves similarly as well.

Even though we use Conditional counterparts mostly,
there are times that we need to evalute second one as well;
especially when another cooperator put important function inside a method that returns bool
for some dubious reasons.

Or `TryGetValue`.

The more you know.

# Bitwise Operators

Hey, I did use this for one of the final games with the last instructor!

[Here][ShooterCode] is that code if you wanna check that out.
In summary, I made an Enum with the name of GameObjects for 4 walls,
and use their names for flags.
So I used 1 int flags instead of 4 bool flags, wasting 32 bits instead of 4 bits.

Didn't I already say something about modders hardly care about optimization or-

Let's see some cpp examples.

```cpp
enum EOptions { kBGM = 1 << 2, kFULLSCREEN = 1 << 5 };

int main() {
    unsigned char options = 0;
    options |= kBGM;
    options |= kFULLSCREEN; // OR: turn on
    printf("options: "); printbyte(options); printf("\n");
    options ^= kFULLSCREEN; // XOR: toggle
    options &= ~kFULLSCREEN; // AND~: turn off
    printf("options: "); printbyte(options); printf("\n");
    printf("options BGM?: %s\n", options & kBGM ? "T" : "F");

    printf("options: "); printbyte(options); printf("\n");

    return 0;
}

int printbyte(unsigned char byte) {
    for (int i = 7; i >= 0; --i) printf("%d", (byte & 1 << i) > 0);
    return 0;
}
```

Also, if I need to multiply/divide by the power of 2,
I can use bitshift.

Also a friend of mine taught me this black magic using XOR:

```cpp
int a = 5, b = 6;
printf("%d %d", a, b); // OUTPUT: 5 6
a ^= b; b ^= a; a ^= b;
printf("%d %d", a, b); // OUTPUT: 6 5
```

Magic.


[BoolGuide]: https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/boolean-logical-operators
[ShooterCode]: https://github.com/metnias/VerticalShooter/blob/main/Assets/Scripts/Player_Controller.cs#L190
