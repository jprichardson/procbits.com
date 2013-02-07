<!--
author: JP
publish: Mon Feb 07 2011 19:32:49 GMT-0600 (CST)
status: publish
type: post
link: https://procbits.wordpress.com/2011/02/07/wpf-application-exception-handling/
tags: C#, WPF
slug: 2011/02/07/wpf-application-exception-handling
-->

WPF Application Wide Exception Handling
=======================================

What do you do if you want to catch all unhandled exceptions in an
application?

In App.xaml:

```xml
<Application x:Class="MyApp.App"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    DispatcherUnhandledException="UnhandledException"
>
```

Add "DispatcherUnhandledException" to App.xaml. Then in your App.xaml.cs
file:

```csharp
private void UnhandledException(object sender, DispatcherUnhandledExceptionEventArgs e) {
    var time = DateTime.Now;
    File.AppendAllText(@"C:\exps.csv", string.Format("{0},{1}", time, e.Exception.Message));
    File.AppendAllText(@"C:\sts.txt", time + ":\n" + e.Exception.StackTrace + "\n");
    e.Handled = false; //we want the user to experience the crash
}
```

That's it. So now if the app crashes on your users, you don't have to
ask your users what steps they did to cause the crash. You can just look
at the logs!

Are you a [Git](http://gitpilot.com) user? Let me help you make project
management with Git simple. Checkout [Gitpilot](http://gitpilot.com).

Follow me on Twitter [@jprichardson](http://twitter.com/jprichardson) or
read my [blog on software entrepreneurship](http://techneur.com).

-JP
