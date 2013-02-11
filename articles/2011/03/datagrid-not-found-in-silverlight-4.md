<!--
author: JP Richardson
publish: Thu Mar 03 2011 16:56:20 GMT-0600 (CST)
status: publish
type: post
link: https://procbits.wordpress.com/2011/03/03/datagrid-not-found-in-silverlight-4/
tags: Silverlight
slug: 2011/03/03/datagrid-not-found-in-silverlight-4
-->

DataGrid Not Found in Silverlight 4
===================================

I created a fresh new Silverlight 4 project. I opened up MainPage.xaml
and started typing "DataGrid"... but intellisense didn't show it.

I found out that I needed to add the following to the top of my Xaml
file:
` xmlns:sdk="http://schemas.microsoft.com/winfx/2006/xaml/presentation/sdk"`

In then started showing up in intellisense. I tried to compile the
simple app and I got an error out of the MainPage.g.i.cs file. The error
is:

> "The type or namespace name 'DataGrid' could not be found (are you
> missing a using directive or an assembly reference?)"

I opened up the [Silverlight MSDN for the DataGrid
control](http://msdn.microsoft.com/en-us/library/system.windows.controls.datagrid(v=vs.95).aspx)
and found out that my DataGrid is in the System.Windows.Controls
namespace and the System.Windows.Controls.Data assembly. I didn't see it
in my references tree, so I tried to add a .NET reference.
Unfortunately, "System.Windows.Controls" or
"System.Windows.Controls.Data" weren't in the list.

It turns out that you need to navigate to the "Browse" tab in the "Add
Reference Dialog" and then add the file "System.Windows.Controls.Data"
which is typically found in "C:\\Program Files\\Microsoft
SDKs\\Silverlight\\v4.0\\Libraries\\Client"

Hope this helps someone.

Are you a [Git](http://gitpilot.com) user? Let me help you make project
management with Git simple. Checkout [Gitpilot](http://gitpilot.com).

Follow me on Twitter: [@jprichardson](http://twitter.com/jprichardson)
or read my blog on entrepreneurship: [Techneur](http://techneur.com).

-JP
