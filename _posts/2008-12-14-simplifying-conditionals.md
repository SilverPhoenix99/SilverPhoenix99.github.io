---
title:  "Simplifying conditionals"
date:   2008-12-14 10:22:00 GMT
tags:   branching conditionals simplifying
---
My job requires me to work in [RPG](http://en.wikipedia.org/wiki/IBM_RPG), an old, old language. Worse yet I cannot use the free keyword because of our pre-processor, and I don't see that changing for some time.

With that in mind, I show you what I have learned when working with conditionals (specifically `if`s).

Let's first assume that lowercase are boolean expressions and that uppercase are sets of statements and expressions (i.e., code instructions). Next I present some snippets, following a demonstration on how to simplify them.

Note: Ignore the brackets. I only use then to make clear where code breaks.

## Case 1

```c
if(a)
{
    if(b)
    {
        A
    }
}
```

Simplification:

```c
if(a && b)
{
    A
}
```

## Case 2

Yes, I do find many of these:

```c
if(a)
{
    A
}

if(b)
{
    A
}
```

Simplification:

```c
if(a || b)
{
    A
}
```

## Case 3

```c
if(a)
{
    if(b)
    {
        B
    }
}
else
{
    A
}
```

Intermediate step:

```c
if(!a) // we reverse the order of the condition
{
    A
}
else
{
    if(b)
    {
        B
    }
}
```

Simplification:

```c
if(!a)
{
    A
}
else if(b) // it's the exact same code, but with better identation
{
    B
}
```

To some people this can seem quite obvious, but I find that most people don't bother to apply these rules, which makes readability more difficult.