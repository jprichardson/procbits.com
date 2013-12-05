<!--
author: JP Richardson
publish: Sat Apr 16 2011 01:27:25 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2011/04/15/locate-and-updatedb-on-os-x-snow-leopard/
tags: OSX
slug: 2011/04/15/locate-and-updatedb-on-os-x-snow-leopard
title: locate and updatedb on OS X Snow Leopard
-->



On Linux, I love using the "locate" utility. In essence, the locate
utility works similar to the "find" utility but it's much faster. This
is because the locate utility works in conjunction with the "updatedb"
utility. updatedb builds an indexed database of the file names, this is
how locate runs so quickly.

I was stoked to find out that OS X has locate, but where is its partner
in crime updatedb? Google to the rescue,
[djangrrl.com](http://www.djangrrl.com/view/update-locate-database-for-mac-os-x/)
has the answer: ` sudo /usr/libexec/locate.updatedb`

