<!--
author: JP Richardson
publish: Wed Nov 10 2010 13:00:38 GMT-0600 (CST)
status: publish
type: post
link: https://procbits.wordpress.com/2010/11/10/iterate-over-files-in-bash/
tags: Linux
slug: 2010/11/10/iterate-over-files-in-bash
-->

Iterate Over Files in Bash
==========================

Sometimes you need to whip up a dirty bash script to process through
some text. I needed to iterate over my database log files that I wrote
about from my [last
post](http://procbits.com/2010/11/09/grep-for-two-or-more-expressions/)
and then output the result to a new file. I wanted the output files to
have a different extension, but I wasn't sure how to get just the
filename from bash, fortunately [this post
helped](http://stackoverflow.com/questions/965053/extract-filename-and-extension-in-bash).

```bash
#!/bin/bash

for logfile in *.log
do

fn=${logfile%.*} #cut extension

echo making $fn.data...
egrep '\#\ collect|\:\=' $logfile > $fn.data 

done
```

Are you a [Git](http://gitpilot.com) user? Let me help you make project
management with Git simple. Checkout [Gitpilot](http://gitpilot.com).

Follow me on Twitter [@jprichardson](http://twitter.com/jprichardson)
and read my [blog on software entrepreneurship](http://techneur.com).

-JP
