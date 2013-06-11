<!--
author: JP Richardson
publish: Fri Sep 10 2010 18:13:41 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2010/09/10/linkedlist-always-slower-than-a-list-in-c/
tags: C#
slug: 2010/09/10/linkedlist-always-slower-than-a-list-in-c
title: LinkedList Always Slower than a List in C#?
-->



I started using some LinkedList's instead of Lists in some of my C\#
algorithms hoping to speed them up. However, I noticed that they just
felt slower. Like any good developer, I figured that I should do due
diligence and verify my feelings. So I decided to benchmark some simple
loops. You can find the benchmark class
[here](http://procbits.com/2010/08/25/benchmarking-c-apps-algorithms/).

I thought that populating the collections with some random integers
should be sufficient. I ran this code in Debug mode to avoid any
compiler optimizations. Here is the code that I used:

```csharp
var rand = new Random(Environment.TickCount);
var ll = new LinkedList<int>();
var list = new List<int>();
int count = 20000000;

BenchmarkTimer.Start("Linked List Insert");
for (int x = 0; x < count; ++x)
    ll.AddFirst(rand.Next(int.MaxValue));
BenchmarkTimer.StopAndOutput();

BenchmarkTimer.Start("List Insert");
for (int x = 0; x < count; ++x)
    list.Add(rand.Next(int.MaxValue));
BenchmarkTimer.StopAndOutput();

int y = 0;
BenchmarkTimer.Start("Linked List Iterate");
foreach (var i in ll)
    ++y; //some atomic operation;
BenchmarkTimer.StopAndOutput();

int z = 0;
BenchmarkTimer.Start("List Iterate");
foreach (var i in list)
    ++z; //some atomic operation;
BenchmarkTimer.StopAndOutput();
```

Here is the output:

> Linked List Insert: 8959.808 ms List Insert: 845.856 ms Linked List
> Iterate: 203.632 ms List Iterate: 125.312 ms

This result baffled me. A Linked List insert should be O(1) whereas as
List Insert is Θ(1), O(n) (because of copy) if it needs to be resized.
Both list iterations should be O(1) because of the enumerator. I looked
at the disassembled output and it doesn't shed much light on the
situation.

Anyone else have any thoughts on why this is? Did I miss something
glaringly obvious?

Are you a [Git](http://gitpilot.com) user? Let me help you make project
management with Git simple. Checkout [Gitpilot](http://gitpilot.com).

Read me blog on entrepreneurship: [Techneur](http://techneur.com) Follow
me on Twitter: [@jprichardson](http://twitter.com/jprichardson)

-JP
