<!--
author: JP Richardson
publish: Thu Sep 09 2010 19:57:22 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2010/09/09/three-ways-to-sort-a-list-of-objects-with-datetime-in-c/
tags: C#
slug: 2010/09/09/three-ways-to-sort-a-list-of-objects-with-datetime-in-c
-->

Three Ways to Sort a List of Objects with DateTime in C#
========================================================

Here are three quick ways to sort a List of objects that have DateTime
properties in C\#.

Initial Code:

```csharp
public class Person{
    public string Name;
    public DateTime Birthday;
}

var people = new List<Person>();
var p1 = new Person() { Name = "Leslie", Birthday = new DateTime(1983, 9, 4) };
var p2 = new Person() { Name = "Chris", Birthday = new DateTime(2001, 6, 19) };
var p3 = new Person() { Name = "JP", Birthday = new DateTime(1983, 4, 5) };

people.Add(p1); people.Add(p2); people.Add(p3);
people.ForEach(p => Console.WriteLine(p.Name));
```

You can see that the output is: "Leslie", "Chris", and then "JP"

Let's start with the LINQ way:

```csharp
var persons = from p in people
            orderby p.Birthday
            select p;
persons.ToList().ForEach(p => Console.WriteLine(p.Name));
```

You can see the correct output: "JP", "Leslie", and then "Chris"

Old fashion way using a Delegate:

```csharp
people.Sort(delegate(Person ps1, Person ps2) { return DateTime.Compare(ps1.Birthday, ps2.Birthday); });
people.ForEach(p => Console.WriteLine(p.Name));
```

Cleaner way using a Lambda:

```csharp
people.Sort((ps1, ps2) => DateTime.Compare(ps1.Birthday, ps2.Birthday));
people.ForEach(p => Console.WriteLine(p.Name));
```

Are you a [Git](http://gitpilot.com) user? Let me help you make project
management with Git simple. Checkout [Gitpilot](http://gitpilot.com).

Read me blog on entrepreneurship: [Techneur](http://techneur.com) Follow
me on Twitter: [@jprichardson](http://twitter.com/jprichardson)

-JP
