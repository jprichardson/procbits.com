<!--
author: JP Richardson
publish: Tue Nov 15 2011 21:58:04 GMT-0600 (CST)
status: publish
type: post
link: https://procbits.wordpress.com/2011/11/15/synchronous-file-copy-in-node-js/
tags: CoffeeScript, Node.js, JavaScript
slug: 2011/11/15/synchronous-file-copy-in-node-js
title: Synchronous File Copy in Node.js
-->



Sometimes, asynchronous operations can be a burden. Especially when
you're writing small console utilities like to batch process files.

There are many asynchronous ways to copy a file. Here is a synchronous
version (CoffeeScript):

```ruby
copyFileSync = (srcFile, destFile) ->
  BUF_LENGTH = 64*1024
  buff = new Buffer(BUF_LENGTH)
  fdr = fs.openSync(srcFile, 'r')
  fdw = fs.openSync(destFile, 'w')
  bytesRead = 1
  pos = 0
  while bytesRead > 0
    bytesRead = fs.readSync(fdr, buff, 0, BUF_LENGTH, pos)
    fs.writeSync(fdw,buff,0,bytesRead)
    pos += bytesRead
  fs.closeSync(fdr)
  fs.closeSync(fdw)
```

You can view the [converted version in
JavaScript][url].



[url]: http://jashkenas.github.io/coffee-script/#try:copyFileSync%20%3D%20(srcFile%2C%20destFile)%20-%3E%0A%20%20BUF_LENGTH%20%3D%2064*1024%0A%20%20buff%20%3D%20new%20Buffer(BUF_LENGTH)%0A%20%20fdr%20%3D%20fs.openSync(srcFile%2C%20%22r%22)%0A%20%20fdw%20%3D%20fs.openSync(destFile%2C%20%22w%22)%0A%20%20bytesRead%20%3D%201%0A%20%20pos%20%3D%200%0A%20%20while%20bytesRead%20%3E%200%0A%20%20%20%20bytesRead%20%3D%20fs.readSync(fdr%2C%20buff%2C%200%2C%20BUF_LENGTH%2C%20pos)%0A%20%20%20%20fs.writeSync(fdw%2Cbuff%2C0%2CbytesRead)%0A%20%20%20%20pos%20%2B%3D%20bytesRead%0A%20%20fs.closeSync(fdr)%0A%20%20fs.closeSync(fdw)
