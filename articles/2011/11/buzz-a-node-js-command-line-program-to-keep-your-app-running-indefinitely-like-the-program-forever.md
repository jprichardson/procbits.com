<!--
author: JP
publish: Tue Nov 08 2011 20:35:56 GMT-0600 (CST)
status: publish
type: post
link: https://procbits.wordpress.com/2011/11/08/buzz-a-node-js-command-line-program-to-keep-your-app-running-indefinitely-like-the-program-forever/
tags: Linux, Node.js
slug: 2011/11/08/buzz-a-node-js-command-line-program-to-keep-your-app-running-indefinitely-like-the-program-forever
-->

Buzz: A Node.js Command Line Program to Keep Your App Running Indefinitely; Like the Program Forever
====================================================================================================

Buzz is a command line program that can kill your app routinely and
restart it. It'll will also restart your app if it dies. It's a lot like
the other Node.js program
[Forever](https://github.com/indexzero/forever).

It's much simpler than Forever. Approximately 50 lines of CoffeeScript
code. It displays your apps output to STDOUT and also displays any of
your apps STDERR output in red.

**Usage**

Install it via npm: ` npm install buzz`

Then run: ` buzz 240 your_cool_app param1 param2`

The first parameter to buzz is the time in seconds that it'll be killed
and restarted. So, \`your\_cool\_app\` would be killed and restarted
after four minutes.

If you don't want buzz to kill your app, but you want it to bring it
back to life if it dies, run: ` buzz your_cool_app param1 param2`

You can test buzz by running his the app \`buzz\_test\`: ` buzz_test`

\`buzz\_test\` runs the app \`smarty\_pants\` that spews out random
facts to you and taunts you. Occasionally \`smarty\_pants\` will commit
suicide, but buzz will bring him back to life.

\`buzz\_test\` ends up actualy just running the following command:
` buzz 10 smarty_pants 2000 0.15`

Which will kill smarty pants every 10 seconds and bring him back to
life. Also, every two seconds, smarty pants will spit out a random fact.
Approximately, every 13 seconds smarty pants will take his own life, but
Buzz will bring him back.

**Motivation**

I have a command line app that is nasty to debug. It's working fine for
the first five minutes or so. Thus, Buzz was born. Instead of fixing the
bug, I wanted to make this. =)

But really, it's utility is that it's a much simpler Forever.

The name comes from Buzz Lightyear in the movie Toy Story. His popular
phrase was: To infinity and beyond!

Do you use Git? If so, checkout [Gitpilot](http://gitpilot.com) to make
using Git thoughtless.

Follow me on Twitter: [@jprichardson](http://twitter.com/jprichardson)
and read my blog on entrepreneurship: [Techneur](http://techneur.com).

-JP Richardson
