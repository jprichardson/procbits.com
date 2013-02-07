<!--
author: JP
publish: Thu Mar 01 2012 03:42:27 GMT-0600 (CST)
status: publish
type: post
link: https://procbits.wordpress.com/2012/02/29/submittingposting-files-and-fields-to-an-http-form-using-c-net/
tags: C#
slug: 2012/02/29/submittingposting-files-and-fields-to-an-http-form-using-c-net
-->

Submitting/Posting Files and Fields to an HTTP Form using C#/.NET 
==================================================================

Awhile back, I had to integrate a C\# program with a web system that
allowed the user to upload a few files and include some misc. data. I
Googled around and didn't find a comprehensive solution.

I did use some code I found on the internet, unfortunately I don't
remember where, so I can't give proper attribution. If you know, please
let me know; it's the code relevant to the `MimePart` class. I added the
form values code and packaged it up into the `HttpForm` sugar.

Here is the code:

```csharp
public class HttpForm {

    private Dictionary<string, string> _files = new Dictionary<string, string>();
    private Dictionary<string, string> _values = new Dictionary<string, string>();

    public HttpForm(string url) {
        this.Url = url;
        this.Method = "POST";
    }

    public string Method { get; set; }
    public string Url { get; set; }

    //return self so that we can chain
    public HttpForm AttachFile(string field, string fileName) {
        _files[field] = fileName;
        return this;
    }

    public HttpForm ResetForm(){
        _files.Clear();
        _values.Clear();
        return this;
    }

    //return self so that we can chain
    public HttpForm SetValue(string field, string value) {
        _values[field] = value;
        return this;
    }

    public HttpWebResponse Submit() {
        return this.UploadFiles(_files, _values);
    }


    private HttpWebResponse UploadFiles(Dictionary<string, string> files, Dictionary<string, string> otherValues) {
        var req = (HttpWebRequest)WebRequest.Create(this.Url);

        req.Timeout = 10000 * 1000;
        req.Accept = "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8";
        req.AllowAutoRedirect = false;

        var mimeParts = new List<MimePart>();
        try {
            if (otherValues != null) {
                foreach (var fieldName in otherValues.Keys) {
                    var part = new MimePart();

                    part.Headers["Content-Disposition"] = "form-data; name=\"" + fieldName + "\"";
                    part.Data = new MemoryStream(Encoding.UTF8.GetBytes(otherValues[fieldName]));

                    mimeParts.Add(part);
                }
            }

            if (files != null) {
                foreach (var fieldName in files.Keys) {
                    var part = new MimePart();

                    part.Headers["Content-Disposition"] = "form-data; name=\"" + fieldName + "\"; filename=\"" + files[fieldName] + "\"";
                    part.Headers["Content-Type"] = "application/octet-stream";
                    part.Data = File.OpenRead(files[fieldName]);

                    mimeParts.Add(part);
                }
            }

            string boundary = "----------" + DateTime.Now.Ticks.ToString("x");

            req.ContentType = "multipart/form-data; boundary=" + boundary;
            req.Method = this.Method;

            long contentLength = 0;

            byte[] _footer = Encoding.UTF8.GetBytes("--" + boundary + "--\r\n");

            foreach (MimePart part in mimeParts) {
                contentLength += part.GenerateHeaderFooterData(boundary);
            }

            req.ContentLength = contentLength + _footer.Length;

            byte[] buffer = new byte[8192];
            byte[] afterFile = Encoding.UTF8.GetBytes("\r\n");
            int read;

            using (Stream s = req.GetRequestStream()) {
                foreach (MimePart part in mimeParts) {
                    s.Write(part.Header, 0, part.Header.Length);

                    while ((read = part.Data.Read(buffer, 0, buffer.Length)) > 0)
                        s.Write(buffer, 0, read);

                    part.Data.Dispose();

                    s.Write(afterFile, 0, afterFile.Length);
                }

                s.Write(_footer, 0, _footer.Length);
            }

            var res = (HttpWebResponse)req.GetResponse();

            return res;
        } catch (Exception ex) {
            Console.WriteLine(ex.Message);
            foreach (MimePart part in mimeParts)
                if (part.Data != null)
                    part.Data.Dispose();

            return (HttpWebResponse)req.GetResponse();
        }
    }

    private class MimePart {
        private NameValueCollection _headers = new NameValueCollection();
        public NameValueCollection Headers { get { return _headers; } }

        public byte[] Header { get; protected set; }

        public long GenerateHeaderFooterData(string boundary) {
            StringBuilder sb = new StringBuilder();

            sb.Append("--");
            sb.Append(boundary);
            sb.AppendLine();
            foreach (string key in _headers.AllKeys) {
                sb.Append(key);
                sb.Append(": ");
                sb.AppendLine(_headers[key]);
            }
            sb.AppendLine();

            Header = Encoding.UTF8.GetBytes(sb.ToString());

            return Header.Length + Data.Length + 2;
        }

        public Stream Data { get; set; }
    }
}
```

You can easily use it like so:

```csharp
var file1 = @"C:\file";
var file2 = @"C:\file2";

var yourUrl = "http://yourdomain.com/process.php";
var httpForm = new HttpForm(yourUrl);
httpForm.AttachFile("file1", file1).AttachFile("file2", file2);
httpForm.setValue("foo", "some foo").setValue("blah", "rarrr!");
httpForm.Submit();
```

Do you use Git? If so, checkout [Gitpilot](http://gitpilot.com) to make
using Git thoughtless.

Follow me on Twitter: [@jprichardson](http://twitter.com/jprichardson).

-JP Richardson
