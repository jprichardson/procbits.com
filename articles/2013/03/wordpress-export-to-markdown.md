<!--
author: JP Richardson
publish: 2013-03-23
tags: meta
slug: 2013/03/02/wordpress-export-to-markdown-hosted-on-amazon-s3
title: Wordpress Export to Markdown Hosted on Amazon S3
-->



You may have been able to tell, but this blog looks different. Yes, I finally left Wordpress. I converted my Wordpress blog to a static generated Markdown blog. It's fairly straight forward.


Why?
----

- I hated writing my blog posts in Wordpress. I wanted to write in pure Markdown since I'm such a big [open source advocate](https://github.com/jprichardson) and have been using Markdown a lot with Github.
- I wanted complete control on the look and the feel.
- I hate the idea of maintaining a server for a blog.


Must Haves:
-----------
- Must not break any links. For obvious SEO purposes. That's why I'm using Amazon S3 over Github.
- Must be able to use existing comments. (Disqus handled this beautifully)
- Must be able to control the entire look and feel.


How?
----

It's pretty straightforward:

1. Installed Pandoc: http://code.google.com/p/pandoc/downloads/list 
2. Exported my Wordpress blog from a tool that I wrote: https://github.com/jprichardson/potter-wordpress
3. Dumped my comments from Wordpress export and imported them using Disqus import tool. You'll need to use an XML sanitization tool on the Wordpress exported XML data... I don't remember what I used, but if you Google for it, I'm sure you can find it. I think I used `xmllint`.
4. Followed Amazon tutorial on redirecting www.procbits.com to procbits.com and keep all links in tact.
5. Wrote my own static blog generator (all the cool kids are doing it), [Sky](https://github.com/skywrite/sky), to actually create the html.
3. Deployed to S3 using another tool that I wrote (Basin): https://github.com/skywrite/basin.

There are still a lot of things to do. But for now, I'm satisfied.



