<!--
author: JP Richardson
publish: Thu Oct 20 2011 15:21:58 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2011/10/20/modifying-node_path-for-node-jsnpmnvm/
tags: Node.js
slug: 2011/10/20/modifying-node_path-for-node-jsnpmnvm
-->

Modifying $NODE_PATH for Node.js/NPM/NVM
========================================

I've been hacking around with Node.js lately. If you develop with
Node.js then you should be using both [NPM](http://npmjs.org/) (package
manager) and [NVM](https://github.com/creationix/nvm) (node version
manager, like RVM).

For some reason, NVM doesn't seem to set \$NODE\_PATH when you switch
Node versions. \$NODE\_PATH needs to be set so that when you call
'require' from within your Node programs, it can find the relevant
modules.

If you don't set \$NODE\_PATH, that's OK too. But you'll find that in
your project directory you'll need to do a lot of linking. You can do
this like so:

```bash
npm install -g express #the '-g' flag installs the module to the npm node_module directory
npm link express
```

Doing this is OK, but is a bit annoying at times because some modules
still won't work if you link with them, so you're left installing it
locally to your project's directory.

I hacked up some bash commands to set my Node environment how I like it.
This also allows me to make my own Node modules.

My \~/.bash\_profile on Mac OS X Lion

```bash
[[ -s "$HOME/.rvm/scripts/rvm" ]] && . "$HOME/.rvm/scripts/rvm" # Load RVM function
PROJ=~/Dropbox/Projects
. ~/.nvm/nvm.sh

JP_NODE_PATH=/Users/jprichardson/Dropbox/Projects/Personal/js/node_modules
JP_NODE_BIN_PATH="${JP_NODE_PATH}/.bin"

NP=$(which node) 
BP=${NP%bin/node} #this replaces the string '/bin/node'
LP="${BP}lib/node_modules"

export PATH="$PATH:$JP_NODE_BIN_PATH"
export NODE_PATH="$JP_NODE_PATH:$LP"

```

When I run \`which node\` this is capturing the output from my default
Node version. You set this like: `nvm alias default 0.4` which will use
the latest 0.4, which is 0.4.12.

Again, I'm not a bash hacker by any means. I didn't even know [why you
need to use
'export'](http://stackoverflow.com/questions/1158091/bash-defining-a-variable-with-or-without-export).
I didn't know how to [concatenate strings in a bash
variable](http://desk.stinkpot.org:8080/tricks/index.php/2007/02/concatenate-strings-in-bash/).
Or, even how to [run a command and capture the output in a
variable](http://stackoverflow.com/questions/4651437/how-to-set-a-bash-variable-equal-to-the-output-from-a-command).
I also didn't know how to [modify strings in
bash](http://www.faqs.org/docs/abs/HTML/string-manipulation.html). What
kind of hacker am I? ::sobs:: Hehehe.

Do you use Git? If so, checkout [Gitpilot](http://gitpilot.com) to make
using Git thoughtless.

Follow me on Twitter: [@jprichardson](http://twitter.com/jprichardson)
and read my blog on entrepreneurship: [Techneur](http://techneur.com).

-JP Richardson
