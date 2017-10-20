---
title:  "Don't try this!"
date:   2008-11-16 10:54:00 GMT
tags:   try catch "exception handling"
---
Some days ago, at work I found this little piece of code:

```java
// It's ok, because myObj can be null.
try
{
    myObj.doSomething();
}
catch(NullPointerException e) {}
```

I don't know who did it, but it would be easy to find out (in CVS), though that's beyond the point.

This type of code is very wrong. In my experience, and in most cases, you should use an `if`.

There are two problems that can arrive from this try/catch block.

First is the performance. At the very least, the block has to construct the exception, and that takes too much time when
compared to `if(myObj != null)`.

Secondly, if a `NullPointerException` occurs in `doSomething()` it will get caught in the catch block, and may cause
unpredictable results.

Even if the person who programmed the method knows it is safe, and will never throw that exception, the other people who
work with that class may not, which breaks much of the encapsulation.
Take me, for example. I had to see what that method does to make sure I could replace it with an `if`.

This solution would be simpler and tidier:

```java
if(myObj != null)
{
    myObj.doSomething();
}
```
