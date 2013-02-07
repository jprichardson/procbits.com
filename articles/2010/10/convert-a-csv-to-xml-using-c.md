<!--
author: JP
publish: Thu Oct 07 2010 15:44:33 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2010/10/07/convert-a-csv-to-xml-using-c/
tags: C#
slug: 2010/10/07/convert-a-csv-to-xml-using-c
-->

Convert a CSV to XML using C#
=============================

I wrote a simple class that will help you convert a CSV file to XML. I
can hear you asking "that's nice JP, but why would I want to convert my
simple CSV to that messy text format from the early 2000's?" You would
do this because you want to use LINQ with CSV files... that's why.

Here is the crux of the algorithm:

```csharp
public void Convert() {
    var tempLines = File.ReadAllLines(this.CsvFile);
    string[] lines = null;
    _columnNames = null;
    
    if (this.HasColumnNames) {
        _columnNames = Csv.RecordSplit(tempLines[0], this.RecordDelimiter, this.TextQualifier);
    
        lines = new string[tempLines.Length - 1];
        Array.Copy(tempLines, 1, lines, 0, lines.Length);
    } else {
        var columnCount = Csv.RecordSplit(tempLines[0], this.RecordDelimiter, this.TextQualifier).Length;
        _columnNames = new string[columnCount];
        for (int x = 0; x < _columnNames.Length; ++x)
            _columnNames[x] = "Column" + (x+1);
    
        lines = tempLines;
    }
    
    this.XmlData = new XElement("Records",
        from line in lines
        let fields = Csv.RecordSplit(line, this.RecordDelimiter, this.TextQualifier)
        select new XElement("Record",
            from fieldData in fields
            let i = fields.ToList().FindIndex(f => f == fieldData)
            select new XElement(_columnNames[i], fieldData)
        )
    );
}
```

You can look at the full code in my [C\# CommonLib
library](http://github.com/jprichardson/CommonLib/blob/master/CommonLib/Data/Csv/CsvToXml.cs).
As you can see, the code is really straightforward.

Let's assume that your CSV file looks like this:
` 'Product','Price','DateStocked' 'Pepsi','4.50','2010-05-04' 'Coke','3.00','2010-09-22' 'Cheetos','7.25','2009-01-13'`

You can then run Csv2Xml like this:

```csharp
var csv = new CsvToXml(csvFile);
csv.RecordDelimiter = ','; 
csv.TextQualifier = '\'';
csv.HasColumnNames = false;
csv.Convert();

var actualXml = csv.XmlString;
```

The output XML will look like this:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Records>
    <Record>
        <Column1>Pepsi</Column1>
        <Column2>4.50</Column2>
        <Column3>2010-05-04</Column3>
    </Record>
    <Record>
        <Column1>Coke</Column1>
        <Column2>3.00</Column2>
        <Column3>2010-09-22</Column3>
    </Record>
    <Record>
        <Column1>Cheetos</Column1>
        <Column2>7.25</Column2>
        <Column3>2009-01-13</Column3>
    </Record>
</Records>
```

If you set "HasColumnNames" to "True", your XML output will look like
this:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Records>
    <Record>
        <Product>Pepsi</Product>
        <Price>4.50</Price>
        <DateStocked>2010-05-04</DateStocked>
    </Record>
    <Record>
        <Product>Coke</Product>
        <Price>3.00</Price>
        <DateStocked>2010-09-22</DateStocked>
    </Record>
    <Record>
        <Product>Cheetos</Product>
        <Price>7.25</Price>
        <DateStocked>2009-01-13</DateStocked>
    </Record>
</Records>
```

Are you a [Git](http://gitpilot.com) user? Let me help you make project
management with Git simple. Checkout [Gitpilot](http://gitpilot.com).

Pretty nifty huh? In the next post, I'll explain how you can use this
with LINQ. In the mean time follow me on Twitter
([@jprichardson](http://twitter.com/jprichardson)) or read my blog on
entrepreneurship: [Techneur](http://techneur.com).

-JP
