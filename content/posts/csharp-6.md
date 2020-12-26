---
title: "C# 6 - the One with Interpolated Strings"
date: "2019-10-29T00:00:00.000Z"
draft: false
---

C# 6 was released in July 2015 with Visual Studio 2015. It doesn't have one big game-changing feature like 3 with LINQ or 5 with async but it does have a range of features that polish up the dev process. I could have easily done 5 or 6 here but I need to finish season 4 of Archer so we'll just cover **interpolated strings**, **static directives** and **exception filters**.

#### Interpolated Strings

Interpolated strings are dope. They make it way easier to build strings. Before them we had to use ```string.Format```.
```
string project = "Mayhem";
string rule = "you do not ask questions";

string result = string.Format("The first rule of Project {0} is {1}.", project, rule);
Console.WriteLine(result);

// Output
The first rule of Project Mayhem is you do not ask questions.
```

***barf***

Now we just add a $ symbol before our quotation marks and wrap our values in brackets.
``` 
string result = $"The first rule of Project {project} is {rule}.";
Console.WriteLine(result);

// Output
The first rule of Project Mayhem is you do not ask questions.
```

##### Verbatim Strings

When the compiler sees a \ character in a string, it expects it to be followed by an [escape sequence](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/strings/#string-escape-sequences). In some cases, I dont want to use the \ character as part of an escape sequence. I want to use the string as I see it. This is what is meant by verbatim string. 

To do this we just plonk an @ after our $ symbol. Without the @ the below won't compile.

```
var file = "Test.cs";
Console.WriteLine($@"c:\users\ash\{file}");

// Output
c:\users\ash\Test.cs
```

#### Static Directives
If you keep referring to the same class and it's bloating your code, you can declare a static directive and refer to it's methods without specifying the class name over and over. We do this be introducing ```using static <classname>``` at the top of the file. 

Before:
```
var answer = Math.Sqrt(144) + Math.Round(255.546, 2) - Math.PI * Math.Ceiling(4.7);
```

After:

```
using static System.Math;

var answer = Sqrt(144) + Round(255.546, 2) - PI * Ceiling(4.7);
```

#### Exception Filters

Exception filters gives us more control when handling specific exception cases. We apply the filtering using the ```when``` keyword after the catch statement.

```
string[] messages = 
{
    "This won't be caught.",
    "Honey came in and she caught me red-handed creeping with the girl next door.",
    "This won't be caught either."
};

foreach(string message in messages)
{
    try 
    {
        throw new Exception(message);
    }
    catch(Exception e) 
        when (e.Message.Contains("creeping with the girl next door"))
    {
        Console.WriteLine("It wasn't me.");
    }
}
```

To create a default case we just add another catch block of the same type without a ```when``` clause.
```
catch(Exception e) 
    when (e.Message.Contains("creeping with the girl next door"))
{
    // Handles specific case.
}
catch(Exception e)
{
    // Handles all other cases of type Exception.
}
```