---
title: "C# 7 - the One with Tuples"
date: "2019-11-02T00:00:00.000Z"
draft: false
---

C# 7 is the first version since C# 1 to have minor versions. 7.0 was released in March 2017 with Visual Studio 2017 and was followed by 7.1, 7.2 and 7.3 over the course of the next year and a bit. The big M will probably continue this trend and carry on with minor releases in C# 8 and onwards. 

We'll be finishing off the C# anatomy sessions with **tuples**, **pattern matching** and **ref-related features**.

#### Tuples
Tuples are bags of variables. You can use them as local variables, you can pass them as arguments, you can return them from methods. I like tuples and I think you will too. 

We create a tuple by wrapping a list of variables in brackets. There are a variety of ways they can be created. All of the below are equivalent.

```
var a = 1;
var b = 2;
var tuple = (a, b);
```
```
(int a, int b) tuple = (1, 2);
```
```
var tuple = (a: 1, b: 2);
```

We can access the variables within tuples in 2 ways. If the variable names are set, we can access using the names.
```
Console.WriteLine(tuple.a);
Console.WriteLine(tuple.b);

// Output
1
2
```

Or we can access using the Item property.
```
Console.WriteLine(tuple.Item1);
Console.WriteLine(tuple.Item2);

// Output
1
2
```

The ability to return tuples from methods is an absolute lifesaver.

```
static (int min, int max) MinMax(int[] input)
{
    (int Min, int Max) result;
    // Find and set min and max values.
    return result;
}
```

Version 7.3 introduced support for tuple equality operators. This works by comparing each member of 2 tuples in order.

```
var left = (a: 100, b: "text");
var right = (x: 100, y: "text");
Console.WriteLine(left == right);
Console.WriteLine(left != right);

// Output
True
False
```

#### Pattern Matching
We already use ```if``` and ```switch``` statements to test values. Version 7 sees improvements to the ```is``` keyword and ```switch``` statements to allow cleaner matching of values.

##### Using Is
We use ```is``` to test a match.
```
if(input is string)
{
    Console.WriteLine("Hooray!");
}
```

The improvements to the ```is``` keyword allow us to assign a variable if the match passes.
```
if(input is string text)
{
    Console.WriteLine(text);
}
```

##### Switch Syntax
The above examples are pretty straightforward. As we add more cases, using ```if``` statements becomes tedious. The improvements to switch statements allow us to infer the ```is``` clause making them a much better choice for pattern matching. You'll see this in action below.

There are 3 types of patterns: constant, type and var. Let's take a look.

##### Constant Patterns
Does the input match this constant value?
```
static string Example(string input)
{
    switch(input)
    {
        case "hello":
            return input;
        default:
            return "Input does not match."
    }
}
```

##### Type Patterns
Of the 3, type matching is probably what you'll see the most of. Does the input match this type? 
```
static string Example(object input)
{
    switch(input)
    {
        case string text:
            return text;
        default:
            throw new ArgumentException("Input is unexpected type.");
    }
}
```

##### Var Patterns
Var patterns are pretty weird. Unlike the other patterns it always matches. This allows you to work with the initial object. For example, say we want a case to handle null values, empty strings and strings which are only white space.

```
static void string Example(string input)
{
    switch(input)
    {
        case "hello":
            return input;
        case var obj when (obj?.Trim().Length ?? 0) == 0:
            return null;
        default:
            return "Invalid input string.";
    }
}
```

#### Ref-related Features
First let's quickly backtrack and look at the usage of ```ref``` prior to this release. Up to this point you could use the ```ref``` keyword before a parameter to pass a reference into a method.

Let's say we want to sort an array.
```
static int[] NormalSort(int[] input)
{
    // Sort input.
    return input;
}

var input = new int[] { 1,5,2,4,3 };
var result = NormalSort(input);
```

Here we create a new variable to store the returned value. This is great if we plan to re-use ```input``` further down our code.

In comparison we can do the same using reference passing.
```
static void RefSort(ref int[] input)
{
    // Sort input.
}

var input = new int[] { 1,5,2,4,3 };
RefSort(ref input);
```

There is no new variable created. The sort method applies it's operations directly to ```input```. If we don't plan on using our ```input``` again, this method is more memory efficient.

If we compare result and input after running RefSort we get the [same result](https://github.com/ashmidgley/csharp-in-depth/blob/master/Tests/C%23-7/RefTests.cs).
```
[ 1, 2, 3, 4, 5]
```

Now that we have an idea of how reference passing works with methods lets take a look at ref local variables and ref returns introduced in C# 7.

##### Local Variable

Say we have an array of tuple elements. We want to change the values for element i. We could do it like this:
```
array[i].min++;
array[i].max--;
```

Or like this:
```
var tuple = array[i];
tuple.min++;
tuple.max--;
array[i] = tuple;
```

But to be honest with you, both of these are pretty slimy. We can do the same using a ref local variable:
```
ref var tuple = ref array[i];
tuple.min++;
tuple.max--;
```

Of the above I think the bottom gives you the most bang for your buck in terms of readability because it clearly shows that the array element being updated is a tuple.

##### Ref Return
Below we see an indexer returning the element of the classes array by reference.
```
class Example
{
    private int[] array = new int[10];

    public ref int this[int index]
    {
        get 
        {
            return ref array[index];
        }
    }
}
```

We return the index as a reference to the original array so that any updates to it will directly reflect on the array.
```
Example example = new Example();
ref int elementOne = ref example[0];
ref int elementOneAswell = ref example[0];

elementOne = 100;
Console.WriteLine(elementOneAswell);

// Output
100
```

You might notice that ref returns require you to throw refs left right and center. Hopefully by the time I get around to using them (2050?) the syntax will have been cleaned up a bit.

These features were introduced to improve memory efficiency. I'll mention that you probably won't come across many ref-related features at the mo unless you're working with a real-time system. Despite this, Jon mentioned that you'll probably reap benefits from them anyway because it means other features in the framework can be re-written to work more efficiently.

#### End

That's a wrap on C# and Fight Club references for now. I've only really scratched the surface of what Jon covers so if you're after more features and much more detail grab a copy [here](https://www.manning.com/books/c-sharp-in-depth-fourth-edition). I haven't had a chance to take a proper look into C# 8 yet but a quick scan of the Microsoft [docs](https://docs.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-8) looks promising. 

Cheers for reading! Now piss off and go write some programs in C#.