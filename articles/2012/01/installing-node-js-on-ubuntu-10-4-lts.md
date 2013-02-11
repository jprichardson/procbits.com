<!--
author: JP Richardson
publish: Mon Jan 30 2012 04:29:57 GMT-0600 (CST)
status: publish
type: post
link: https://procbits.wordpress.com/2012/01/29/installing-node-js-on-ubuntu-10-4-lts/
tags: Node.js
slug: 2012/01/29/installing-node-js-on-ubuntu-10-4-lts
-->

Installing Node.js on Ubuntu 10.04 LTS 
=======================================

Installing Node.js on Ubuntu 10.04 LTS is pretty straight forward.

You will want a Node.js versioning manager. Node.js has a quick release
cycle, point releases happen quite frequently. A Node.js versioning
manager will help you keep all of your versions isolated from each
other.

As it stands today, there are four Node.js version managers. They are:

1.  [NVM](https://github.com/creationix/nvm) - NVM works like RVM. It
    must be sourced in your \~./bashrc or \~./profile file. Some people
    don't like this. It's my understanding that some find this to be a
    bit of hackery.
2.  [Nave](https://github.com/isaacs/nave) - Nave doesn't need to be
    sourced or loaded up into your bash profile. But, when you use Nave
    it executes commands into a
    [subshell](http://docstore.mik.ua/orelly/unix/upt/ch38_04.htm). It's
    my understanding that if any process in a subshell modifies the
    environment then these changes won't persist to the parent process.
    It's not entirely clear these changes persist or not. But the
    rhetoric from some regarding using subshells for version management
    was enough to drive me away.
3.  [n](https://github.com/visionmedia/n) - I love the simplicity of
    'n'. It doesn't use subshells and it doesn't require that you modify
    your bash profile. I would use 'n' if it installed NPM (Node.js
    package manager) with each release, and [it
    doesn't](https://github.com/visionmedia/n/issues/47).
4.  [nodeenv](https://github.com/ekalinin/nodeenv)- I never seriously
    considered this one as it requires Python to be installed. I haven't
    read about anyone using this. But I wanted to list it so that you'd
    be informed about its existence.

Use NVM. Seriously, it just works.

On your clean Ubuntu machine, make sure that Git is installed:

```bash
sudo apt-get install git-core
```

Then install NVM:

```bash
git clone git://github.com/creationix/nvm.git ~/.nvm
. ~/.nvm/nvm.sh # <------ be sure to add this line to the end of your ~./profile or ~./bashrc file
```

Now install all of the packages need to build Node.js:

```bash
sudo apt-get install build-essential openssl libssl-dev pkg-config
```

Now install the latest version of Node.js, at the time of this writing
it's v0.6.9

```bash
nvm install v0.6.9
```

You now have a Node.js environment on your machine! Just run `node` on
the command line to experiment with the Node.js REPL. You can also run
`npm` to install Node.js packages. Read more about [NPM
here](http://npmjs.org/).

Do you use Git? If so, checkout [Gitpilot](http://gitpilot.com) to make
using Git mindless.

Follow me on Twitter: [@jprichardson](http://twitter.com/jprichardson)
and read my blog on entrepreneurship: [Techneur](http://techneur.com).

-JP Richardson
