<!--
author: JP Richardson
publish: Fri Jan 15 2010 21:59:56 GMT-0600 (CST)
status: publish
type: post
link: https://procbits.wordpress.com/2010/01/15/handy-c-xml-serialization-methods/
tags: C#
slug: 2010/01/15/handy-c-xml-serialization-methods
title: Handy C# XML Serialization Methods
-->



I have been hacking away on WPF/MVVM, GWT/MVP, and Rails for the last
six months and haven't found much time to update. But I wrote some handy
C\# XML serialization methods. They are very straightforward.

Deserialize:

```csharp
public static T ReadFromFile(string file) {
    XmlSerializer xs = new XmlSerializer(typeof(T));
    StreamReader sr = new StreamReader(file);
    T ret = (T)xs.Deserialize(sr);
    sr.Close();
    return ret;
}
```

Serialize:

```csharp
public static void WriteToFile(T obj, string file) {
    XmlSerializer xs = new XmlSerializer(typeof(T));
    StreamWriter sw = new StreamWriter(file);
    xs.Serialize(sw, obj);
    sw.Close();
}
```

Enjoy.

Are you a [Git](http://gitpilot.com) user? Let me help you make project
management with Git simple. Checkout [Gitpilot](http://gitpilot.com).
