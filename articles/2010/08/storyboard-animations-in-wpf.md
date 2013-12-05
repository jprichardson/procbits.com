<!--
author: JP Richardson
publish: Tue Aug 10 2010 22:18:11 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2010/08/10/storyboard-animations-in-wpf/
tags: WPF
slug: 2010/08/10/storyboard-animations-in-wpf
title: StoryBoard: Animations in WPF
-->



I recently discovered how to do animations in WPF. It's super simple! So
what if you want to create a mouse over animation on a StackPanel? You
use the StoryBoard XAML element. Snippet:

```xml
 <Storyboard x:Key="mouseFadeIn">
    <DoubleAnimation  
    Duration="0:0:5"  
    Storyboard.TargetName="myStackPanel"  
    Storyboard.TargetProperty="(UIElement.Opacity)"
    From="0.25"
    To="1.0"/>
</StoryBoard>
```

This will cause a the Opacity of the element to change from 0.25 to 1.0
over a period of 5 seconds. Note: this should be put in the "Resources"
tag. So, if it's a Window it would be "Window.Resources" or if it's a
UserControl it would be "UserControl.Resources"... Snippet:

```xml
<UserControl.Resources>
  <StoryBoard key="blah">...
```

So how do we get this to trigger on a mouse over? By using Triggers of
course! Snippet:

```xml
<UserControl.Triggers>
    <EventTrigger RoutedEvent="Mouse.MouseEnter" SourceName="myStackPanel">
        <BeginStoryboard Storyboard="{StaticResource mouseFadeIn}" />
    </EventTrigger>
</UserControl.Triggers>
```

If you want to create a Fade Out effect, just use the "Mouse.MouseLeave"
event and reverse the animation. You can even have multiple animations
in a StoryBoard. They can run simultaneously or sequentially. If you
have multiple animations in a StoryBoard and you want them to run
sequentially, you need to add a "BeginTime" attribute. This ensures that
the animation is executed after the previous animation(s).


