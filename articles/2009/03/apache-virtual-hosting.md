<!--
author: JP Richardson
publish: Tue Mar 10 2009 05:43:55 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2009/03/10/apache-virtual-hosting/
tags: Linux
slug: 2009/03/10/apache-virtual-hosting
title: Apache Virtual Hosting
-->


This is extremely easy to do.  This was done on Ubuntu 8.04.  Replace
"SERVER_IP" with the IP address of your  server.

Edit /etc/apache2/apache2.conf and add the following:

    NameVirtualHost SERVER_IP:80

Create a new file "subdomain" in /etc/apache2/sites-available with the
content:

    <VirtualHost SERVER_IP:80>
      ServerName subdomain.yourdomain.com 
      DocumentRoot /var/www/subdomain
      <Directory /var/www/subdomain> Order allow,deny Allow from all \</Directory>
   </VirtualHost>

Then run: 

    a2ensite subdomain 

then:

    /etc/init.d/apache2 restart
