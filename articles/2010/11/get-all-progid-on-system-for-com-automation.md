<!--
author: JP Richardson
publish: Mon Nov 08 2010 16:48:07 GMT-0600 (CST)
status: publish
type: post
link: https://procbits.wordpress.com/2010/11/08/get-all-progid-on-system-for-com-automation/
tags: C#, Silverlight
slug: 2010/11/08/get-all-progid-on-system-for-com-automation
title: Get All ProgID on System for COM Automation
-->



If you want to use [Silverlight COM
Automation](http://procbits.com/2010/10/28/silverlight-4-and-comactivex-integration/),
you need to know the ProgID of your COM component. These are buried in
the registry. Here is a snippet that I found (don't remember where) and
modified a bit to do this:

```csharp
var regClis = Registry.ClassesRoot.OpenSubKey("CLSID");
var progs = new List<string>();

foreach (var clsid in regClis.GetSubKeyNames()) {
    var regClsidKey = regClis.OpenSubKey(clsid);
    var ProgID = regClsidKey.OpenSubKey("ProgID");
    var regPath = regClsidKey.OpenSubKey("InprocServer32");

    if (regPath == null)
        regPath = regClsidKey.OpenSubKey("LocalServer32");

    if (regPath != null && ProgID != null) {
        var pid = ProgID.GetValue("");
        var filePath = regPath.GetValue("");
        progs.Add(pid + " -> " + filePath);
        regPath.Close();
    }

    regClsidKey.Close();
}

regClis.Close();

progs.Sort();

var sw = new StreamWriter(@"c:\ProgIDs.txt");
foreach (var line in progs)
    sw.WriteLine(line);

sw.Close();
```


