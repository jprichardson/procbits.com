<!--
author: JP Richardson
publish: Thu Oct 28 2010 21:33:52 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2010/10/28/silverlight-4-and-comactivex-integration/
tags: C#, Silverlight
slug: 2010/10/28/silverlight-4-and-comactivex-integration
title: Silverlight 4 and COM/ActiveX Integration
-->



Silverlight 4 supports COM/ActiveX controls with Trusted permissions.
However you must configure your app to be an
[Out-of-Browser](http://msdn.microsoft.com/en-us/library/dd550721(VS.95).aspx)
app with [Trusted
permissions](http://msdn.microsoft.com/en-us/library/ee721083(v=VS.95).aspx).

Here is the snippet of code that will open Excel and place a value in
the first cell:

```csharp
dynamic ex = AutomationFactory.CreateObject("Excel.Application");
ex.Visible = true;
ex.workbooks.Add();

dynamic sheet = ex.ActiveSheet;
sheet.Cells[1, 1].Value = "hello!";
```

However this won't compile out of the box. First make sure that your app
is enabled to run as an Out-of-Browser app. Open up your project
properties and on the "Silverlight" tab make sure that "Enable running
application out of the browser" and then click the button
"Out-of-Browser Settings..." and at the bottom check "Require elevated
trust when running outside the browser."

If you've read other tutorials, you'll see that a lot of them are using
ComAutomationFactory. This is because these tutorials were written with
a beta release of Silverlight 4. That's why you're getting this error:
` The name 'ComAutomationFactory' does not exist in the current context`

Change "ComAutomationFactory" to "AutomationFactory" and you'll still
notice that it won't compile. Now you're probably getting this error:
` The name 'AutomationFactory' does not exist in the current context`

ComAutomationFactory was previously in the namespace
System.Windows.Interop. AutomationFactory has been moved to
System.Runtime.InteropServices.Automation.

Now you might be getting the following error:
` One or more types required to compile a dynamic expression cannot be found. Are you missing references to Microsoft.CSharp.dll and System.Core.dll`

All you need to do is add a reference to "Microsoft.CSharp.dll." You can
do this by right-clicking "References" in the "Solution Explorer" and
then navigating to the ".NET" tab and selecting it.

Are you a [Git](http://gitpilot.com) user? Let me help you make project
management with Git simple. Checkout [Gitpilot](http://gitpilot.com).

Follow me on Twitter: [@jprichardson](http://twitter.com/jprichardson)
or read my blog on entrepreneurship: [Techneur](http://techneur.com).

-JP
