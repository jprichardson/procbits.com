<!--
author: JP Richardson
publish: Thu Aug 11 2011 19:01:33 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2011/08/11/fridaythe13th-the-best-json-parser-for-silverlight-and-net/
tags: C#, Silverlight, WPF
slug: 2011/08/11/fridaythe13th-the-best-json-parser-for-silverlight-and-net
title: FridayThe13th the Best JSON Parser for Silverlight and C#/.NET
-->



Up until a couple of months ago I was writing most of my code using WPF.
Recently, a project came up where Silverlight made more sense to use.
I'd thought that wouldn't be a problem since I'd just use
[JavaScriptSerializer](http://msdn.microsoft.com/en-us/library/system.web.script.serialization.javascriptserializer.aspx)
[wrote about it
[here](http://procbits.com/2011/04/21/quick-json-serializationdeserialization-in-c/ "here")]
like I did for my WPF project.

Uh oh. It turns out that Silverlight doesn't have JavaScriptSerializer.
Never fear!
[DataContractJsonSerializer](http://msdn.microsoft.com/en-us/library/system.runtime.serialization.json.datacontractjsonserializer(v=VS.95).aspx)
is here! Or so I thought.

It turns out that if you want to use DataContractJsonSerializer you must
actually create POCOs to backup this "data contract." I didn't want to
do that.

I wanted to turn this...

```javascript
{
    "some_number": 108.541,
    "date_time": "2011-04-13T15:34:09Z",
    "serial_number": "SN1234"
    "more_data": {
        "field1": 1.0
        "field2": "hello"
    }
}
```

into..

```csharp
using System.Web.Script.Serialization;

var jss = new JavaScriptSerializer();
var dict = jss.Deserialize<dynamic>(jsonText);

Console.WriteLine(dict["some_number"]); //outputs 108.541
Console.WriteLine(dict["more_data"]["field2"]); //outputs hello
```

So I set out to write my own JSON parser. I call it FridayThe13th... how
fitting huh? Now, using either Silverlight or .NET 4.0, you can parse
the previous JSON into the following:

```csharp
using FridayThe13th;

var jsonText = File.ReadAllText("mydata.json");

var jsp = new JsonParser(){CamelizeProperties = true};
dynamic json = jsp.Parse(jsonText);

Console.WriteLine(json.SomeNumber); //outputs 108.541
Console.WriteLine(json.MoreData.Field2); //outputs hello
```

Since I work with a lot of Ruby on Rails backends, I want to add a
property "CamelizeProperties" to turn "some\_number" into
"SomeNumber"... it's more .NET like.

Try it! You can find it on
[Github](https://github.com/jprichardson/FridayThe13th). Oh yeah... it's
also faster than that [other .NET JSON
library](http://json.codeplex.com/) that everyone uses.

