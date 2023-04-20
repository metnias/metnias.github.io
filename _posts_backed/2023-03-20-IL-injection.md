---
layout:     post
title:      "IL Injection"
date:       2023-03-20 12:25 +0900
summary:    Rambling about IL injection
categories: blog
thumbnail: 
tags:
 - csharp
 - il
 - opcode
 - monomod
 - hooking
---


Let's start by recapping on how we do normal hooking.

```csharp
private static void UpdatePatch(On.Player.orig_Update orig, Player self, bool eu)
{
    // codes to run before original
	// can also swap parameters
    orig(self, eu); // can be skipped (but never recommended)
	// codes to run after original
	// can also swap returns (if there's one)
}
```

Usually, even if the original code changes parts where I want to touch,
I can edit that afterwards to make the result how I want it to be.

However, sometimes that doesn't work, unless you skip the original code.

Which, should be avoided at all cost to keep compatibility with other mods.

This may be something like accessing local variables,
or accessing variables within codes.

That's where IL injection comes in.


# What is IL

C# source codes compile to **Intermediate Language** code, or **IL** Code,
in the form of DLL file.

![CsharpCompile](/images/230320-C.png)

If you compile C or C++ code, it'll be compiled to machine code.
It's fast, yes, but it only runs in specific CPU or OS.

However, Intermediate Language is independent from CPU or OS.
This assembly-esque code runs over a virtual machine called
**Common Language Runtime(CLR)**.

(Apparently, you *can* compile C++ with .NET C++ to compile into
IL code)

When you use [dnSpy][dnSpy GitHub], you can decompile .NET and Unity
assemblies.
(You can also edit this with dnSpy directly but
that is years old modding meta)

But If you're doing IL hooking, you really should get [ILSpy][ILSpy GitHub].
It has IL with C# mode, which is not perfect, but saves the amount of time you decoding IL code significantly.


# How IL Injection works







[dnSpy GitHub]: https://github.com/dnSpy/dnSpy
[ILSpy GitHub]: https://github.com/icsharpcode/ILSpy