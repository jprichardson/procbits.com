<!--
author: JP Richardson
publish: Wed Jul 27 2011 14:54:55 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2011/07/27/printing-to-a-postscript-file-from-delphic-builder/
tags: Uncategorized
slug: 2011/07/27/printing-to-a-postscript-file-from-delphic-builder
-->

Printing to a Postscript File from Delphi/C++ Builder
=====================================================

Unfortunately for me, I had to patch some legacy C++ Builder code to
output the reports that were sent to a printer to a PDF file.
Fortunately, many printers support standard Postscript and converting to
a PDF from a Postscript is trivial.

Here are the instructions, assuming Windows XP: 1)Go to Printers/Faxes
and Add New Printer 2)When the wizard pops up click “Next” 3)You should
be on the screen “Add Printer Wizard”… select “Local Printer Attached”,
uncheck “Automatically detect…” 4)Use port “LPT1” 5)Select “Apple” for
manufacturer and printers select “Apple LaserWriter 12/640 PS” 6)In
printer name I put “Apple PS” 7)For default, you can select “No” 8)No on
sharing/or printing test page.

Now we have a Postscript printer installed.

Here is some sample code:

```cpp
void __fastcall TForm2::Button1Click(TObject *Sender)
{
    TDocInfo di;
 
    int i = Printer()->Printers->IndexOf("Apple PS");
    Printer()->PrinterIndex = i;
 
    Printer()->BeginDoc();
    EndPage(Printer()->Canvas->Handle);
    AbortDoc(Printer()->Canvas->Handle);
 
    memset(&di, sizeof(di), 0);
    di.cbSize = sizeof(di);
    di.lpszDocName = "TEST";
    di.lpszOutput = "C:\\somefile.ps";
    StartDoc(Printer()->Canvas->Handle, &di);
    StartPage(Printer()->Canvas->Handle);
 
    Printer()->Canvas->TextOutA(0,0, "Testing Printer");
    Printer()->EndDoc();
}
```

Are you a Git user? Let me help you make project management with Git
simple. Checkout [Gitpilot](http://gitpilot.com).

Follow me on Twitter: [@jprichardson](http://twitter.com/jprichardson)
and read my blog on software entrepreneurship:
[Techneur](http://techneur.com)

-JP
