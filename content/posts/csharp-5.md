---
title: "C# 5 - the One with Asynchrony"
date: "2019-10-26T00:00:00.000Z"
draft: false
---

C# 5 was released in August 2012 with Visual Studio 2012. This version came with the ```async``` and ```await``` keywords to allow you to write asynchronous code. Let's crack on and take a look at **asynchrony** and a bonus feature called **caller information attributes**.

#### Asynchronous Code

Getting ready for work can be broken down into a set of tasks.

1. Shower.
2. Clothes.
3. Coffee.
4. Teeth.
5. Loo.
6. Out the door.

Once you've done this a few times, you may want to perform these steps asynchronously. You could brush your teeth while you're on the loo or try getting changed while you shower. This is the basic premise behind asynchronous operations. We can reduce time by having multiple things on the go at once.

In terms of code, asynchrony is about starting an operation and then later continuing when the operation has completed without having to block in the middle. We declare a method as asyncronous by using the ```async``` keyword.

```
async void Usage()
{
    ...
}
```

As of C# 5, async methods can only return ```void```, ```Task``` or ```Task<TResult>``` types. In an async method we use the ```await``` keyword to wait for the result of an operation. Generally, you await an operation which returns a ```Task``` or ```Task<T>```. If an async method doesn't contain an ```await``` call, it executes synchronously but the compiler will give you a warning to say you're missing something.

```
static async Task<string> GetContent(string url)
{
    return await httpClient.GetStringAsync(url);
}
```

We can access the value of the returned task using it's ```Result``` property. The below will spit out the HTML for the webpage you are currently on.

```
string html = GetContent("https://ashmidgley.github.io/posts/csharp-5").Result;
Console.WriteLine(html);

// Output
<!DOCTYPE html><html lang="en"><head><meta charSet="utf-8"/>...
```

The best usage of async code I've seen in my work so far was in an email alerts service for a business broker in New Zealand. The company had to send thousands of marketing emails out to users all across the globe. The current system was **iteratively** working through the email list. It was taking around an hour per country. This was problematic as they wanted all the emails fired out before people arrived at work in the morning but each set of jobs could only start once the previous countries emails were sent out. We were asked to fix this so I teamed up with a senior dev and he showed me how to rewrite it to work through the emails in batches **asynchronously**. It reduced the processing to a matter of minutes per country code.

Sadly, asynchrony isn't all sunshine and rainbows. It's very difficult to create tests for and dealing with bugs is beyond grim.

#### Caller Information Attributes

C# 5 introduced 3 new attributes to provide you with the current file, line number and member name. These attributes can only be used with parameters that have default values.

```
static void Usage(
    [CallerFilePath] string file = null,
    [CallerLineNumber] int line = 0,
    [CallerMemberName] string member = null)
{
    Console.WriteLine($"{file}:{line} - {member}"); 
}
```

```
Usage();

// Output
/home/ash/dev/misc/App/Program.cs:9 - Main
```

If for some reason you wanted to override any of these values, you can do so by specifying arguments in the method call.
```
Usage("DummyClass.cs", 500);

// Output
DummyClass.cs:500 - Main
```