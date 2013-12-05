<!--
author: JP Richardson
publish: Thu May 05 2011 00:51:29 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2011/05/04/installing-mongodb-1-8-1-on-ubuntu-10-04-lts/
tags: Linux, MongoDB
slug: 2011/05/04/installing-mongodb-1-8-1-on-ubuntu-10-04-lts
title: Installing MongoDB 1.8.1 on Ubuntu 10.04 LTS
-->



A lot of this information will seem obvious to a lot of you. But it did
require me to perform a few Google searches, so hopefully I can save you
the trouble.

I provide a script to automate this process:
[](https://github.com/jprichardson/mongo_install "https://github.com/jprichardson/mongo_install")

This script is deprecated now. Fortunately 10Gen (the company who makes
MongoDB) provides an Apt repository. Do not use the current MongoDB
version in the Ubuntu apt sources. It's version 1.6.\*

All you need to do: ` vim /etc/apt/sources.list`

Add the following line: deb
http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen

` apt-get update apt-get install mongodb-10gen`

By default MongoDB runs in [trusted
mode](http://www.mongodb.org/display/DOCS/Security+and+Authentication).
This means that it's listening on port 27017 for any connection. We want
it to only listen on localhost.

Edit the file: /etc/mongodb.conf

Add the following lines: bind\_ip 127.0.0.1 noauth = true \#\#\#\#\# YOU
MUST add this line if you use bind\_ip


