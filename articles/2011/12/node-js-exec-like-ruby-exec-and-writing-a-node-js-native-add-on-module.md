<!--
author: JP Richardson
publish: Mon Dec 05 2011 02:14:55 GMT-0600 (CST)
status: publish
type: post
link: https://procbits.wordpress.com/2011/12/04/node-js-exec-like-ruby-exec-and-writing-a-node-js-native-add-on-module/
tags: JavaScript, Node.js
slug: 2011/12/04/node-js-exec-like-ruby-exec-and-writing-a-node-js-native-add-on-module
title: Node.js Exec Like Ruby Exec and Writing a Node.js Native Add On Module
-->



Recently, I was faced with a problem that required my Node.js programs
process to execute another process and have the procoess that's passed
to the exec function completely replace the Node.js process. In short, I
wanted an 'exec' function like Ruby's 'exec' function. Unfortunately,
out of the box, Node.js doesn't support this functionality. I asked on
[Stackoverflow.com, and someone had a
response](http://stackoverflow.com/questions/8362181/like-ruby-exec-but-for-node-js)
that I should use the [POSIX exec
functions](http://linux.die.net/man/3/exec) to solve my problem and to
consider writing a native Node.js extension.

` npm install kexec`

You can then use it like:

```javascript
var kexec = require('kexec');
kexec('top'); //you can pass any process that you want here
```

Here is the C++ source for Node Kexec:

```cpp

#include <v8.h>
#include <node.h>
#include <cstdio>

//#ifdef __POSIX__
#include <unistd.h>
/*#else
#include <process.h>
#endif*/

using namespace node;
using namespace v8;

static Handle<Value> kexec(const Arguments& args) {
    String::Utf8Value v8str(args[0]);
    char* argv2[] = {"", "-c", *v8str, NULL};

    execvp("/bin/sh", argv2);      
    return Undefined();
}

extern "C" {
    static void init (Handle<Object> target) {
        NODE_SET_METHOD(target, "kexec", kexec);
    }

    NODE_MODULE(kexec, init);
}
```

As you can see, writing a C++ add on in Node.js isn't too difficult. You
can use it in your Node.js Javascript like so:

```javascript
var kexec;

try {
  kexec = require("./build/default/kexec.node"); //Node.js v0.4
} catch(e) {
  kexec = require("./build/Release/kexec.node"); //Node.js v0.6
}

module.exports = kexec.kexec; //function of kexec module is named kexec
```

Don't forget your wscript file, which ironically is Python code:

```python
def set_options(opt):
  opt.tool_options("compiler_cxx")

def configure(conf):
  conf.check_tool("compiler_cxx")
  conf.check_tool("node_addon")

def build(bld):
  obj = bld.new_task_gen("cxx", "shlib", "node_addon") 
  obj.cxxflags = ["-g", "-D_FILE_OFFSET_BITS=64", "-D_LARGEFILE_SOURCE","-Wall"]
  obj.target = "kexec"
  obj.source = "src/node_kexec.cpp"
```

In your package.json, include this bit:

```javascript
"scripts": { "install": "node-waf configure build" }
```

Github Sourcecode: [Node.js kernel
exec](https://github.com/jprichardson/node-kexec)

I've also included other resources for writing a Node.js Native Add On
Module:

1.  [Google V8 Engine Getting
    Started](http://code.google.com/apis/v8/get_started.html)
2.  [Google V8 Embedder's
    Guide](http://code.google.com/apis/v8/embed.html)
3.  [How to Roll Your Own Javascript API with
    V8](http://syskall.com/how-to-roll-out-your-own-javascript-api-with)
4.  [How to Write Your Own Native Node.js
    Extension](http://syskall.com/how-to-write-your-own-native-nodejs-extension)
5.  [Writing Node.js Native
    Extensions](https://www.cloudkick.com/blog/2010/aug/23/writing-nodejs-native-extensions/)
6.  [Node.js Native Extension with Hammer and a
    Prayer](http://odoe.net/blog/?p=168)
7.  [Mastering Node; Add
    Ons](http://www.ipreferjim.com/2011/04/node-js-mastering-node-excerpt-addons/)
8.  [Node.js Documentation: Add
    Ons](http://nodejs.org/docs/v0.6.4/api/addons.html)
9.  [Postgres Node.js Module](https://github.com/ry/node_postgres)
10. [V8 Sample:
    shell.cc](http://v8.googlecode.com/svn/trunk/samples/shell.cc)
11. [V8
    Objects](http://create.tpsitulsa.com/blog/2009/01/29/v8-objects/)
12. [There's C in My
    JavaScript](http://nikhilm.bitbucket.org/articles/c_in_my_javascript/c_in_javascript_part_2.html)
13. [Converting V8 Arguments to C++
    Types](http://stackoverflow.com/questions/7476145/converting-from-v8arguments-to-c-types)

Do you use Git? If so, checkout [Gitpilot](http://gitpilot.com) to make
using Git easy.

Follow me on Twitter: [@jprichardson](http://twitter.com/jprichardson)
and read my blog on entrepreneurship: [Techneur](http://techneur.com).

-JP Richardson
