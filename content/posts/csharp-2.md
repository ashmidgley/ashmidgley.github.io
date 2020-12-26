---
title: "C# 2 - the One with Generics"
date: "2019-10-12T00:00:00.000Z"
draft: false
---

C# 2 was released in November 2005 with Visual Studio 2005. As mentioned previously, I'll just be covering the most useable features so we'll take a look at **generics**, **nullable value types** and **iterators**.

#### Generics

Prior to C# 2, if we wanted to store values in a dynamic list we had a choice between ```ArrayList``` which holds a collection of objects and ```StringCollection``` which holds a collection of strings.
```
ArrayList list = new ArrayList();
list.Add(5);
list.Add(true);
list.Add("Test");

StringCollection names = new StringCollection();
names.Add("Tyler Durden");
names.Add("Marla Singer");
names.Add("Robert Paulson");
```

If we wanted to work with the values in our ArrayList, we'd have to do some fiddly type checking or casting. Wouldn't it be easier if we had a collection were we could specify a single type of value being added?

Enter generics:

```
List<int> numbers = new List<int>();
numbers.Add(1);
numbers.Add(2);
numbers.Add(3);

List<string> dogma = new List<string>();
dogma.Add("The lower you fall, the higher you'll fly.");
dogma.Add("The things you used to own, now they own you.");
dogma.Add("This is your life and its ending one moment at a time.");
```

You specify the type stored in the collection ```List<*insert type here*>``` and the compiler will make sure only values of that type are stored. I thank the C# gods every night for introducing generics and saving us from the casting nightmare that were ArrayLists.

#### Nullable value types

Say you're setting up a website to sell premium hand-made soap. You want a field for Company details but in some cases the person buying wants it for personal use so they aren't ordering on behalf of a Company. This means in some cases CompanyId will have a value, and in other cases it will be null.

Enter nullable value types:

```
public struct Order
{
    public int CustomerId;
    public int SoapId;
    public int? CompanyId;
}
```

Here we are using the ? operator after the value type to specify this field as nullable. This can also be represented as:

```
public Nullable<int> CompanyId
```

You can use the HasValue property to check if a value is present or access the value using the Value property.
```
if(order.CompanyId.HasValue) 
{
    Console.WriteLine(order.CompanyId.Value);
} 
```

##### Null-coalescing Operator

With the introduction of nullable values types came the ?? operator.

```
int? nullableOne;
int? nullableTwo = 2;
int value = 1000;

Console.WriteLine(nullableOne ?? value);
Console.WriteLine(nullableOne ?? nullableTwo ?? value);

// Output
1000
2
```

If the value prior to the operator has a value then it will be returned. If not, the preceding value will be returned. As seen in the second example, you can chain the operator with multiple values.

#### Iterators

Iterators do what they say on the packaging. They iterate through a collection of values. An iterator method uses ```yield return``` to return each element in the sequence **one at a time**. Iterator methods can only return ```IEnumerable```, `IEnumerable<T>`, ```IEnumerator``` or `IEnumerator<T>` types.

You consume an iterator using a foreach statement. The C# 3 release allows us to consume iterators using LINQ queries aswell.

```
public static IEnumerable<int> SimpleIterator()
{
    for(int i=1; i <= 5; i++)
    {
        yield return i;
    }
}

public static void PrintSimpleIterator()
{
    foreach(int value in SimpleIterator())
    {
        Console.WriteLine(value);
    }
}

// Output from call to PrintSimpleIterator()
1
2
3
4
5
```

Bit boring. Let's look at something more interesting. The below is Jon's implementation of returning a sequence of [fibonacci numbers](https://en.wikipedia.org/wiki/Fibonacci_number).

```
public static IEnumerable<int> Fibonacci()
{
    int current = 0;
    int next = 1;
    while(true)
    {
        yield return current;
        int oldCurrent = current;
        current = next;
        next = oldCurrent += next;
    }
}

public static void PrintFibonnaciUpToValue(int candidate)
{
    foreach(int value in Fibonacci()) 
    {
        if(value > candidate) 
        {
            break;
        }
        Console.WriteLine(value);
    }
}

// Output from call to PrintFibonnaciUpToValue(1000);
0
1
1
2
3
5
8
13
21
34
55
89
144
233
377
610
987
```

You could implement this by storing the values and then returning them in a list but this could become problematic as the value gets bigger.

To test this I implemented another Fibonacci method that stored the results in a list. Any input over 10 billion results in an OutOfMemoryException whereas the iterator method chews up to 10 quintillion like it's nothing. You can check out my test code [here](https://github.com/ashmidgley/csharp-in-depth/blob/master/Tests/C%23-2/IteratorsTests.cs).