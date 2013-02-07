<!--
author: JP
publish: Mon Oct 11 2010 18:09:24 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2010/10/11/using-linq-with-csv-files/
tags: C#
slug: 2010/10/11/using-linq-with-csv-files
-->

Using LINQ with CSV Files
=========================

In my [last
post](http://procbits.com/2010/10/07/convert-a-csv-to-xml-using-c/), I
wrote how you can convert a CSV to XML. I stated that the reason that
you would do this is so that you can use LINQ on CSV files.

Using my CsvToXml class you can easily use LINQ on CSV. You can see the
[source for
CsvToXml](http://github.com/jprichardson/CommonLib/blob/master/CommonLib/Data/Csv/CsvToXml.cs)
in my C\# CommonLib library.

Let's assume your CSV file looks like the following:
` 'Product','Price','DateStocked' 'Pepsi','4.50','2010-05-04' 'Coke','3.00','2010-09-22' 'Cheetos','7.25','2009-01-13'`

You can then convert like so:

```csharp
var csv2xml = new CsvToXml("mycsvfile.csv", true);
csv2xml.TextQualifier = '\'';
csv2xml.Convert();
```

You can then do cool LINQ queries using the "Records" property of the
CsvToXml class:

```csharp
var records = from rec in csv2xml.Records
            where (decimal)rec.Element("Price") > 3.5m
            orderby (string)rec.Element("Product")
            select rec;
```

or use LINQ like...

```csharp
var records = from rec in csv2xml.Records
            where Convert.ToDateTime((string)rec.Element("DateStocked")) > new DateTime(2010, 9, 1)
            orderby (string)rec.Element("Product")
            select rec;
```

Then to access the data of the returned "records" object you can do
this:

```csharp
var myrec = records.ElementAt(0);
var firstProduct = (string)myrec.Element("Product");
```

Cool, huh? But we can do better. It's annoying to cast your objects all
of the time and to call Element("CSV\_COLUMN\_NAME"). Why not take
advantage of .NET 4.0 and use its 'dynamic' data type? Admittedly, I got
this idea from this article: [Creating Wrappers with
DynamicObject](http://blogs.msdn.com/b/csharpfaq/archive/2009/10/19/dynamic-in-c-4-0-creating-wrappers-with-dynamicobject.aspx).

Now we can use LINQ like this:

```csharp
var records = from rec in csv2xml.DynamicRecords
            where (decimal)rec.Price > 3.5m
            orderby (string)rec.Product
            select rec;
            
//OR

records = from rec in csv2xml.DynamicRecords
        where Convert.ToDateTime((string)rec.DateStocked) > new DateTime(2010, 9, 1)
        orderby (string)rec.Product
        select rec;
```

A bit better. Now you don't have to call "Element" method everytime, you
still have to annoyingly cast. But, in each CSV column, we already know
that the types should be consistent. Let's define our types up front:

```csharp
var csv2xml = new CsvToXml("mycsvfile.csv", true);
csv2xml.TextQualifier = '\'';

csv2xml.ColumnTypes.Add("Product", typeof(string));
csv2xml.ColumnTypes.Add("Price", typeof(decimal));
csv2xml.ColumnTypes.Add("DateStocked", typeof(DateTime));
            
csv2xml.Convert();
```

Now we can use LINQ like this:

```csharp
records = from rec in csv2xml.DynamicRecords
            where rec.Price > 3.5m
            orderby rec.Product
            select rec;

//OR
records = from rec in csv2xml.DynamicRecords
            where rec.DateStocked > new DateTime(2010, 9, 1)
            orderby rec.Product
            select rec;
```

This is truly clean code. All you need to do is define your types for
each column and you're set. At this time, the major disadvantage is that
you can't lazily evaluate your CSV. You must read it entirely into
memory. This class could be modified for lazy evaluation and then you
would be able to use LINQ on large CSV files.

Are you a [Git](http://gitpilot.com) user? Let me help you make project
management with Git simple. Checkout [Gitpilot](http://gitpilot.com).

I hope you've enjoyed this. You should read my blog on entrepreneurship:
[Techneur](http://techneur.com) or follow me on Twitter:
[@jprichardson](http://twitter.com/jprichardson).

-JP
