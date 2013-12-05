<!--
author: JP Richardson
publish: Fri Feb 18 2011 15:57:37 GMT-0600 (CST)
status: publish
type: post
link: https://procbits.wordpress.com/2011/02/18/streamwriter-share-read-access-in-another-process/
tags: C#
slug: 2011/02/18/streamwriter-share-read-access-in-another-process
title: StreamWriter Share Read Access in Another Process
-->



I have two applications, one is the main application that generates a
log file, the other is an application that reads the log file to compute
statistics.

I want my app that reads the log file to be able to read the file while
the other app is running and generating the log file. If you just use
the default StreamWriter and StreamReader constructors you will get a
file access in error in one of the processes.

Log file generator: (1st process)

```csharp
static void Main(string[] args) {
    var file = @"C:\logfile.txt";
    var sw = new StreamWriter(file);
    sw.AutoFlush = true;
    sw.WriteLine("some data");
    sw.WriteLine("some data2"); //set breakpoint here
    
    sw.Close();
}
```

Log file reader: (2nd process)

```csharp
static void Main(string[] args) {
    var file = @"C:\logfile.txt";
    var sr = new StreamReader(file);

    var l1 = sr.ReadLine();
    var l2 = sr.ReadLine(); //set breakpoint here

    sr.Close();
}
```

Startup the "log file generator" app and then startup the "log file
reader" app, you'll get an IOException. Here's how you can fix this:

Log file generator: (1st process)

```csharp
static void Main(string[] args) {
    var file = @"C:\logfile.txt";
    var fs = File.Open(file, FileMode.Append, FileAccess.Write, FileShare.Read);
    var sw = new StreamWriter(fs);
    sw.AutoFlush = true;
    sw.WriteLine("some data");
    sw.WriteLine("some data2"); //set breakpoint here
    
    sw.Close();
}
```

Log file reader: (2nd process)

```csharp
static void Main(string[] args) {
    var file = @"C:\logfile.txt";
    var fs = File.Open(file, FileMode.Open, FileAccess.Read, FileShare.ReadWrite);
    var sr = new StreamReader(fs);

    var l1 = sr.ReadLine();
    var l2 = sr.ReadLine(); //set breakpoint here

    sr.Close();
}
```

You'll notice that the breakpoint on the 2nd process will now work
without getting an IOException. In short this works because the
[FileShare](http://msdn.microsoft.com/en-us/library/system.io.fileshare.aspx)
permissions are set properly. You can also read more about the [enum
FileMode](http://msdn.microsoft.com/en-us/library/system.io.filemode.aspx).


