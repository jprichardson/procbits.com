<!--
author: JP Richardson
publish: Wed Apr 08 2009 01:45:26 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2009/04/07/backup-a-mysql-database-on-ubuntu/
tags: Linux, MySql
slug: 2009/04/07/backup-a-mysql-database-on-ubuntu
title: Backup a MySQL Database on Ubuntu
-->



Backing up a MySQL database is extremely simple. Just simply create a
directory as to where you want to store your backups. I typically just
dump them in /var/dbbak. You can then run the following command:
``  /usr/bin/mysqldump -u root -p YOUR_DB_NAME | gzip > /root/dbbak/YOUR_DB_NAME_`date +%y_%m_%d`.gz ``


