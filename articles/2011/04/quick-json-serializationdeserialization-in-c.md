<!--
author: JP
publish: Thu Apr 21 2011 18:38:48 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2011/04/21/quick-json-serializationdeserialization-in-c/
tags: C#
slug: 2011/04/21/quick-json-serializationdeserialization-in-c
-->

Quick JSON Serialization/Deserialization in C#
==============================================

**\*This outdated\*.** You should use [FridayThe13th the best JSON
parser for Silverlight and .NET
4.0](http://procbits.com/2011/08/11/fridaythe13th-the-best-json-parser-for-silverlight-and-net/).

You don't need to download an [additional
library](http://json.codeplex.com/)to serialize/deserialize your objects
to/from JSON. Since .NET 3.5, .NET can do it natively.

Add a reference to your project to "System.Web.Extensions.dll"

Let's look at this example JSON string:

```javascript
{
    "some_number": 108.541, 
    "date_time": "2011-04-13T15:34:09Z", 
    "serial_number": "SN1234"
}
```

You can deserialize the previous JSON into a dictionary like so:

```csharp
using System.Web.Script.Serialization;

var jss = new JavaScriptSerializer();
var dict = jss.Deserialize<Dictionary<string,string>>(jsonText);

Console.WriteLine(dict["some_number"]); //outputs 108.541
```

So what if your JSON is a bit more complex?



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

Deserialize like so...

```csharp
using System.Web.Script.Serialization;

var jss = new JavaScriptSerializer();
var dict = jss.Deserialize<Dictionary<string,dynamic>>(jsonText);

Console.WriteLine(dict["some_number"]); //outputs 108.541
Console.WriteLine(dict["more_data"]["field2"]); //outputs hello
```

The field "more\_data" gets deserialized into a Dictionary\<string,
object\>.

You can actually just just deserialize like so:

```csharp
using System.Web.Script.Serialization;

var jss = new JavaScriptSerializer();
var dict = jss.Deserialize<dynamic>(jsonText);

Console.WriteLine(dict["some_number"]); //outputs 108.541
Console.WriteLine(dict["more_data"]["field2"]); //outputs hello
```

And everything still works the same. The only caveat is that you lose
intellisense by using the "dynamic" data type.

Serialization is just as easy:

```csharp
using System.Web.Script.Serialization;

var jss = new JavaScriptSerializer();
var dict = jss.Deserialize<dynamic>(jsonText);

var json = jss.Serialize(dict);
Console.WriteLine(json);
```

Outputs... [sourcecode language="javascript"] { "some\_number": 108.541,
"date\_time": "2011-04-13T15:34:09Z", "serial\_number": "SN1234"
```

Do you use Git? If so, checkout [Gitpilot](http://gitpilot.com) to make
project management and collaborating on projects seamless.

If you made it this far, read my blog on [software
entrepreneurship](http://techneur.com) and follow me on Twitter:
[@jprichardson](http://twitter.com/jprichardson).

-JP
