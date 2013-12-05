<!--
author: JP Richardson
publish: Tue Jul 19 2011 19:05:21 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2011/07/19/missing-dockpanel-add-dockpanel-for-silverlight-4-or-silverlight-5/
tags: C#, Silverlight
slug: 2011/07/19/missing-dockpanel-add-dockpanel-for-silverlight-4-or-silverlight-5
title: Missing DockPanel? Add DockPanel for Silverlight 4 or Silverlight 5
-->



When I first created a demo project for Silverlight 5, I opened the XAML
code to start editing. I started typing "DockPanel" but then I noticed
that intellisense didn't show anything. It turns out that DockPanel and
some other controls aren't included in the default installation of
Silverlight 4 or Silverlight 5 beta.

Here's what you need to do:

Download the [Silverlight 4
Toolkit](http://silverlight.codeplex.com/releases/view/43528). Install
it. (Yes, this will work with Silverlight 5 beta)

Add a reference to "System.Windows.Controls.Toolkit". In Silverlight 5,
you will need to navigate to the file: %ProgramFiles%\\Microsoft
SDKs\\Silverlight\\v4.0\\Toolkit\\Apr10\\Bin\\System.Windows.Controls.Toolkit.dll

Add the following attribute to your UserControl:
xmlns:tk="clr-namespace:System.Windows.Controls;assembly=System.Windows.Controls.Toolkit"

So your code might look like:

```xml
<UserControl x:Class="Project1.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    xmlns:tk="clr-namespace:System.Windows.Controls;assembly=System.Windows.Controls.Toolkit"
    mc:Ignorable="d"
    d:DesignHeight="300" d:DesignWidth="400">

    <Grid x:Name="LayoutRoot" Background="White">
        <tk:DockPanel>
            
        </tk:DockPanel>
    </Grid>
    
</UserControl>
```

Enjoy.

