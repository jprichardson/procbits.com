<!--
author: JP
publish: Wed Dec 08 2010 20:29:05 GMT-0600 (CST)
status: publish
type: post
link: https://procbits.wordpress.com/2010/12/08/including-more-than-one-resourcedictionary-in-your-xaml/
tags: C#, WPF
slug: 2010/12/08/including-more-than-one-resourcedictionary-in-your-xaml
-->

Including More than One ResourceDictionary in Your Xaml
=======================================================

I have one giant Xaml ResourceDictionary that's becoming unwieldy to
manage. The solution is simple. Use
[MergedDictionaries](http://msdn.microsoft.com/en-us/library/aa350178.aspx).

Snippet:

```xml
<Window.Resources>
    <ResourceDictionary>
        <ResourceDictionary.MergedDictionaries>
            <ResourceDictionary Source="FileResources1.xaml" />
            <ResourceDictionary Source="FileResources2.xaml" />
        </ResourceDictionary.MergedDictionaries>
    </ResourceDictionary>
</Window.Resources>
```

Are you a [Git](http://gitpilot.com) user? Let me help you make project
management with Git simple. Checkout [Gitpilot](http://gitpilot.com).

Follow me on Twitter [@jprichardson](http://twitter.com/jprichardson)
and read my [blog on entrepreneurship](http://techneur.com).

-JP
