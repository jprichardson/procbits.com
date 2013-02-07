<!--
author: JP
publish: Fri Aug 03 2012 16:09:40 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2012/08/03/like-unix-expect-automate-command-line-programs-in-node-js-with-suppose/
tags: JavaScript, Node.js
slug: 2012/08/03/like-unix-expect-automate-command-line-programs-in-node-js-with-suppose
-->

Like Unix Expect: Automate Command Line Programs in Node.js with Suppose
========================================================================

Have you ever heard of the command line program
[expect](http://en.wikipedia.org/wiki/Expect)? Basically, expect allows
you to automate command line programs.
[suppose](https://github.com/jprichardson/node-suppose) is a
programmable Node.js module that allows the same behavior.

Why would you do this? Maybe you want to automate a ssh session? Or,
maybe you want to test the external interface of on of your Node.js
command line scripts.

Install: ` npm install suppose`

Example:

```javascript
process.chdir('/tmp/awesome');
suppose('npm', ['init'])
  .on('name: (awesome) ').respond('awesome_package\n')
  .on('version: (0.0.0) ').respond('0.0.1\n')
  .on('description: ').respond("It's an awesome package man!\n")
  .on('entry point: (index.js) ').respond("\n")
  .on('test command: ').respond('npm test\n')
  .on('git repository: ').respond("\n")
  .on('keywords: ').respond('awesome, cool\n')
  .on('author: ').respond('JP Richardson\n')
  .on('license: (BSD) ').respond('MIT\n')
  .on('ok? (yes) ' ).respond('yes\n')
.end(function(code){
    assert(code === 0);
    var packageFile = '/tmp/awesome/package.json';
    fs.readFile(packageFile, function(err, data){
        var packageObj = JSON.parse(data.toString());
        assert(packageObj.name === 'awesome_package');
        assert(packageObj.version === '0.0.1');
        assert(packageObj.description === "It's an awesome package man!");
        assert(packageObj.main === 'index.js');
        assert(packageObj.scripts.test === 'npm test');
        assert(packageObj.keywords[0] === 'awesome');
        assert(packageObj.keywords[1] === 'cool');
        assert(packageObj.author === 'JP Richardson');
        assert(packageObj.license === 'MIT');
        done();
    });
});
```

Pretty easy, huh? You can grab the source on
[Github](https://github.com/jprichardson/node-suppose).

If you use Git with others, you should checkout
[Gitpilot](http://gitpilot.com) to make collaboration with Git simple
using a different GUI. We would love your feedback.

Follow me on Twitter: [@jprichardson](http://twitter.com/jprichardson)

-JP
