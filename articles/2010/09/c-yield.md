<!--
author: JP Richardson
publish: Fri Sep 03 2010 04:14:38 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2010/09/02/c-yield/
tags: C#
slug: 2010/09/02/c-yield
-->

C# yield
========

I've seen the "yield" keyword here and there... but I've never really
cared to learn what it does. Until now. Actually, it's my understanding
that it's more or less a convenience keyword like "var."

In short, "yield" allows you to return an IEnumerable without creating a
List.

Example: (old way)

```csharp
IEnumerable<Car> FindBlueCars(){
    var blueCars = new List<Car>();
    foreach (var c in _allCars)
        if (c.IsBlue())
            blueCars.Add(c);
    return blueCars;
}
```

Example: (new way)

```csharp
IEnumerable<Car> FindBlueCars(){
    foreach (var c in _allCars)
        if (c.IsBlue())
            yield return c;
}
```

Saves some typing. [MSDN reference for
yield.](http://msdn.microsoft.com/en-us/library/9k7k7cf0(v=VS.100).aspx)

Are you a [Git](http://gitpilot.com) user? Let me help you make project
management with Git simple. Checkout [Gitpilot](http://gitpilot.com).

Read me blog on entrepreneurship: [Techneur](http://techneur.com) Follow
me on Twitter: [@jprichardson](http://twitter.com/jprichardson)

-JP
