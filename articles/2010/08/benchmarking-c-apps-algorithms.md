<!--
author: JP Richardson
publish: Wed Aug 25 2010 16:38:39 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2010/08/25/benchmarking-c-apps-algorithms/
tags: C#
slug: 2010/08/25/benchmarking-c-apps-algorithms
title: Benchmarking C# Apps & Algorithms
-->



I created a class to help me benchmark my C\# algorithms.

Here's how you can use it:

```csharp
BenchmarkTimer.Start("Long running algorithm");
for (int x = 0; x < 3; ++x) {
    BenchmarkTimer.Start("Small running algorithm");
    Thread.Sleep(1000);
    BenchmarkTimer.StopAndOutput();
}
BenchmarkTimer.StopAndOutput();
```

Output: Small running algorithm: 1000.3712 ms Small running algorithm:
1000.3712 ms Small running algorithm: 1000.3712 ms Long running
algorithm: 3001.1136 ms

You can find my entire C\# CommonLib library here:
[http://github.com/jprichardson/CommonLib](http://github.com/jprichardson/CommonLib)

Here is the relevant class:

```csharp
public static class BenchmarkTimer
    {
        private static Stack<BenchmarkData> _startStack = new Stack<BenchmarkData>();

        public static void Start() {
            _startStack.Push(new BenchmarkData());
        }

        public static void Start(string label) {
            var bd = new BenchmarkData() { Label = label };
            _startStack.Push(bd);
        }

        public static TimeSpan Stop() {
            var stop = DateTime.Now;
            var startBD = _startStack.Pop();
            return stop - startBD.DateTime;
        }

        public static void StopAndOutput() {
            var stop = DateTime.Now;
            var startBD = _startStack.Pop();

            var delta = stop - startBD.DateTime;

            var lbl = "{0}: {1} ms";
            Console.WriteLine(String.Format(lbl, startBD.Label, delta.TotalMilliseconds));
        }

        private class BenchmarkData
        {
            public DateTime DateTime { get; set; }
            public string Label { get; set; }

            public BenchmarkData(){
                this.DateTime = DateTime.Now;
                this.Label = "";
            }
        }
    }
```
Enjoy.