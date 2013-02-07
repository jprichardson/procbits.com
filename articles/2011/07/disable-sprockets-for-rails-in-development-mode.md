<!--
author: JP
publish: Tue Jul 26 2011 15:30:56 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2011/07/26/disable-sprockets-for-rails-in-development-mode/
tags: Rails, Ruby
slug: 2011/07/26/disable-sprockets-for-rails-in-development-mode
-->

Disable Sprockets for Rails in Development Mode
===============================================

Sprockets (is/are)? the technology in Rails 3.1 and up that combines
your CSS and Javascript files into one CSS and Javascript file. It
remains to be seen if this was a positive change or not. The argument is
that the client only has to make one HTTP request instead of many for
each file; this is a good thing. However, if you change one line in one
of your Javascript files, the client then has to redownload the entire
combined file instead of just the one Javascript file that you modified.

Regardless, you may want to disable Sprockets while you're developing
your Rails application. This will aid in debugging.

Change...

```ruby
<%= stylesheet_include_tag "application" %>
<%= javascript_include_tag "application" %>
```

to...

```ruby
<%= stylesheet_include_tag "application", debug: Rails.env.development? %>
<%= javascript_include_tag "application", debug: Rails.env.development? %>
```

Are you a Git user? Let me help you make project management with Git
simple. Checkout [Gitpilot](http://gitpilot.com).

Follow me on Twitter: [@jprichardson](http://twitter.com/jprichardson)
and read my blog on software entrepreneurship:
[Techneur](http://techneur.com)

-JP
