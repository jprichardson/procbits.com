<!--
author: JP Richardson
publish: Tue Mar 10 2009 05:50:10 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2009/03/10/deploying-a-rails-application-on-ubuntu-804-lts-with-phusion-passenger-and-sql-server-2005/
tags: Linux, Rails, Ruby, SQL
slug: 2009/03/10/deploying-a-rails-application-on-ubuntu-804-lts-with-phusion-passenger-and-sql-server-2005
title: Deploying a Rails application on Ubuntu 8.04 LTS with Phusion Passenger and SQL Server 2005
-->



This article will provide step by step instructions for preparing and
then deploying a Rails application on Ubuntu 8.04 LTS that uses
Microsoft SQL Server 2005.


**Install Ruby** 

    sudo apt-get install ruby-full build-essential


**Install Apache 2**

    sudo apt-get install apache2 apache2-mpm-prefork apache2-prefork-dev


**Install Ruby Gems** 

Do not install Ruby Gems from Apt.  This will
screw up your Gems repository as Apt and Gems will both want to manage
it.  Download the latest Gems package from [http://rubyforge.org](http://rubyforge.org/).  At the time of this writing the latest version is 1.3.  Version 1.3 is also the version that
I used. 

    wget http://rubyforge.org/frs/download.php/43985/rubygems-1.3.0.tgz 
    tar xzvf rubygems-1.3.0.tgz 
    cd rubygems-1.3.0 
    sudo ruby setup.rb 
    sudo ln -s /usr/bin/gem1.8 /usr/bin/gem sudo gem update --system



**Install Rails** 

Make sure to install the proper version.  You may need to pass the "--version" flag. 

    sudo gem install rails


**Install Phusion Passenger** 

This allows us to deploy Rails application with as much simplicity as it takes to deploy a PHP application.  This we can avoid using Mongrel/Proxy
Balancing/(Apache|Lighttpd|Nginx).

    sudo gem install passenger sudo passenger-install-apache2-module


Next add the following to your `/etc/apache2/apache2.conf` file:


    LoadModule passenger_module /usr/lib/ruby/gems/1.8/gems/passenger-2.0.3/ext/apache2/mod_passenger.so PassengerRoot /usr/lib/ruby/gems/1.8/gems/passenger-2.0.3 PassengerRuby /usr/bin/ruby1.8`


**Install Ruby/ODBC/SQLServer2005 Compatibility** 

In /etc/profile add the following:

    export ODBCINI=/etc/odbc.ini export ODBCSYSINI=/etc export FREETDSCONF=/etc/freetds/freetds.conf

Make sure you restart your shell after this!

Now install UnixODBC:

    sudo apt-get install unixodbc 
    sudo apt-get install unixodbc-dev 
    sudo apt-get install tdsodbc


Now install FreeTDS (stable) from source. The FreeTDS in the Apt repository is version 0.63 and will not work with SQL Server 2005. At
the time of this writing, the current stable version is
0.82.

    wget ftp://ftp.ibiblio.org/pub/Linux/ALPHA/freetds/stable/freetds-stable.tgz 
    cd freetds-stable 
    ./configure 
    make sudo make install

Edit /etc/freetds/freetds.conf and add the following:` [YOUR_DB_DEFINITION_NAME] host = 172.24.40.100 port = 1433 tds version = 8.0`

Test it with the
following:

    sqsh -S YOUR_DB_DEFINITION_NAME -U USERNAME -P PASSWORD

Once actually logged in, you can run some SQL and verify the results.
Make sure to type “go” after each command and also make sure to “use”
the right database.

Now edit /etc/odbc.ini and add the following:

    [YOUR_DB_DEFINITION_NAME] 
    Driver = FreeTDS Description     
    = ODBC connection via FreeTDS Trace           
    = No Servername      
    = YOUR_DB_DEFINITION_NAME Database        
    = YOUR_ACTUAL_DB_NAME

Edit /etc/odbcinst.ini and add the following:

    [FreeTDS] Description     = TDS driver (Sybase/Microsoft SQL) Driver          = /usr/lib/odbc/libtdsodbc.so Setup           = /usr/lib/odbc/libtdsS.so CPTimeout       = CPReuse         = FileUsage       = 1


Now test it….

    isql YOUR_DB_DEFINITION_NAME USERNAME PASSWORD

Install Ruby ODBC:

    wget http://www.ch-werner.de/rubyodbc/ruby-odbc-0.9995.tar.gz 
    tar zvxf ruby-odbc-0.9995.tar.gz 
    cd ruby-odbc-0.9995/ 
    ruby extconf.rb 
    make 
    sudo make install

Install ActiveRecord ODBC Adapter

    sudo gem install activerecord-odbc-adapter

Edit your database.yml in your application and configure it
like…

    development: adapter: odbc dsn: YOUR_DB_DEFINITION_NAME username: USERNAME password: PASSWORD
    

Now dump your Rails app in /var/www/yourrailsapp and add a new Apache
Virtual Host as seen in the previous post. Configure the virtual host
DocumentRoot to point to the public directory in Apache2.  Do not forget
to change ownership of your rails app to www-data. (chown -R
www-data:www-data yourrailsapp) That’s all there is too it! Now you can
deploy a number of Rails apps in Apache with little trouble!




