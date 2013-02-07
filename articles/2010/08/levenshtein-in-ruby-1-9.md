<!--
author: JP
publish: Sat Aug 07 2010 06:59:53 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2010/08/07/levenshtein-in-ruby-1-9/
tags: Ruby
slug: 2010/08/07/levenshtein-in-ruby-1-9
-->

Levenshtein in Ruby 1.9
=======================

In Ruby 1.8, I would use the gem 'levenshtein'... well that doesn't work
in 1.9. So you need to install the gem 'text'.

Then the call is as simple as:

```ruby
Text::Levenshtein.distance('hello', 'hell')
```

Are you a [Git](http://gitpilot.com) user? Let me help you make project
management with Git simple. Checkout [Gitpilot](http://gitpilot.com).

Follow me on Twitter: [@jprichardson](http://twitter.com/jprichardson)

-JP
