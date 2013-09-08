<!--
title: Getting Started with Ansible
publish: 2013-09-07
tags: Linux, Server Management
-->

If you have to manage more than one server with common software packages and configuration, you're going to want a server configuration management tool. Originally, I wrote shell scripts to handle this. However, shell scripts can have problems with error handling. Another benefit of most configuration management tools is that their operations are [idempotent](http://en.wikipedia.org/wiki/Idempotence) i.e. you can run an operation more than once and any additional operation won't have any effect. 

For the last few months I've read tutorials on [Puppet](http://en.wikipedia.org/wiki/Puppet_(software)), [Chef](http://en.wikipedia.org/wiki/Chef_(software)), and [Salt](http://en.wikipedia.org/wiki/Salt_(software)). I took a liking to Chef, especially the Chef tool [Knife Solo](https://github.com/matschaffer/knife-solo). I finally discovered [Ansible](http://en.wikipedia.org/wiki/Ansible_(software)) in the comments of a [Hacker News](http://news.ycombinator.com) post about Salt.

Ansible's primary benefit is that of simplicity and not requiring any software to be installed on the target machine. All you need is SSH enabled. I have two [Digital Ocean][do] VPS that I need to installed software on and configure.



Assumptions
-----------

You're running Mac OS X (should work with Linux) and you have Git and Python installed. These instructions are specific to [Digital Ocean][do] but should work with any remote servers offered by any vendor.



Installing Ansible
------------------

checkout latest development branch of Ansible:

    cd /tmp
    git clone git://github.com/ansible/ansible.git
    cd ./ansible
  

if you want a specifi version, you can check that out too:

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

according to this, you need to install some python ansible dependencies:

    sudo easy_install jinja2 
    sudo easy_install pyyaml
    sudo easy_install paramiko



Configuring Ansible to Communicate with Servers
-----------------------------------------------

Let's assume that you have two servers named with IPs `1.2.3.4` and `4.3.2.1`. With Digital Ocean, you're inscurely emailed the root password to each server. Let's add our SSH key to each server.


### SSH Keys

if you don't have an SSH key, you can generate one really easily:

    ssh-keygen -t rsa -C "your_email@somedomain.com"

if you're running Linux, you can use `ssh-copy-id` to copy the key the remote servers:

    ssh-copy-id -i ~/.ssh/id_rsa.pub 1.2.3.4
    ssh-copy-id -i ~/.ssh/id_rsa.pub 4.3.2.1

if you're running on OS X, you won't have `ssh-copy-id`, here is an alternative:

    cat ~/.ssh/id_rsa.pub | ssh root@1.2.3.4 "mkdir ~/.ssh; cat >> ~/.ssh/authorized_keys"
    cat ~/.ssh/id_rsa.pub | ssh root@4.3.2.1 "mkdir ~/.ssh; cat >> ~/.ssh/authorized_keys"

if you get an error about the `.ssh` directory existing, modify the previous to this:

    cat ~/.ssh/id_rsa.pub | ssh root@1.2.3.4 "cat >> ~/.ssh/authorized_keys"
    cat ~/.ssh/id_rsa.pub | ssh root@4.3.2.1 "cat >> ~/.ssh/authorized_keys" 


### Ansible Hosts File

You must create a file that tells Ansible 




[do]: https://www.digitalocean.com/?refcode=a65fd89c7fd0
