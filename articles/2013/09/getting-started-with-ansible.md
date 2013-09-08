<!--
title: Getting Started with Ansible
publish: 2013-09-07
tags: Linux, Server Management
-->

If you have to manage more than one server with common software packages and configuration, you're going to want a server configuration management tool. Originally, I wrote shell scripts to handle this. However, shell scripts can have problems with error handling. Another benefit of most configuration management tools is that their operations are [idempotent](http://en.wikipedia.org/wiki/Idempotence) i.e. you can run an operation more than once and any additional operation won't have any effect. 

For the last few months I've read tutorials on [Puppet](http://en.wikipedia.org/wiki/Puppet_(software)), [Chef](http://en.wikipedia.org/wiki/Chef_(software)), and [Salt](http://en.wikipedia.org/wiki/Salt_(software)). I took a liking to Chef, especially the Chef tool [Knife Solo](https://github.com/matschaffer/knife-solo). I finally discovered [Ansible](http://en.wikipedia.org/wiki/Ansible_(software)) in the comments of a [Hacker News](http://news.ycombinator.com) post about Salt.

Ansible's primary benefit is that of simplicity and not requiring any software to be installed on the target machine. All you need is SSH enabled. I have two Digital Ocean VPS that I need to installed software on and configure.



Assumptions
-----------

You're running Mac OS X (should work with Linux) and you have Git and Python installed.



Installing Ansible
------------------

checkout latest development branch of Ansible...

    cd /tmp
    git clone git://github.com/ansible/ansible.git
    cd ./ansible
  

if you want a specifi version, you can check that out too...

    git tag

outputs:

    ...

    0.8
    v0.9
    v1.0
    v1.1
    v1.2
    v1.2.1
    v1.2.2
    v1.2.3

to checkout a specific version:

    git checkout v1.2.3

or, as stated, if you want the latest, don't call `git checkout`

    sudo make install

according to this, you need to install some python ansible dependencies

    sudo easy_install jinja2 
    sudo easy_install pyyaml
    sudo easy_install paramiko



Configuring to Communicate with Servers
---------------------------------------