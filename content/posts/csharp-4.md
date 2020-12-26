---
title: "C# 4 - the One with Dynamic Typing"
date: "2019-10-23T00:00:00.000Z"
draft: false
---

C# 4 was released in April 2010 with Visual Studio 2010. This release was mainly focused on improving interoperability between systems so many of the features aren't useful on a day-to-day basis. Let's have a geez at **dynamic types**, **optional parameters** and **named arguments**.

#### Dynamic Typing

Dynamic types change the binding of types from compile time to execution time. We create dynamic types by using the ```dynamic``` keyword.
```
dynamic text = "Tyler Durden lives.";
```

According to [this answer](https://stackoverflow.com/questions/21544784/advantage-of-using-dynamic-type-in-c-sharp) on Stack Overflow, one of the benefits of dynamic typing is better interoperability with languages such as Python or JavaScript.

I've left out any details here because I can't see myself using this feature unless I had no choice. I'm a fan of static typing and would prefer to have the compiler complaining than have to deal with run-time bugs. I just included as I think it could be useful to have an understanding of it incase you come across it in a foreign code base.

#### Optional Parameters

Optional parameters allow us to specify a default value for a parameter.
```
static void Test(int x, int y = 100, int z = 200)
{
    Console.WriteLine($"[ x = {x}, y = {y}, z = {z} ]");
}
```

We can override this value by providing an argument or just leave the argument empty if we want the default value.
```
Test(1);
Test(1, 2);
Test(1, 2, 3);

// Output
[ x = 1, y = 100, z = 200 ]
[ x = 1, y = 2, z = 200 ]
[ x = 1, y = 2, z = 3 ]
```

#### Named Arguments

Named arguments allow us to specify the name of arguments so it's clear which parameter it relates to. I find this helps with readability if your arguments are similar in type or value. All of the below examples are valid.

```
// No named arguments
Test(1, 2, 3);

// Named arguments in order
Test(x: 1, y: 2, z: 3);

// Named arguments out of order
Test(z: 1, x: 2, y: 3);

// Mix of positional and named arguments
Test(1, y: 2, 3);
```