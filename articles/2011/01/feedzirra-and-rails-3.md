<!--
author: JP Richardson
publish: Wed Jan 26 2011 03:50:17 GMT-0600 (CST)
status: publish
type: post
link: https://procbits.wordpress.com/2011/01/25/feedzirra-and-rails-3/
tags: Rails, Ruby
slug: 2011/01/25/feedzirra-and-rails-3
title: Feedzirra and Rails 3
-->



[Feedzirra](https://github.com/pauldix/feedzirra) is probably one of the
best Ruby RSS parsers. But if you're using it with Rails 3, you may get
an error message like so:

`no such file to load -- active_support/core_ext/time`

If that happens, locate your feedzirra.rb file on your machine and
comment out the lines:

```ruby
#require 'active_support/core_ext/object'
#require 'active_support/core_ext/time'
```

Add these lines:

```ruby
require 'active_support/core_ext'
require 'active_support/time'
```
