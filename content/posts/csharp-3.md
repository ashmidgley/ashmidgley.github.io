---
title: "C# 3 - the One with LINQ"
date: "2019-10-19T00:00:00.000Z"
draft: false
---

C# 3 was released in November 2007 with Visual Studio 2008. Pretty much all the features introduced build into LINQ. I can't cover them all so I'll just look to cover **implicit typing**, **object and collection initializers**, **anonymous types** and finish with a quick look at usage of **LINQ**.

#### Implicit Typing

Say we have a big ol' Dictionary initialization:
```
Dictionary<int, List<string>> mapping = new Dictionary<int, List<string>>();
```

You may be thinking to yourself "geez that takes up a load of room". I agree. Implicit typing allows us to use the ```var``` keyword to infer the type from the right hand side of the statement.
```
var mapping = new Dictionary<int, List<string>>();
```

You can also apply this sort of typing to arrays. The below statements are equivalent.
```
int[] array = new int[] { 1, 2, 3 };
var array = new[] { 1, 2, 3 };
```

#### Object and Collection Initializers

##### Collection

Collection's previously had to be initializing before we could manually add each value one by one:
```
var members = new List<string>();
members.Add("Space Monkey #1");
members.Add("Space Monkey #2");
members.Add("Space Monkey #3");
```

Collection initializers allow us to do the same in a single statement:
```
var members = new List<string> { "Space Monkey #1, "Space Monkey #2", "Space Monkey #3" };
```

##### Object

Say we have an object type Fight:

```
public struct Fight {
    public int Id;
    public string MemberOne;
    public string MemberTwo;
    public List<string> Rules;
}
```

Previously we would have to initialize the object like this:
```
Fight fight = new Fight();
fight.Id = 1;
fight.MemberOne = "Big Bob";
fight.MemberOne = "Angel Face";
List<string> rules = new List<string>();
rules.Add("The first rule about...");
fight.Rules = rules;
```

Proper tedious. Now we can just do this:
```
Fight fight = new Fight 
{
    Id = 1,
    MemberOne = "Big Bob",
    MemberTwo = "Angel Face",
    Rules = new List<string> { "The first rule about..." }
};
```

Pretty simple change but has a huge impact on readability. 

#### Anonymous Types

Sometimes when creating local variables, we can't be bothered specifying the value types. Anonymous types let the compiler do the dirty work for you.
```
var player = new
{
    Username = "Zero Cool",
    Score = 420
};

Console.WriteLine(player.Username.Substring(0, 4));
Console.WriteLine(player.Score + 5);

// Output
Zero
425
```

The compiler recognises that Username is type string so we can use the Substring method on it. It recognises Score is type int so it allows us to add a value to it. Pretty neat huh.

Mr. Compiler can also infer variable names in anonymous types. This is called projection initialization.
```
var flattenedItem = new
{
    player.Username, // haven't specified the variable name eg: Username = 
    player.Score
};

Console.WriteLine($"Flattened item: Username = {flattenedItem.Username}, Score = {flattenedItem.Score}.");

// Output
Flattened item: Username = Zero Cool, Score = 420.
```

#### LINQ

LINQ stands for Language Integrated Query. It allows us to query against strongly typed collections of objects using language keywords. There are 2 types of syntax for LINQ queries: lambda and query.

```
string[] words = { "His", "name", "was", "Robert", "Paulson" };

// Lambda syntax
var shortWords = words
    .Where(word => word.length < 4);

// Query syntax
var shortWords =
    from word in words
    where word.length < 4;
```

I personally prefer the lambda syntax but I'm sure there are plenty out there who prefer the structure of query syntax. LINQ is arguably the most important feature introduced into C#.

##### Useful Keywords

**```Where```** filters elements in the collection based on boolean expressions. The below only returns elements that are less than 4 characters in length.
```
var query = words
    .Where(word => word.Length < 4);

// Result
[ 'His', 'was' ]
```

**```Select```** specifies the type and shape of the returned elements. The below returns the elements in upper case format.
```
var query = words
    .Select(word => word.ToUpper());

// Result
[ 'HIS', 'NAME', 'WAS', 'ROBERT', 'PAULSON' ]
```

**```OrderBy```** sorts the results in order. By default it will order in ascending order as seen below.
```
var ascending = words
    .OrderBy(word => word);

// Result
[ 'His', 'name', 'Paulson', 'Robert', 'was' ]
```

You can specify descending order by using ```OrderByDescending```.
```
var descending = words
    .OrderByDescending(word => word);

// Result
[ 'was', 'Robert', 'Paulson', 'name', 'His' ]
```

You can also order on a custom option by adjusting the element in the expression. Below we are ordering on the first character in each word.
```
var customOrder = words
    .OrderBy(word => word[0]);

// Result
[ 'His', 'Paulson', 'Robert', 'name', 'was' ]
```

If you're interested in checking out the rest have a gander [here](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/query-keywords).