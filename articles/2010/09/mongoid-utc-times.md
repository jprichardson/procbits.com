<!--
author: JP Richardson
publish: Fri Sep 24 2010 03:36:31 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2010/09/23/mongoid-utc-times/
tags: MongoDB, Rails, Ruby
slug: 2010/09/23/mongoid-utc-times
title: Mongoid UTC Times
-->



By default Mongoid will store your Time objects in your local timezone.
It's not made clear on the [installation
page](http://mongoid.org/docs/installation/), but all you have to do is
add "use\_utc: true" to the defaults in your mongoid.yml configuration
file. Yes Virginia, it's that simple.

Are you a [Git](http://gitpilot.com) user? Let me help you make project
management with Git simple. Checkout [Gitpilot](http://gitpilot.com).

-JP
