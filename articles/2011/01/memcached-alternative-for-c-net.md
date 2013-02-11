<!--
author: JP Richardson
publish: Fri Jan 21 2011 19:25:07 GMT-0600 (CST)
status: publish
type: post
link: https://procbits.wordpress.com/2011/01/21/memcached-alternative-for-c-net/
tags: C#
slug: 2011/01/21/memcached-alternative-for-c-net
-->

Memcached Alternative for C#/.NET
=================================

I'm currently developing a software package that has to retrieve large
amounts of data from a server. This data is needed frequently throughout
the application and doesn't change much in a given day. This sounds like
a perfect use case for
[Memcached](http://en.wikipedia.org/wiki/Memcached), or so I thought.
After I installed the .NET Memcached adapters, I was in for a big
surprise. Memcached has a 1 MB value limit!

So, like any good developer I wrote my own tool. First, two caveats, as
I don't want to waste your time. 1) I wouldn't use this in a production
environment. It just hasn't had enough rigorous testing. YMMV. 2) It
only works on the same machine that the app resides on. Yes, I realize
that this is a huge caveat.

### Why?

So, what was my use case? I follow a loose Agile process of delivering
every week. Well, as the production went on, more data would accumulate
on the server. I needed a quick way to cache this data locally without
rewriting my algorithms in the short term. Thus,
[MemMapCache](https://github.com/jprichardson/MemMapCache) was born.

### How Does It Work?

It uses memory mapped files that are new in .NET 4.0 (they just wrap the
Win32 API). The client creates a key and persists the object value to a
memory mapped file using the said key. It then passes the key onto the
server so that the server can keep the reference. This prevents the .NET
garbage collector from cleaning up the memory mapped file so that upon a
new instance of the client, it can still retrieve the data.

### How do you use it?

Go clone this project:
[https://github.com/jprichardson/MemMapCache](https://github.com/jprichardson/MemMapCache)
and then add MemMapCacheLib to your project. You'll need to make sure
that MemMapCache.exe is running on your system.

```csharp
var cache = new MemMapCache();
cache.Connect();

var col = new Dictionary<string, int>();
col.Add("hello", 5);
col.Add("hi", 2);

cache.Set("myKey", col);

var newCol = cache.Get<Dictionary<string,int>>("myKey");

//you can pass in an expiration time as DateTime or TimeSpan
cache.Set("newKey", "some data", DateTime.Now.AddMinutes(4)); 

//hypothetical large data repository that returns IEnumerable<double>
var dataRepo = new DataRepository(); 

//you can even call one function to retrieve the last value for a key, if it doesn't exist set a new value
IEnumerable<double> largeDataSet = cache.TryGetThenSet("dataSetKey", () => dataRepo.LoadLargeDataSet());

//you can also specify for the cache to always miss, in case you have it deeply embedded in your code and you want to run unit tests that aren't cache dependent
cache.CacheHitAlwaysMiss = true
```

That's it. It's come in handy for me during development. If you find
this useful please leave a comment.

Are you a [Git](http://gitpilot.com) user? Let me help you make project
management with Git simple. Checkout [Gitpilot](http://gitpilot.com).

Follow me on Twitter [@jprichardson](http://twitter.com/jprichardson) or
read my blog on entrepreneurship: [Techneur](http://techneur.com)

-JP
