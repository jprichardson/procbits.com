<!--
author: JP Richardson
publish: Wed Apr 08 2009 02:00:42 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2009/04/07/convert-innodb-tables-to-myisam/
tags: MySql
slug: 2009/04/07/convert-innodb-tables-to-myisam
title: Convert InnoDB Tables to MyISAM
-->



I have been trying to optimize my 256 MB slice from
[slicehost](http://slicehost.com). One of the strategies is to **not**
use the InnoDB SQL engine for your tables and instead use MyISAM. If you
need transactions or foreign key support you should not do this.

I came across this
[snippet](http://codesnippets.joyent.com/posts/show/1451):
` SELECT CONCAT('ALTER TABLE ',table_schema,'.',table_name,' engine=MyISAM;') FROM information_schema.tables WHERE engine = 'InnoDB';`

It seems to have worked. Maybe I'll provide an article or tutorial in
the future on benchmarking your slice.

Are you a [Git](http://gitpilot.com) user? Let me help you make project
management with Git simple. Checkout [Gitpilot](http://gitpilot.com).

-JP
