---
layout:     post
title:      "Not So Private"
date:       2023-03-09 12:48:00 +0900
summary:    Rambling about how useless access modifiers are
categories: modding
thumbnail: 
tags:
 - access-modifiers
 - modding
---

As a modder, I have to decompile other person's code, analyse their algorithms,
and insert my own algorithms on front or at the end of the execution to modify
the function of existing game and thus, modding.

(Let us not talk about IL injection)

So, access modifiers in C#.

`public` to allow access from anywhere, `private` for not.
`protected` to allow access in its children, `internal` for the same assembly. (both can be used at once)

That's how they teach, and that's how they use (if they are competent)

So does this actually help with detering *evil* hackers (well, modders are hackers) like us?


# Reflection: Black Magic

Very first way we modders used to access non-public stuff is Reflection.
Ah, yes, good old black magic.

It goes by this:

```csharp
using System.Reflection;
/* ... */
var flags = BindingFlags.Instance | BindingFlags.Public | BindingFlags.NonPublic;
typeof(ClassName).GetField("fieldName", flags).GetValue(classInstance); // equal to classInstance.fieldName. need to cast this.
typeof(ClassName).GetField("fieldName", flags).SetValue(classInstance, newValue); // equal to classInstance.fieldName = newValue.
```

The problem is, Reflection is unreliable and very slow.

If I say slow, it's really slow. (as you hardly care about 'optimization' when modding)

And unreliable, which means there are many cases
we say "works on my machine" whenever we hear outcries of end-users.

As such, it fits into the definition of black magic perfectly.


# Bee Magic

This is a code snippet by pipi toki.

```csharp
using System;
using System.Security;
using System.Runtime.CompilerServices;
using System.Security.Permissions;

[assembly: IgnoresAccessChecksTo("Assembly-CSharp")]
[assembly: SecurityPermission(SecurityAction.RequestMinimum, SkipVerification = true)]
[module: UnverifiableCode]

namespace System.Runtime.CompilerServices
{
    [AttributeUsage(AttributeTargets.Assembly, AllowMultiple = true)]
    public class IgnoresAccessChecksToAttribute : Attribute
    {
        public IgnoresAccessChecksToAttribute(string assemblyName)
        {
            AssemblyName = assemblyName;
        }
 
        public string AssemblyName { get; }
    }
}
``` 

As long as a cs file containing this is included in the assembly,
this assmebly can access everything in `Assembly-CSharp` (Unity game's compiled codes)
ignoring access modifiers.


# Rain 2 Magic

What contemporary modders use for this is from Risk of Rain 2 modding community:

```csharp
using System.Security;
using System.Security.Permissions;

[module: UnverifiableCode]
[assembly: SecurityPermission( SecurityAction.RequestMinimum, SkipVerification = true )]
```

We usually put this above our `BaseUnityPlugin` class.


# So why use access modifiers?

Uh... it makes using IntelliSense more endurable?

Okay, I honestly cannot find the answer.
Modders gonna mod.

