<!--
author: JP Richardson
publish: Thu Sep 09 2010 04:59:46 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2010/09/08/rvm-is-a-must-for-the-rubyist/
tags: Rails, Ruby
slug: 2010/09/08/rvm-is-a-must-for-the-rubyist
-->

RVM Is a Must for the Rubyist
=============================

If you are a Ruby or Rails developer and you haven't heard of
[RVM](http://rvm.beginrescueend.com/) yet, I'll let you in on the
secret. It rocks! Basically, RVM allows you to easily simultaneously run
multiple ruby version on the same machine. You see, Rails 3.0 just came
out and I thought it would run on Ruby 1.9.1 but it won't. It require's
Ruby 1.9.2. So I upgraded my OS X dev box and manually created the new
symlinks. All was well, but I wasn't sure how my apps that required
1.9.1 would behave. So I researched RVM.

To install on your OS X box, it's quite simple. Just make sure that you
have XCode installed, as you'll need the GCC.

```bash
bash < <( curl http://rvm.beginrescueend.com/releases/rvm-install-head )
```

That's all you need to do to install it!

Edit \~./.bash\_profile with the following:

```bash
[[ -s "$HOME/.rvm/scripts/rvm" ]] && source "$HOME/.rvm/scripts/rvm"  
```

Open a new shell and you're ready to rock.

To install a Ruby version 1.9.2, just type:

```bash
rvm install 1.9.2
```

Or for version 1.8.7

```bash
rvm install 1.8.7
```

Switch versions like:

```bash
rvm 1.9.2
```

To see where "ruby" is actually installed you type:

```bash
which ruby
```

You'll see:

```bash
/Users/jp/.rvm/rubies/ruby-1.9.2-p0/bin/ruby
```

To see the gem path:

```bash
gem env path
```

Its output:

```bash
/Users/jp/.rvm/gems/ruby-1.9.2-p0:/Users/jp/.rvm/gems/ruby-1.9.2-p0@global
```

It's like magic! Take note: [according to the
guide](http://rvm.beginrescueend.com/rvm/install/), you should NOT run
"sudo" to install rvm.

Are you a [Git](http://gitpilot.com) user? Let me help you make project
management with Git simple. Checkout [Gitpilot](http://gitpilot.com).

Read me blog on entrepreneurship: [Techneur](http://techneur.com) Follow
me on Twitter: [@jprichardson](http://twitter.com/jprichardson)

-JP
